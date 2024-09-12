# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-GPS定位信息

### 基础使用
权限：
```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" /
```

### 继承GpsStatus.Listener, LocationListener
> public class GPSLocation implements GpsStatus.Listener, LocationListener {

- onGpsStatusChanged：gps状态改变
- onLocationChanged：定位信息改变时

启动方式：
```
    //检查设备是否具有GPS硬件：‌可以通过检查设备是否具有LocationManager服务来实现。‌如果设备支持GPS，‌那么就可以进行后续的操作。‌
    public GPSLocation(Context context) {
        this.context = context;
        //获取位置管理器（‌LocationManager）‌实例：‌通过调用getSystemService(Context.LOCATION_SERVICE)方法获取LocationManager实例，‌以便进行进一步的操作。‌
        locationManager = (LocationManager) context.getSystemService(Context.LOCATION_SERVICE);
        if (!openGps() && !openNetWork()) {
            MyLog.e(TAG, "gps未打开");
            return;
        }
        Criteria criteria = new Criteria();//
        criteria.setAccuracy(Criteria.ACCURACY_COARSE);//设置定位精准度
        criteria.setSpeedRequired(true);//是否要求速度
        criteria.setPowerRequirement(Criteria.POWER_LOW);//低功率
        criteria.setSpeedAccuracy(Criteria.ACCURACY_HIGH);//设置速度精确度
        criteria.setHorizontalAccuracy(Criteria.ACCURACY_HIGH);//设置水平方向精确度
        //检查是否有可用的GPS提供者：‌通过调用LocationManager的getProviders方法来检查是否有可用的GPS提供者。‌
        provider = locationManager.getBestProvider(criteria, true);
        locationManager.requestLocationUpdates(provider, 2000, 0, this);    //使用给定参数和回调来监听位置更新
        locationManager.addGpsStatusListener(this);     //添加gps状态监听
        prevGpsLocation = new Location(provider);       //前一个gps位置对象
        currentGpsLocation = new Location(provider);    //当前gps位置对象
    }    
    /**
     * 判断gps是否开启
     */    
    private boolean openGps() {
        return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
    }
    /**
     * 判断网络
     */
    private boolean openNetWork() {
        return locationManager
                .isProviderEnabled(LocationManager.NETWORK_PROVIDER);
    }
    /**
     * 销毁
     */
    public void endDestroy() {
        locationManager.removeGpsStatusListener(this);
        locationManager.removeUpdates(this);
    }
```

### 实现示例：
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

### 检测GPS信号
主要通过重载方法：onGpsStatusChanged(int event) 实现，event中的卫星数量和信噪比进行判断：

示例代码：
```
    @Override
    public void onGpsStatusChanged(int event) {
        switch (event) {
            case GpsStatus.GPS_EVENT_STARTED:
                Log.e("GpsSignalExample", "GPS started");
                break;
            case GpsStatus.GPS_EVENT_STOPPED:
                Log.e("GpsSignalExample", "GPS stopped");
                break;
            case GpsStatus.GPS_EVENT_FIRST_FIX:
                Log.e("GpsSignalExample", "Got first fix");
                break;
            case GpsStatus.GPS_EVENT_SATELLITE_STATUS:
                int count = 0;
                //当GPS状态变为GPS_EVENT_SATELLITE_STATUS时，我们遍历所有卫星并记录最大的信号强度（使用getSnr方法），然后输出最大信号强度。
                @SuppressLint("MissingPermission") GpsStatus gpsStatus = locationManager.getGpsStatus(null);
                int maxSatellites = gpsStatus.getMaxSatellites();
                Iterable<GpsSatellite> satellites = gpsStatus.getSatellites();
                int maxSnr = 0;
                for (GpsSatellite satellite : satellites) {
                    int snr = (int) satellite.getSnr(); // SNR in dB
                    if (snr > maxSnr) {
                        maxSnr = snr;
                    }
                    count++;
                }
                //判断卫星数量小于10并且最大信噪比小于maxSnrLimit则提示为信号弱
                if(count<10 && maxSnr < maxSnrLimit){
                    lastRemind++;
                    long currentTime = System.currentTimeMillis();
                    if (currentTime - lastClickTime > 1000 && lastRemind < 3) {
                        Log.e("GpsSignalExample", "Satellite status changed，maxSatellites："+ maxSatellites + "，satellites："+count+ ",Max SNR: " + maxSnr + " dB");
                        lastClickTime = currentTime;
                        voicePlayOperation("GPS定位信号弱");
                    }
                }else{
                    lastRemind = 0;
                }
                break;
        }
    }
```
常见的：
```
maxSatellites=255
satellites.size = 16
```

#### GPS SNR（‌信噪比） 
GPS SNR（‌信噪比）‌是衡量GPS信号强度的一个重要指标，‌通常用分贝（‌dB）‌表示。‌
SNR值越大，‌表示信号相对于噪声的强度越大，‌即信号越强。‌
典型的SNR值在0到50之间，‌其中50表示非常好的信号。‌
一般来说，‌在行业里，‌SNR值至少要40以上才算及格，‌能勉强保证在恶劣气候下20米以内的精准度。‌如果SNR值低于这个标准，‌可能会影响到GPS信号的接收和定位的准确性‌
GPS系统由24颗卫星组成，‌这些卫星分布在6个轨道上，‌每个轨道有4颗卫星

信号打印：
```
2024-08-13 09:58:35.350  4206-4206  GpsSignalExample        com.bd.train                         E  Satellite status changed
2024-08-13 09:58:35.350  4206-4206  GpsSignalExample        com.bd.train                         E  Max SNR: 35 dB
```
