## Android定位信息

### 主要实现方法
- onGpsStatusChanged：gps状态改变
- onLocationChanged：定位信息改变时


启动定位：
```
    @SuppressLint("MissingPermission")
    public void startGps() {
        Criteria criteria = new Criteria();//
        criteria.setAccuracy(Criteria.ACCURACY_COARSE);//设置定位精准度
        criteria.setSpeedRequired(true);//是否要求速度
        criteria.setPowerRequirement(Criteria.POWER_LOW);
        criteria.setSpeedAccuracy(Criteria.ACCURACY_HIGH);//设置速度精确度
        criteria.setHorizontalAccuracy(Criteria.ACCURACY_HIGH);//设置水平方向精确度
        provider = locationManager.getBestProvider(criteria, true);
        locationManager.requestLocationUpdates(provider, 2000, 0, this);
        locationManager.addGpsStatusListener(this);
        prevGpsLocation = new Location(provider);
        currentGpsLocation = new Location(provider);
    }
```

### 示例代码
```

public class GPSLocation implements GpsStatus.Listener, LocationListener {
    private static GPSLocation gpsLocation;
    private final LocationManager locationManager;
    private final Context context;
    private final String TAG = getClass().getSimpleName();
    private final int gpscount = 0;
    int rawlat = 0;
    int rawlon = 0;
    private String provider;
    private volatile Location prevGpsLocation = null, currentGpsLocation;
    private volatile double milestone = 0.00d;
    private final UpdateLocationListener gpsListener = new UpdateLocationListener() {
        @RequiresApi(api = Build.VERSION_CODES.O)
        @Override
        public void updateLocation(Location location) {
            MMKV kv = MMKVService.getKV();
            if(kv.decodeBool(MMKVService.POWER_SWITCH,true)){
                String  time=DateFormatUtils.format(new Date(),"yyyyMMddHHmmss");
//                Log.d(TAG,"power off:"+time);
                kv.putString(MMKVService.POWER_OFF, time);
            }

//            Log.d(TAG,"速度：lgn="+location.getLongitude()+",lat="+location.getLatitude());
            //未定位
            if (location == null || location.getLongitude() == 0d || location.getLatitude() == 0d) {
                MyLog.e(TAG, "未定位");
                return;
            }
//            MyLog.d(TAG, "gps："+location.getTime());
            //初始地理原点
            currentGpsLocation = location;
            if (prevGpsLocation == null || prevGpsLocation.getLongitude() == 0d || prevGpsLocation.getLatitude() == 0d) {
                prevGpsLocation = currentGpsLocation;
                return;
            }

            //飘逸
            double mile = prevGpsLocation.distanceTo(currentGpsLocation);//移动距离
            if (hasMoved(mile)) {
//                BigDecimal bdMile=new BigDecimal(mile);
//                BigDecimal bdSecond=new BigDecimal((currentGpsLocation.getTime()-prevGpsLocation.getTime())/1000);
//                BigDecimal speed=bdMile.divide(bdSecond,1,BigDecimal.ROUND_FLOOR);

                kv.putFloat(MMKVService.MAX_SPEED,currentGpsLocation.getSpeed()*3.6f*100f);


//                MyLog.e(TAG,"移动距离mile="+mile+"，speed="+currentGpsLocation.getSpeed()+",km/h="+currentGpsLocation.getSpeed()*3.6
//                );
                //弧度变化
                DeviceCfgInformation deviceCfgInformation = MyApplication.getDeviceCfgInfo();

                if (null != deviceCfgInformation && null != deviceCfgInformation.getMileageRate() && deviceCfgInformation.getMileageRate() > 1) {
                    milestone += mile * deviceCfgInformation.getMileageRate();
                } else {
                    milestone += mile;
                }

                prevGpsLocation = currentGpsLocation;
            } else {
                //超过1分钟，强制更新坐标
                if (currentGpsLocation.getTime() - prevGpsLocation.getTime() > 10 * 1000) {
                    prevGpsLocation = currentGpsLocation;
                }
            }
        }
    };
    private int preLatCount = 4;//默认不使用缓存的position
    private int preLonCount = 4;//默认不使用缓存的position

    public GPSLocation(Context context) {
        this.context = context;
        locationManager = (LocationManager) context.getSystemService(Context.LOCATION_SERVICE);
        if (!openGps() && !openNetWork()) {
            MyLog.e(TAG, "gps未打开");
            return;
        }
        startGps();
    }

    public static GPSLocation getGpsManager(Context context) {
        if (gpsLocation == null)
            gpsLocation = new GPSLocation(context);
        return gpsLocation;
    }

    @SuppressLint("MissingPermission")
    public void startGps() {
        Criteria criteria = new Criteria();//
        criteria.setAccuracy(Criteria.ACCURACY_COARSE);//设置定位精准度
        criteria.setSpeedRequired(true);//是否要求速度
        criteria.setPowerRequirement(Criteria.POWER_LOW);
        criteria.setSpeedAccuracy(Criteria.ACCURACY_HIGH);//设置速度精确度
        criteria.setHorizontalAccuracy(Criteria.ACCURACY_HIGH);//设置水平方向精确度
        provider = locationManager.getBestProvider(criteria, true);
        locationManager.requestLocationUpdates(provider, 2000, 0, this);
        locationManager.addGpsStatusListener(this);
        prevGpsLocation = new Location(provider);
        currentGpsLocation = new Location(provider);
    }

    @Override
    public void onGpsStatusChanged(int event) {
        @SuppressLint("MissingPermission") GpsStatus status = locationManager.getGpsStatus(null);
        if (event == GpsStatus.GPS_EVENT_SATELLITE_STATUS) {
            int maxSatellites = status.getMaxSatellites();
            Iterator<GpsSatellite> it = status.getSatellites().iterator();
            int count = 0;
            while (it.hasNext() && count <= maxSatellites) {
                GpsSatellite s = it.next();
                if (s.getSnr() > 20) {
                    count++;
                }
            }

        }
    }

    @RequiresApi(api = Build.VERSION_CODES.O)
    @Override
    public void onLocationChanged(Location location) {
        gpsListener.updateLocation(location);
    }

    @Override
    public void onStatusChanged(String s, int i, Bundle bundle) {

    }

    @Override
    public void onProviderEnabled(String s) {

    }

    @Override
    public void onProviderDisabled(String s) {

    }

    public void usePrePosition() {
        preLatCount = 0;
        preLonCount = 0;
    }

    public void restartGps() {
        endDestroy();
        startGps();
    }

    public Double getLatitude() {
//        return 0.29;
        try {
            if (currentGpsLocation.getLatitude() == 0 && prevGpsLocation.getLatitude() > 0 && preLatCount < 4) {
                preLatCount++;
                //MyLog.i(TAG, "使用pre地址");
                return prevGpsLocation.getLatitude();
            } else {
                // MyLog.i(TAG, "使用原有地址");
                return currentGpsLocation.getLatitude();
            }
        } catch (Exception e) {
            MyLog.e(TAG, "gps出现异常:", e);
            return currentGpsLocation == null ? 0.0d : currentGpsLocation.getLatitude();
        }
    }

    public Double getLongitude() {
//        return 0.134;
        try {
            if (currentGpsLocation.getLongitude() == 0 && prevGpsLocation.getLongitude() > 0 && preLonCount < 4) {
                preLonCount++;
                MyLog.i(TAG, "使用pre地址");
                return prevGpsLocation.getLongitude();
            } else {
                //MyLog.i(TAG, "使用原有地址");
                return currentGpsLocation.getLongitude();
            }
        } catch (Exception e) {
            MyLog.e(TAG, "gps出现异常:", e);
            return currentGpsLocation == null ? 0.0d : currentGpsLocation.getLongitude();
        }
    }

    public double getMilestone() {//获取里程
        return milestone;
    }

    public void initMilestone() {
        if (milestone < 10) return;//解决里程不足100时，上传为0的问题
        MyLog.e(TAG, "分钟里程归零");
        milestone = 0;
    }

    public float getSpeed() {
        try {
//            double temp = Math.ceil(new Double(currentGpsLocation.getSpeed()));
            double temp = currentGpsLocation.getSpeed();
//            Log.e(TAG,"速度值temp="+temp*3.6 +",原始="+currentGpsLocation.getSpeed()*3.6+"，角度："+currentGpsLocation.getBearing()+"，gps="+currentGpsLocation.getLatitude()+","+currentGpsLocation.getLongitude());
            return ((float) temp);
        } catch (Exception e) {
            MyLog.e(TAG,  Log.getStackTraceString(e));
            return 0f;
        }
    }

    public GNSS getGPSInfo() {
        GNSS gnss = new GNSS();

        int lat = (int) (getLatitude() * 1000000);
        int lon = (int) (getLongitude() * 1000000);
        short speed = (short) (getSpeed() * 3.6 * 100);

        if (lat == 0) {
            gnss.setLat(rawlat);
        } else {
            rawlat = lat;
            gnss.setLat(lat);
        }
        if (lon == 0) {
            gnss.setLng(rawlon);
        } else {
            rawlon = lon;
            gnss.setLng(lon);
        }

        gnss.setSpeedCar(speed);
        gnss.setSpeedGPS(speed);
        gnss.setAlarm("0");
        StringBuffer stars = new StringBuffer();
        stars = stars
                .append("1")//ACC开
                .append("1")//定位
                .append("0")//北纬
                .append("0")//东经
                .append("0")//运营状态
                .append("0")//经纬度未经保密插件加密
                .append("0")//正常位置汇报
                .append("000")//保留
                .append("0")//车辆油路正常
                .append("0")//车辆电路正常
                .append("1")//车门加锁
                .append("0000000000000000000")//保留
                .reverse()
        ;
        gnss.setStatus(stars.toString());
        try {
            gnss.setAngle((short) getDir());

        } catch (Exception e) {
            MyLog.e(TAG,  Log.getStackTraceString(e));
        }
        gnss.setTimestamp(DateUtils.getDateTime());
        gnss.setExtendInfo(ExtendGNSS.LocationAddinfo());
        return gnss;

    }

    public int getDir() {
        return (int) currentGpsLocation.getBearing();
    }

    private boolean hasMoved(double distance) {
        return distance > 0.01 && distance <= 200;
    }

    private boolean openGps() {
        return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
    }

    private boolean openNetWork() {
        return locationManager
                .isProviderEnabled(LocationManager.NETWORK_PROVIDER);
    }

    public void endDestroy() {
        locationManager.removeGpsStatusListener(this);
        locationManager.removeUpdates(this);
    }

    public interface UpdateLocationListener {
        void updateLocation(Location location);
    }
}
```