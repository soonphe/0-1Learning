# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## IP工具类
JDK中提供了有一个包专门提供了对网络的接入处理
```
package java.net;
```
getName：获取网络名称
getInetAddresses：获取IP地址
。。。

### 代码实现
```
/**
 * 系统信息工具类
 */
public final class IPUtil {

    /**
     * 取到当前机器的IP地址
     */
    public static String getIp() {
        String hostIp;
        List<String> ips = new ArrayList<>();
        Enumeration<NetworkInterface> netInterfaces;
        try {
            netInterfaces = NetworkInterface.getNetworkInterfaces();
            while (netInterfaces.hasMoreElements()) {
                NetworkInterface netInterface = netInterfaces.nextElement();
                Enumeration<InetAddress> inteAddresses = netInterface.getInetAddresses();
                while (inteAddresses.hasMoreElements()) {
                    InetAddress inetAddress = inteAddresses.nextElement();
                    if (!inetAddress.isLoopbackAddress() && inetAddress instanceof Inet4Address) {
                        ips.add(inetAddress.getHostAddress());
                    }
                }
            }
        } catch (SocketException ex) {
            ex.printStackTrace();
        }
        hostIp = collectionToDelimitedString(ips, ",");
        return hostIp;
    }

    private static String collectionToDelimitedString(Collection<String> coll, String delim) {
        if (coll == null || coll.isEmpty()) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        Iterator<?> it = coll.iterator();
        while (it.hasNext()) {
            sb.append(it.next());
            if (it.hasNext()) {
                sb.append(delim);
            }
        }
        return sb.toString();
    }

    /**
     * 获取服务器名称
     */
    public static String getHostName() {
        String hostName = null;
        try {
            hostName = InetAddress.getLocalHost().getHostName();
        } catch (Exception e) {
            logger.warn(e.getMessage(), e);
        }
        return hostName;
    }
}
```
