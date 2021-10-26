# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Andorid NFC开发

1.NFC的工作模式
NFC支持如下3种工作模式：读卡器模式（Reader/writer mode）、仿真卡模式(Card Emulation Mode)、点对点模式（P2P mode）。

（1）读卡器模式
数据在NFC芯片中，可以简单理解成“刷标签”。本质上就是通过支持NFC的手机或其它电子设备从带有NFC芯片的标签、贴纸、名片等媒介中读写信息

（2）仿真卡模式
数据在支持NFC的手机或其它电子设备中，可以简单理解成“刷手机”。本质上就是将支持NFC的手机或其它电子设备当成借记卡、公交卡、门禁卡等IC卡使用

（3）点对点模式
该模式与蓝牙、红外差不多，用于不同NFC设备之间进行数据交换

一、NFC的配置总结
第一：屏幕没有锁住 。     第二：NFC功能已经在设置中打开
当系统检测到一个NFC标签的时候，他会自动去寻找最合适的activity去处理这个intent.
NFC发出的这个Intent将会有三种action:
ACTION_NDEF_DISCOVERED:当系统检测到tag中含有NDEF格式的数据时，且系统中有activity声明可以接受包含NDEF数据的Intent的时候，系统会优先发出这个action的intent。

ACTION_TECH_DISCOVERED：当没有任何一个activity声明自己可以响应ACTION_NDEF_DISCOVERED时，系统会尝试发出TECH的intent.即便你的tag中所包含的数据是NDEF的，但是如果这个数据的MIMEtype或URI不能和任何一个activity所声明的想吻合，系统也一样会尝试发出tech格式的intent，而不是NDEF.

ACTION_TAG_DISCOVERED:当系统发现前两个intent在系统中无人会接受的时候，就只好发这个默认的TAG类型的
——————————————————————————————————————————————————————————————
NDEF格式其实就类似于硬盘的NTFS，下面我们看一下NDEF数据：

NDEF数据被封装一个消息（NdefMessage）的内部，一个消息包含一个或多个的记录（NdefRecord）

（1）NDEF数据的操作

Android SDK API支持如下3种NDEF数据的操作：

1）从NFC标签读取NDEF格式的数据。
2）向NFC标签写入NDEF格式的数据。
3）通过Android Beam技术将NDEF数据发送到另一部NFC设备。

用于描述NDEF格式数据的两个类：

1）NdefMessage：描述NDEF格式的信息，实际上我们写入NFC标签的就是NdefMessage对象。
2）NdefRecord：描述NDEF信息的一个信息段，一个NdefMessage可能包含一个或者多个NdefRecord。

——————————————————————————————————————————————————————————————————
1，设置权限
<uses-sdk android:minSdkVersion="14"/>
<uses-permission android:name="android.permission.NFC" />
<!-- 要求当前设备必须要有NFC芯片 -->
<uses-feature android:name="android.hardware.nfc" android:required="true" />

2.定义可接收Tag的Activity

Activity清单需要配置一下launchMode属性：

<activity
    android:name=".TagTextActivity"
    android:launchMode="singleTop"/>

过滤ACTION_NDEF_DISCOVERED:
<intent-filter>   
<action android:name="android.nfc.action.NDEF_DISCOVERED"/>   
<category android:name="android.intent.category.DEFAULT"/>   
<data android:mimeType="text/plain" />  
 </intent-filter>  
最重要的应该算是data的mimeType类型了，这个定义的越准确，intent指向你这个activity的成功率就越高

通常来说，所有处理NFC的Activity都要设置launchMode属性为singleTop或者singleTask，保证了无论NFC标签靠近手机多少次，Activity实例只有一个。
moris采用在Intent中设置：
        mPendingIntent = PendingIntent.getActivity(this, 0,
                new Intent(this, getClass()).addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP), 0);

3.Activity中定义一个base类
/**
 * 1.子类需要在onCreate方法中做Activity初始化。
 * 2.子类需要在onNewIntent方法中进行NFC标签相关操作。
 *   当launchMode设置为singleTop时，第一次运行调用onCreate方法，
 *   第二次运行将不会创建新的Activity实例，将调用onNewIntent方法
 *   所以我们获取intent传递过来的Tag数据操作放在onNewIntent方法中执行
 *   如果在栈中已经有该Activity的实例，就重用该实例(会调用实例的onNewIntent())
 *   只要NFC标签靠近就执行
 */
public class BaseNfcActivity extends AppCompatActivity {
    private NfcAdapter mNfcAdapter;
    private PendingIntent mPendingIntent;
    /**
     * 启动Activity，界面可见时
     */
    @Override
    protected void onStart() {
        super.onStart();	
        mNfcAdapter = NfcAdapter.getDefaultAdapter(this);	//判断是否支持NFC
        //一旦截获NFC消息，就会通过PendingIntent调用窗口
        mPendingIntent = PendingIntent.getActivity(this, 0, new Intent(this, getClass()), 0);
    }
    /**
     * 获得焦点，按钮可以点击
     */
    @Override
    public void onResume() {
        super.onResume();
        //设置处理优于所有其他NFC的处理
        if (mNfcAdapter != null)
            mNfcAdapter.enableForegroundDispatch(this, mPendingIntent, null, null);
    }
    /**
     * 暂停Activity，界面获取焦点，按钮可以点击
     */
    @Override
    public void onPause() {
        super.onPause();
        //恢复默认状态
        if (mNfcAdapter != null)
            mNfcAdapter.disableForegroundDispatch(this);
    }
}

4.实例

读取NFC标签文本数据：
    /**
     * 读取NFC标签文本数据
     */
    private void readNfcTag(Intent intent) {
        if (NfcAdapter.ACTION_NDEF_DISCOVERED.equals(intent.getAction())) {

            Parcelable[] rawMsgs = intent.getParcelableArrayExtra(	//从intent中获取NDEF数据
                    NfcAdapter.EXTRA_NDEF_MESSAGES);
            NdefMessage msgs[] = null;
            if (rawMsgs != null) {
                msgs = new NdefMessage[rawMsgs.length];
                for (int i = 0; i < rawMsgs.length; i++) {
                    msgs[i] = (NdefMessage) rawMsgs[i];
                }
            }




向NFC tag中写入信息
    private NdefRecord newTextRecord(String text, Locale locale, boolean encodeInUtf8) {
        byte[]  langBytes   = locale.getLanguage().getBytes(Charset.forName("US-ASCII"));
        Charset utfEncoding = encodeInUtf8 ? Charset.forName("UTF-8") : Charset.forName("UTF-16");
        byte[]  textBytes   = text.getBytes(utfEncoding);
        int     utfBit      = encodeInUtf8 ? 0 : (1 << 7);
        char    status      = (char) (utfBit + langBytes.length);
        byte[]  data        = new byte[1 + langBytes.length + textBytes.length];
        data[0] = (byte) status;
        System.arraycopy(langBytes, 0, data, 1, langBytes.length);
        System.arraycopy(textBytes, 0, data, 1 + langBytes.length, textBytes.length);
        return new NdefRecord(NdefRecord.TNF_WELL_KNOWN, NdefRecord.RTD_TEXT, new byte[0], data);
    }




5.NDEF文本格式深度解析

（1）判断数据是否为NDEF格式

1）TNF（类型名格式，Type Name Format）必须是NdefRecord.TNF_WELL_KNOWN。
2）可变的长度类型必须是NdefRecord.RTD_TEXT。

如果这两个标准同时满足，那么就为NDEF格式。

（2）NDEF文本格式规范

不管什么格式的数据本质上都是由一些字节组成的。对于NDEF文本格式来说，这些数据的第1个字节描述了数据的状态，然后若干个字节描述文本的语言编码，最后剩余字节表示文本数据。这些数据格式由NFC Forum的相关规范定义，可以通过 http://members.nfc-forum.org/specs/spec_dashboard 下载相关的规范。

下面这两张表是规范中 3.2节 相对重要的翻译部分：

NDEF文本数据格式：

偏移量	长度（bytes）	描述
0	1	状态字节，见下表（状态字节编码格式）
1	n	ISO/IANA语言编码。例如"en-US"，"fr-CA"。编码格式是US-ASCII，长度（n）由状态字节的后6位指定。
n+1	m	文本数据。编码格式是UTF-8或UTF-16。编码格式由状态字节的前3位指定。
状态字节编码格式：

字节位（0是最低位，7是最高位）	含义
7	0:文本编码为UTF-8，1:文本编码为UTF-16
6	必须设为0
5..0	语言编码的长度（占用的字节个数）































