## Java中的Cipher类
第一个方法是加密方法，获取到Cipher实例，再将加密的私钥公钥作为参数传入实例。随后调用cipher的dofinal方法，返回加密后的字符串。 第二个方法是解密方法，同样是实例化，传入对应的参数，讲字节流传入，用dofinal完成解密。


先来看一对加解密的方法：

加密--------------------------------------------------------------------------------------------------------
```
/**

content: 加密内容
slatKey: 加密的盐，16位字符串
vectorKey: 加密的向量，16位字符串
*/
public String encrypt(String content, String slatKey, String vectorKey) throws Exception {
    Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
    SecretKey secretKey = new SecretKeySpec(slatKey.getBytes(), "AES");
    IvParameterSpec iv = new IvParameterSpec(vectorKey.getBytes());
    cipher.init(Cipher.ENCRYPT_MODE, secretKey, iv);
    byte[] encrypted = cipher.doFinal(content.getBytes());
    return Base64.encodeBase64String(encrypted);
}
```

解密----------------------------------------------------------------------------------------------------------
```
/**

content: 解密内容(base64编码格式)
slatKey: 加密时使用的盐，16位字符串
vectorKey: 加密时使用的向量，16位字符串
*/
public String decrypt(String base64Content,String slatKey,String vectorKey)throws Exception{
    Cipher cipher=Cipher.getInstance("AES/CBC/PKCS5Padding");
    SecretKey secretKey=new SecretKeySpec(slatKey.getBytes(),"AES");
    IvParameterSpec iv=new IvParameterSpec(vectorKey.getBytes());
    cipher.init(Cipher.DECRYPT_MODE,secretKey,iv);
    byte[]content=Base64.decodeBase64(base64Content);
    byte[]encrypted=cipher.doFinal(content);
    return new String(encrypted);
    }
```