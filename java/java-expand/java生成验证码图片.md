# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java生成验证码图片

### 1.验证码
```
public class VerifyCodeUtil {
	// 图片的宽度。  
	private int width = 120;
	// 图片的高度。  
	private int height = 40;
	// 验证码字符个数  
	private int codeCount = 5;
	// 验证码干扰线数  
	private int lineCount = 150;
	// 验证码  
	private String code = null;
	// 验证码图片Buffer  
	private BufferedImage buffImg = null;

	public VerifyCodeUtil(String randomCode) {
		this.createCode(randomCode);
	}

	/** 
	 *  
	 * @param width 图片宽 
	 * @param height 图片高 
	 */
	public VerifyCodeUtil(int width, int height, String randomCode) {
		this.width = width;
		this.height = height;
		this.createCode(randomCode);
	}

	/** 
	 *  
	 * @param width 图片宽 
	 * @param height 图片高 
	 * @param codeCount 字符个数 
	 * @param lineCount 干扰线条数 
	 */
	public VerifyCodeUtil(int width, int height, int codeCount, int lineCount, String randomCode) {
		this.width = width;
		this.height = height;
		this.codeCount = codeCount;
		this.lineCount = lineCount;
		this.createCode(randomCode);
	}

	public void createCode(String randomCode) {
		int x = 0, fontHeight = 0, codeY = 0;
		int red = 0, green = 0, blue = 0;

		x = width / (codeCount + 2);//每个字符的宽度  
		fontHeight = height - 2;//字体的高度  
		codeY = height - 4;

		// 图像buffer  
		buffImg = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
		Graphics2D g = buffImg.createGraphics();
		// 生成随机数  
		Random random = new Random();
		// 将图像填充为白色  
		g.setColor(Color.WHITE);
		g.fillRect(0, 0, width, height);
		// 创建字体  
		ImgFontByte imgFont = new ImgFontByte();
		Font font = imgFont.getFont(fontHeight);
		g.setFont(font);

		for (int i = 0; i < lineCount; i++) {
			int xs = random.nextInt(width);
			int ys = random.nextInt(height);
			int xe = xs + random.nextInt(width / 8);
			int ye = ys + random.nextInt(height / 8);
			red = random.nextInt(255);
			green = random.nextInt(255);
			blue = random.nextInt(255);
			g.setColor(new Color(red, green, blue));
			g.drawLine(xs, ys, xe, ye);
		}

		// randomCode记录随机产生的验证码  
		//		StringBuffer randomCode = new StringBuffer();
		// 随机产生codeCount个字符的验证码。  
		for (int i = 0; i < randomCode.length(); i++) {
			String strRand = randomCode.substring(i, i + 1);
			// 产生随机的颜色值，让输出的每个字符的颜色值都将不同。  
			red = random.nextInt(255);
			green = random.nextInt(255);
			blue = random.nextInt(255);
			g.setColor(new Color(red, green, blue));
			g.drawString(strRand, (i + 1) * x, codeY);
			// 将产生的四个随机数组合在一起。  
		}
	}

	public void write(String path) throws IOException {
		OutputStream sos = new FileOutputStream(path);
		this.write(sos);
	}

	public void write(OutputStream sos) throws IOException {
		ImageIO.write(buffImg, "png", sos);
		sos.close();
	}

	public BufferedImage getBuffImg() {
		return buffImg;
	}

	public String getCode() {
		return code;
	}

	public static void main(String[] args) {
		VerifyCodeUtil vCode = new VerifyCodeUtil(120, 40, 5, 100, "11111");
		try {
			String path = "D:\\ConnXunWork\\" + "20170728122130493863.jpg";
			System.out.println(vCode.getCode() + " >" + path);
			vCode.write(path);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

### 2.字体
```
public class ImgFontByte {
	public Font getFont(int fontHeight) {
		try {
			Font baseFont = Font.createFont(Font.TRUETYPE_FONT, new ByteArrayInputStream(hex2byte(getFontByteStr())));
			return baseFont.deriveFont(Font.PLAIN, fontHeight);
		} catch (Exception e) {
			return new Font("Arial", Font.PLAIN, fontHeight);
		}
	}

	private byte[] hex2byte(String str) {
		if (str == null)
			return null;
		str = str.trim();
		int len = str.length();
		if (len == 0 || len % 2 == 1)
			return null;

		byte[] b = new byte[len / 2];
		try {
			for (int i = 0; i < str.length(); i += 2) {
				b[i / 2] = (byte) Integer.decode("0x" + str.substring(i, i + 2)).intValue();
			}
			return b;
		} catch (Exception e) {
			return null;
		}
	}

	/** 
	* ttf字体文件的十六进制字符串 
	* @return 
	*/
	private String getFontByteStr() {
		return null;
	}
}
```
