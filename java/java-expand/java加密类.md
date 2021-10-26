# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java加密类
MD5、SHA


### 代码
```
public class EncryptUtil {
	/**
	 * MD5加密
	 * @param inputText
	 * @return
	 */
	public static String getMd5(String inputText) {
		return encrypt(inputText, "md5");
	}

	/**
	 * sha加密 
	 * @param inputText
	 * @return
	 */
	public static String getSha(String inputText) {
		return encrypt(inputText, "sha-1");
	}

	/** 
	 * md5或者sha-1加密 
	 *  
	 * @param inputText 要加密的内容
	 * @param algorithmName 加密算法名称：md5或者sha-1，不区分大小写
	 * @return 
	 */
	private static String encrypt(String inputText, String algorithmName) {
		if (inputText == null || "".equals(inputText.trim())) {
			throw new IllegalArgumentException("请输入要加密的内容");
		}
		if (algorithmName == null || "".equals(algorithmName.trim())) {
			algorithmName = "md5";
		}
		try {
			MessageDigest m = MessageDigest.getInstance(algorithmName);
			m.update(inputText.getBytes("UTF8"));
			byte s[] = m.digest();
			return hex(s);
		} catch (NoSuchAlgorithmException | UnsupportedEncodingException e) {
			e.printStackTrace();
		}
		return null;
	}

	/**
	 * 返回十六进制字符串1
	 * @param arr
	 * @return
	 */
	private static String hex(byte[] arr) {
		StringBuilder sb = new StringBuilder();
		for (byte anArr : arr) {
			sb.append(Integer.toHexString((anArr & 0xFF) | 0x100), 1, 3);
		}
		return sb.toString();
	}

	/**
	 * 返回十六进制字符串2
	 * @param bytes
	 * @return
	 */
	private static String getFormattedText(byte[] bytes) {
		int len = bytes.length;
		StringBuilder buf = new StringBuilder(len * 2);
		for (int j = 0; j < len; j++) {
			buf.append(HEX_DIGITS[(bytes[j] >> 4) & 0x0f]);
			buf.append(HEX_DIGITS[bytes[j] & 0x0f]);
		}
		return buf.toString();
	}

	private static final char[] HEX_DIGITS = {'0', '1', '2', '3', '4', '5',
			'6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};

	/**
	 * 十六进制字符串转数字
	 *
	 * @param str
	 * @return
	 */
	public static int hexTrans(String str) {
		return Integer.parseInt(str.replaceAll("^0[x|X]", ""), 16);
	}

	public static void main(String[] args) {
		System.out.println(EncryptUtil.getMd5("123456"));
//		System.out.println(EncryptUtil.getSha("123456"));
	}

}
```

### 自定义序列化类
```java
public class SerializeUtil implements RedisSerializer {

    @Override
    public byte[] serialize(Object object) {
        ObjectOutputStream oos;
        ByteArrayOutputStream baos;
        try {
            //字节数组输出流，
            baos = new ByteArrayOutputStream();
            //对象输出流——创建写入指定OutputStream的ObjectOutputStream
            oos = new ObjectOutputStream(baos);
            //将指定的对象写进字节数组输出
            oos.writeObject(object);
            return baos.toByteArray();
        } catch (Exception e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }

    @Override
    public Object deserialize(byte[] bytes) {
        if (bytes == null) {
            return null;
        }
        ByteArrayInputStream bais;
        try {
            // 反序列化
            bais = new ByteArrayInputStream(bytes);
            ObjectInputStream ois = new ObjectInputStream(bais);
            return ois.readObject();
        } catch (Exception e) {
            throw new RuntimeException(e.getMessage(), e);
        }
    }
}
```