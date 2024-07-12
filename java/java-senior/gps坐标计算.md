## gps坐标计算

参考代码：
```
package org.starry.utils.gps;

import java.math.BigDecimal;
import java.util.Arrays;
import java.util.Random;

import org.apache.commons.lang3.ArrayUtils;

import com.vividsolutions.jts.geom.Coordinate;
import com.vividsolutions.jts.geom.GeometryFactory;
import com.vividsolutions.jts.geom.Point;
import com.vividsolutions.jts.geom.Polygon;
import com.vividsolutions.jts.operation.distance.DistanceOp;

/**
 * 计算偏移距离
 *
 * @author soonphe
 * @since 1.0
 */
public class CalculateMinDistance {
	private static GeometryFactory geometryFactory = new GeometryFactory();
    //地球平均半径
    private static double EARTH_RADIUS = 6378.137;

    /**
     * 将gps坐标转换为弧度
     * @param d
     * @return
     */
    private static double rad(double d) {
        return d * Math.PI / 180.0;
    }

    /**
     * 计算GPS点的距离
     * @param one 经度,纬度
     * @param two 经度,纬度
     * @return 千米
     */
    public static double getDistance(String one,String two) {
        String[] t1=one.split(",");
        String[] t2=two.split(",");
        double lat1=Double.valueOf(t1[1]);
        double lng1=Double.valueOf(t1[0]);
        double lat2=Double.valueOf(t2[1]);
        double lng2=Double.valueOf(t2[0]);

        double radLat1 = rad(lat1);
        double radLat2 = rad(lat2);
        //计算纬度弧度差
        double a = radLat1 - radLat2;
        //计算经度弧度差
        double b = rad(lng1) - rad(lng2);
        /**
         * △ABC中,a²=b²+c²-2bc×cosA，即：cosA=(b²+c²-a²)÷2bc
         *
         * 计算顺序：
         * 1.根据经纬度，以及地球半径R，将A、B两点的经纬度坐标转换成球面坐标
         * 2.根据A、B两点的三维坐标求AB长度
         * 3.根据余弦定理求出角AOB
         * 4.于是得到AB弧长=R*角AOB
         */
        double s = 2 * Math.asin(
                Math.sqrt(
                        Math.pow(Math.sin(a / 2), 2)
                                + Math.cos(radLat1) * Math.cos(radLat2) * Math.pow(Math.sin(b / 2), 2)
                )
        );
        //实际距离：弧长=弧度*半径 弧长=圆心角*π*r/180  角度转换弧度公式：弧度=角度÷180×π
        s = s * EARTH_RADIUS;
        s = Math.round(s * 10000d) / 10000d;
        return s;
    }
    /**
	 * 计算坐标离坐标区域的偏移距离
	 * @param coordination gps坐标点(经度,纬度-偏移后)
	 * @param linearRing 坐标区域(纬度,经度)
	 * @return
	 */
	public static double getDis(String coordination ,String linearRing) {
		
		String[]  searchCoord = coordination.split(",");
		double lon = Double.valueOf(searchCoord[0]); 
		double lat = Double.valueOf(searchCoord[1]);
		
		String[] strRing = linearRing.replace("[[", "").replace("]]", "").split("\\],\\[");
		strRing=ArrayUtils.add(strRing, strRing[0]);
		Coordinate[] coords = new Coordinate[strRing.length];

		for (int i = 0; i < strRing.length; i++) {
			String[] coor = strRing[i].split(",");
			coords[i] = new Coordinate(Double.valueOf(coor[1]), Double.valueOf(coor[0]));
		}
		Polygon polygon = geometryFactory.createPolygon(coords);
		Point point = geometryFactory.createPoint(new Coordinate(lon, lat));
		//round double 
		double distance = polygon.distance(point);
		BigDecimal bd = new BigDecimal(distance);
		//13 after dot
		return bd.setScale(3, BigDecimal.ROUND_DOWN).doubleValue();
	}
	
	/**
	 * 区域最近距离点
	 * @param point 经度在前，纬度在后 
	 * @param linearRing  纬度在前，经度在后 [[,],[]]
	 * @return 经度,纬度
	 */
	 public static String getNearestPointCp(String point, String linearRing){
        String[] pointCp = point.split(",");
        Point pointGeo = geometryFactory.createPoint(new Coordinate(Double.valueOf(pointCp[0]),Double.valueOf(pointCp[1])));
        String[] linearRingCoord = linearRing.replace("[[", "").replace("]]", "").split("\\],\\[");
         linearRingCoord=ArrayUtils.add(linearRingCoord, linearRingCoord[0]);
        int length = linearRingCoord.length;
        
        Coordinate[] coordinates = new Coordinate[length];
        for (int i = 0 ;  i < length ; i ++){
            String[] coordCp = linearRingCoord[i].split(",");
            coordinates[i] = new Coordinate(Double.valueOf(coordCp[1]),Double.valueOf(coordCp[0]));
        }
        Polygon polygon = geometryFactory.createPolygon(coordinates);
        
        Coordinate[] nearestResult = DistanceOp.nearestPoints(pointGeo,polygon);
        Coordinate pointPolygon = nearestResult[1]; 
        
        return pointPolygon.y+","+pointPolygon.x;
    }
	 /**
	  *	 输入围栏外一个点，和围栏边界，返回围栏内靠近围栏距离最近点附近的随机点
	  * @param point 外部点，经度在前，纬度在后; 如: 114.1331926489299,30.480347536072795 
	  * @param linearRing 纬度在前，经度在后，;分割 如: 30.4792639386713,114.134032724334;30.479612341126845,114.13480549499748
	  * @param deta 随机中心点偏移距离
	  * @return	经度在前，纬度在后; 如：114.1331926489299,30.480347536072795 
	  */
	 public static String getRandomPoint(String point , String linearRing,double deta) {
	   //parse Polygon
        String[] linearRingCoord = linearRing.replace("[[", "").replace("]]", "").split("\\],\\[");
        int length = linearRingCoord.length;
        Coordinate[] coordinates = new Coordinate[length];
        for (int i = 0 ;  i < length ; i ++){
            String[] coordCp = linearRingCoord[i].split(",");
            coordinates[i] = new Coordinate(Double.valueOf(coordCp[1]),Double.valueOf(coordCp[0]));
        }
        Polygon polygon = geometryFactory.createPolygon(coordinates);
        //parse edge Point
        String[] pointCp = point.split(",");
        Coordinate outPoint = new Coordinate(Double.valueOf(pointCp[0]),Double.valueOf(pointCp[1]));
        Coordinate[] pointsRealted = DistanceOp.nearestPoints(geometryFactory.createPoint(outPoint), polygon);
        outPoint = pointsRealted[1];
        
        //pick random Point from a Square place
        //double deta=0.0006; // half of the square Edge length
        double x_min = outPoint.x - deta;
        double y_min = outPoint.y - deta;
        //the random ratio for pick point
        Random random = new Random();
        double ratioX = 0.0;
        double ratioY = 0.0;
        //pick random
        Point randomPoint = null;
        while(true){
            ratioX = random.nextDouble();
            ratioY = random.nextDouble();
            randomPoint = geometryFactory.createPoint(new Coordinate(x_min + 2*deta*ratioX,y_min+2*deta*ratioY));
            if(polygon.contains(randomPoint))
                break;
        }
        return randomPoint.getX()+ ","+randomPoint.getY();
	 }
}
```