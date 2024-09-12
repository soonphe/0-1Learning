# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-Serialport

### Android-Serialport 目录结构 
Android-Serialport 项目的目录结构如下：
```
Android-Serialport/
├── app/        主应用程序模块，包含主要的业务逻辑和界面
│   ├── build/  构建生成的文件
│   ├── libs/   存放第三方库文件
│   ├── src/    源代码目录
│   │   ├── androidTest/    存放Android测试代码
│   │   ├── main/           主代码目录
│   │   │   ├── java/       Java源代码目录
│   │   │   │   └── com/
│   │   │   │       └── serialport/
│   │   │   │           ├── SerialPort.java         串口操作的核心类
│   │   │   │           ├── SerialPortFinder.java   串口查找类
│   │   │   │           └── SerialPortManager.java  串口管理类
│   │   │   ├── res/                                资源文件目录
│   │   │   └── AndroidManifest.xml                 应用程序配置文件
│   │   └── test/                                   存放单元测试代码
│   ├── build.gradle                                模块构建配置文件
│   └── proguard-rules.pro                          ProGuard 配置文件
├── serialport/                                     串口库模块，包含串口操作的JNI代码
│   ├── src/
│   │   ├── main/
│   │   │   ├── cpp/                                C++源代码目录
│   │   │   │   ├── SerialPort.c                    串口操作的C代码
│   │   │   │   └── SerialPort.h                    串口操作的头文件
│   │   │   └── Android.mk
│   ├── build.gradle
│   └── proguard-rules.pro
├── gradle/
├── gradle.properties
├── gradlew
├── gradlew.bat
├── build.gradle
├── settings.gradle
└── README.md
```
项目的启动文件主要位于 app/src/main/java/com/serialport/ 目录下，其中 SerialPort.java 是串口操作的核心类，负责打开和关闭串口，以及读写数据。

### 集成
依赖
```
    // 串口
    implementation 'com.licheedev:android-serialport:2.1.2'
```

打开串口：
```
    // 创建并打开串口... 配置串口参数，如路径、波特率等 ...     
    mSerialPort = new SerialPort(new File("/dev/ttyS0"), 9600, 0); 
    //后面进行读写操作...
```

复杂一点的串口创建方式：
```
    private static final String TAG = "串口处理类";
    private String path = "";//串口地址
    private SerialPort mSerialPort;//串口对象
    private InputStream mInputStream;//串口的输入流对象
    private BufferedInputStream mBuffInputStream;//用于监听硬件返回的信息
    private OutputStream mOutputStream;//串口的输出流对象 用于发送指令
    private SerialInter serialInter;//串口回调接口
    private ScheduledFuture readTask;//串口读取任务
    
    /**
     * 打开串口
     *
     * @param devicePath 串口地址(根据平板的说明说填写)
     * @param baudrate   波特率(根据对接的硬件填写 - 硬件说明书上"通讯"中会有标注)
     * @param dataBits   数据位(根据对接的硬件填写 - 硬件说明书上"通讯"中会有标注)
     * @param stopBits   停止位(根据对接的硬件填写 - 硬件说明书上"通讯"中会有标注)
     * @param parity     校验位(根据对接的硬件填写 - 硬件说明书上"通讯"中会有标注)
     * @param isRead     是否持续监听串口返回的数据
     * @return 是否打开成功
     */
    public boolean open(String devicePath, int baudrate, int dataBits, int stopBits, int parity, boolean isRead) {
        boolean isSucc = false;
        try {
            if (mSerialPort != null) close();
            File device = new File(devicePath);
            mSerialPort = SerialPort // 串口对象
                    .newBuilder(device, baudrate) // 串口地址地址，波特率
                    .dataBits(dataBits) // 数据位,默认8；可选值为5~8
                    .stopBits(stopBits) // 停止位，默认1；1:1位停止位；2:2位停止位
                    .parity(parity) // 校验位；0:无校验位(NONE，默认)；1:奇校验位(ODD);2:偶校验位(EVEN)
                    .build(); // 打开串口并返回
            mInputStream = mSerialPort.getInputStream();
            mBuffInputStream = new BufferedInputStream(mInputStream);
            mOutputStream = mSerialPort.getOutputStream();
            isSucc = true;
            path = devicePath;
            if (isRead) readData();//开启识别
        } catch (Throwable tr) {
            close();
            isSucc = false;
        } finally {
            return isSucc;
        }
    }
    
```

### 示例代码
```
public class SerialPortUtils {
    private static final String TAG = "SerialPortUtils";
    private static class InstanceHolder {
        public static SerialPortUtils sUtils = new SerialPortUtils();
    }
    private SerialPortUtils() {
    }
    public static SerialPortUtils instance() {
        return SerialPortUtils.InstanceHolder.sUtils;
    }

    private SerialPort mSerialPort;
    private BufferedInputStream mInputStream;
    private OutputStream mOutputStream;

    private byte[] addCRC16(byte[] bytes) {
        byte[] crc16 = CRC16.calculateCRC16(bytes);

        byte[] data = new byte[bytes.length + crc16.length];
        System.arraycopy(bytes, 0, data, 0, bytes.length);
        System.arraycopy(crc16, 0, data, bytes.length, crc16.length);

        return data;
    }

    public Observable<Boolean> checkElectronicTag(String electronicTag, Boolean isBind) {
        if (electronicTag.length() < 15) {
            electronicTag = org.apache.commons.lang3.StringUtils.leftPad(electronicTag, 15, "0");
        }
        String finalElectronicTag = electronicTag;
        return Observable.create(emitter -> {
            try {
                File device = new File("/dev/ttyS1");
                mSerialPort = new SerialPort(device, 9600);
                mInputStream = new BufferedInputStream(mSerialPort.getInputStream());
                mOutputStream = mSerialPort.getOutputStream();

                byte[] cardNo = findCard();
                if (cardNo != null) {
                    MyLog.e(TAG, "读到电子标签：" + ConvertUtil.bytes2HexStr(cardNo));
                    String tagNo = readTag(cardNo);
                    if (tagNo != null) {
                        MyLog.e(TAG, "读到电子标签数据：" + tagNo);
                        emitter.onNext(finalElectronicTag.equals(tagNo));
                    } else {
                        if (isBind) {
                            MyLog.e(TAG, "读取到空白卡,但是设备写卡状态为1");
                            emitter.onNext(false);
                        } else {
                            MyLog.e(TAG, "读取到空白卡,开始写卡");
                            boolean ret = writeCard(cardNo, finalElectronicTag);
                            if (ret) {
                                // 再次读卡验证
                                tagNo = readTag(cardNo);
                                if (tagNo != null) {
                                    MyLog.e(TAG, "读到电子标签数据：" + tagNo);
                                    emitter.onNext(finalElectronicTag.equals(tagNo));
                                } else {
                                    emitter.onNext(false);
                                }
                            } else {
                                emitter.onNext(false);
                            }
                        }
                    }
                } else {
                    emitter.onNext(false);
                }
                emitter.onComplete();
            } catch (Throwable tr) {
                MyLog.e(TAG, "打开串口失败", tr);
                emitter.onNext(false);
                emitter.onError(tr);
            } finally {
                close();
            }
        });
    }

    private byte[] findCard() {
        try {
            // 寻卡
            byte[] bytes = addCRC16(ConvertUtil.hexStr2bytes("2424000711"));
            mOutputStream.write(bytes);

            byte[] received = new byte[1024];
            byte[] cardNo = new byte[4];
            long startTime = System.currentTimeMillis();
            while (!Thread.currentThread().isInterrupted()) {
                int available = mInputStream.available();
                if (available > 0) {
                    int size = mInputStream.read(received);
                    if (size > 10) {
                        if (received[4] == (byte) 0x81) {
                            if (received[5] == (byte) 0x02) {
                                System.arraycopy(received, 7, cardNo, 0, cardNo.length);
                                return cardNo;
                            }
                        }
                        break;
                    }
                } else {
                    // 暂停一点时间，免得一直循环造成CPU占用率过高
                    SystemClock.sleep(1);
                }
                // 3秒超时
                if (System.currentTimeMillis() - startTime > 3000) {
                    break;
                }
            }
        } catch (Exception e) {
            MyLog.e(TAG, "寻卡失败");
        }
        return null;
    }

    private String readTag(byte[] cardNo) {
        try {
            byte[] bytes = ConvertUtil.hexStr2bytes("242400141201FFFFFFFFFFFF000000000101");
            System.arraycopy(cardNo, 0, bytes, 12, 4);
            bytes = addCRC16(bytes);
            mOutputStream.write(bytes);
            // 获取数据
            byte[] received = new byte[1024];
            byte[] eTagBytes = new byte[15];
            long startTime = System.currentTimeMillis();
            while (!Thread.currentThread().isInterrupted()) {
                int available = mInputStream.available();
                if (available > 0) {
                    int size = mInputStream.read(received);
                    if (size > 10) {
                        if (received[4] == (byte) 0x82) {
                            if (received[5] == (byte) 0x05) {
                                if (received[8] == (byte) 0x01) {
                                    System.arraycopy(received, 9, eTagBytes, 0, eTagBytes.length);
                                    MyLog.e(TAG, "读到电子标签数据：" + ConvertUtil.bytes2HexStr(eTagBytes));
                                    return new String(eTagBytes);
                                }
                            }
                        }
                        break;
                    }
                } else {
                    // 暂停一点时间，免得一直循环造成CPU占用率过高
                    SystemClock.sleep(1);
                }
                // 3秒超时
                if (System.currentTimeMillis() - startTime > 3000) {
                    break;
                }
            }
        } catch (Exception e) {
            MyLog.e(TAG, "读取电子标签数据失败");
        }
        return null;
    }

    private boolean writeCard(byte[] cardNo, String tagNo) {
        try {
            byte[] bytes = ConvertUtil.hexStr2bytes("242400241302FFFFFFFFFFFF00000000010101000000000000000000000000000000");
            System.arraycopy(cardNo, 0, bytes, 12, 4);
            byte[] eTagBytes = tagNo.getBytes();
            System.arraycopy(eTagBytes, 0, bytes, 19, eTagBytes.length);
            bytes = addCRC16(bytes);
            mOutputStream.write(bytes);
            return true;
        } catch (Exception e) {
            MyLog.e(TAG, "写卡失败");
        }
        return false;
    }

    public void close() {
        if (mInputStream != null) {
            try {
                mInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (mOutputStream != null) {
            try {
                mOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (mSerialPort != null) {
            mSerialPort.close();
            mSerialPort = null;
        }
    }
}
```

