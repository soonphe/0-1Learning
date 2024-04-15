# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java数据导入excel

### 处理方式
1. 原生poi
2. hutool工具类
3. easyexcel（阿里开源项目）参考文档：https://easyexcel.opensource.alibaba.com/

### 原生poi
#### 1.依赖
```
            <!-- excel-->
            <!-- HSSF格式 -->
            <dependency>
                <groupId>org.apache.poi</groupId>
                <artifactId>poi</artifactId>
                <version>3.10-FINAL</version>
            </dependency>
            <!-- XSSF格式（excel，ppt，word） -->
            <dependency>
                <groupId>org.apache.poi</groupId>
                <artifactId>poi-ooxml</artifactId>
                <version>3.10-FINAL</version>
            </dependency>
```
说明：
HSSF：POI工程对Excel 97(-2007)文件操作的纯Java实现
XSSF：POI工程对Excel 2007 OOXML (.xlsx)文件操作的纯Java实现
SXSSF：从POI 3.8版本开始，提供了一种基于XSSF的低内存占用的API

#### 2.业务代码
```
public class ExcelUtil {

    /**
     * 导出excel
     *
     * @param name    文件名
     * @param title   表格标题名
     * @param headers 表格属性列名数组
     * @param dataset 需要显示的数据集合,集合中一定要放置符合javabean风格的类的对象。此方法支持的
     *                javabean属性的数据类型有基本数据类型及String,Date,byte[](图片数据)
     */
    public void exportExcel(String name, String title, String[] headers, Collection<T> dataset) throws IOException {
        // 声明一个工作薄
        HSSFWorkbook workbook = new HSSFWorkbook();
        // 生成一个表格
        HSSFSheet sheet = workbook.createSheet(title);
        // 设置表格默认列宽度为15个字节
        sheet.setDefaultColumnWidth((short) 15);
        // 生成一个样式
        HSSFCellStyle style = workbook.createCellStyle();
        // 设置这些样式
        style.setFillForegroundColor(HSSFColor.SKY_BLUE.index);
        style.setFillPattern(HSSFCellStyle.SOLID_FOREGROUND);
        style.setBorderBottom(HSSFCellStyle.BORDER_THIN);
        style.setBorderLeft(HSSFCellStyle.BORDER_THIN);
        style.setBorderRight(HSSFCellStyle.BORDER_THIN);
        style.setBorderTop(HSSFCellStyle.BORDER_THIN);
        style.setAlignment(HSSFCellStyle.ALIGN_CENTER);
        // 生成一个字体
        HSSFFont font = workbook.createFont();
//        font.setColor(HSSFColor.VIOLET.index);
        font.setColor(HSSFColor.BLACK.index);
        font.setFontHeightInPoints((short) 14);
        font.setBoldweight(HSSFFont.BOLDWEIGHT_BOLD);
        // 把字体应用到当前的样式
        style.setFont(font);
        // 生成并设置另一个样式
        HSSFCellStyle style2 = workbook.createCellStyle();
//        style2.setFillForegroundColor(HSSFColor.LIGHT_YELLOW.index);
        style2.setFillForegroundColor(HSSFColor.WHITE.index);
        style2.setFillPattern(HSSFCellStyle.SOLID_FOREGROUND);
        style2.setBorderBottom(HSSFCellStyle.BORDER_THIN);
        style2.setBorderLeft(HSSFCellStyle.BORDER_THIN);
        style2.setBorderRight(HSSFCellStyle.BORDER_THIN);
        style2.setBorderTop(HSSFCellStyle.BORDER_THIN);
        style2.setAlignment(HSSFCellStyle.ALIGN_CENTER);
        style2.setVerticalAlignment(HSSFCellStyle.VERTICAL_CENTER);
        // 生成另一个字体
        HSSFFont font2 = workbook.createFont();
        font2.setBoldweight(HSSFFont.BOLDWEIGHT_NORMAL);
        font2.setFontHeightInPoints((short) 14);
        // 把字体应用到当前的样式
        style2.setFont(font2);

        // 声明一个画图的顶级管理器
        HSSFPatriarch patriarch = sheet.createDrawingPatriarch();
        // 定义注释的大小和位置,详见文档
        HSSFComment comment = patriarch.createComment(new HSSFClientAnchor(0,
                0, 0, 0, (short) 4, 2, (short) 6, 5));
        // 设置注释内容
        comment.setString(new HSSFRichTextString("可以在POI中添加注释！"));
        // 设置注释作者，当鼠标移动到单元格上是可以在状态栏中看到该内容.
        comment.setAuthor("leno");

        // 产生表格标题行
        HSSFRow row = sheet.createRow(0);
        for (int i = 0; i < headers.length; i++) {
            HSSFCell cell = row.createCell(i);
            cell.setCellStyle(style);
            HSSFRichTextString text = new HSSFRichTextString(headers[i]);
            cell.setCellValue(text);
        }

        // 遍历集合数据，产生数据行
        Iterator<T> it = dataset.iterator();
        int index = 0;
        while (it.hasNext()) {
            index++;
            //创建行
            row = sheet.createRow(index);

            // 利用反射，根据javabean属性的先后顺序，动态调用getXxx()方法得到属性值
            T t = (T) it.next();
            Field[] fields = t.getClass().getDeclaredFields();
            for (int i = 0; i < fields.length; i++) {
//                System.out.println(fields[i].getName());
                //创建单元格
                HSSFCell cell = row.createCell(i);
                cell.setCellStyle(style2);
                Field field = fields[i];
                String fieldName = field.getName();
                String getMethodName = "get"
                        + fieldName.substring(0, 1).toUpperCase()
                        + fieldName.substring(1);
                try {
                    Class tCls = t.getClass();
                    Method getMethod = tCls.getMethod(getMethodName
                    );
                    Object value = getMethod.invoke(t);
                    // 判断值的类型后进行强制类型转换
                    String textValue = null;
                    if (value instanceof Integer) {
                        int intValue = (Integer) value;
                        cell.setCellValue(intValue);
                    } else if (value instanceof Float) {
                        float fValue = (Float) value;
                        cell.setCellValue(fValue);
                    } else if (value instanceof Double) {
                        double dValue = (Double) value;
                        cell.setCellValue(dValue);
                    } else if (value instanceof Long) {
                        long longValue = (Long) value;
                        cell.setCellValue(longValue);
                    } else {
                        textValue = value + "";
                        cell.setCellValue(textValue);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        String path = ClassUtils.getDefaultClassLoader().getResource("").getPath() + "/static" + name;
        workbook.write(new FileOutputStream(path));
    }
}
```

### hutool工具类
```
public static String explain() {
        ExcelReader reader;
        //从文件中读取Excel为ExcelReader
//        ExcelReader reader = ExcelUtil.getReader(FileUtil.file("test.xlsx"));
        //从流中读取Excel为ExcelReader（比如从ClassPath中读取Excel文件）
//        ExcelReader reader = ExcelUtil.getReader(ResourceUtil.getStream("aaa.xlsx"));

        /**
         * 读取指定的sheet
         * bookFile – Excel文件
         * sheetIndex – sheet序号，0表示第一个sheet
         */
        //通过sheet编号获取
        reader = ExcelUtil.getReader(FileUtil.file("/Users/luoxiaosheng/Downloads/less5000.xlsx"), 0);
        //通过sheet名获取
//        reader = ExcelUtil.getReader(FileUtil.file("test.xlsx"), "sheet1");

        /**
         * 读取大数据量的Excel
         * path – Excel文件路径
         * rid – sheet rid，-1表示全部Sheet, 0表示第一个Sheet
         * rowHandler – 行处理器
         */
        ExcelUtil.readBySax("/Users/luoxiaosheng/Downloads/less5000.xlsx", -1, createRowHandler());

        //读取Excel中所有行和列，都用列表表示
        List<List<Object>> readAll = reader.read();
        //读取为Map列表，默认第一行为标题行，Map中的key为标题，value为标题对应的单元格值。
//        List<Map<String,Object>> readAll = reader.readAll();
        //读取为Bean列表，Bean中的字段名为标题，字段值为标题对应的单元格值。
//        List<Person> all = reader.readAll(Person.class);

        for (List<Object> objects : readAll) {
            System.out.println(objects.get(0).toString()+"==="+objects.get(1));
        }

        return "";
    }

    /**
     * 读取大数据量的Excel
     *
     * @return
     */
    private static RowHandler createRowHandler() {

        return new RowHandler() {
            /**
             sheetIndex – 当前Sheet序号
             rowIndex – 当前行号，从0开始计数
             rowCells – 行数据，每个Object表示一个单元格的值
             */
            @Override
            public void handle(int sheetIndex, long rowIndex, List<Object> rowlist) {
                System.out.println("sheetIndex："+sheetIndex+"，rowIndex："+rowIndex+"，rowlist："+rowlist);
//                Console.log("[{}] [{}] {}", sheetIndex, rowIndex, rowlist);
            }
        };
    }
```

### easyexcel
依赖
```

```

实体类
```
@Data
public class TrainFenceExcelDto {

    /**
     * 强制读取第三个 这里不建议 index 和 name 同时用，要么一个对象只用index，要么一个对象只用name去匹配
     */
    @ExcelProperty(index = 2)
    private Double doubleData;
    
    /**
     * 用名字去匹配，这里需要注意，如果名字重复，会导致只有一个字段读取到数据
     * id
     */
    @ExcelProperty("id")
    private String id;

    /**
     * 名称
     */
    @ExcelProperty("name")
    private String name;
}
```

监听器代码
```
public class EasyExcelUtil<E> {


    private static final EasyExcelUtil  instance = new EasyExcelUtil();

    /**
     * 私有构造方法 单例
     */
    private EasyExcelUtil(){

    }

    /**
     * 获取excel读取工具类的实例
     * @return
     */
    public static EasyExcelUtil getInstance(){
        return instance;
    }

    /**
     * 同步读取Excel
     * @param inputStream 文件内容
     * @param sheetNum 读取第几个sheet
     * @param c 指定读取的bean
     * @param headNum 指定读取第一行开始
     * @return 读取到的文件内容，转换成bean的list
     */
    public List<E> syncReadExcelToBean(InputStream inputStream, int sheetNum, Class<E> c,Integer headNum){
        // 这里 需要指定读用哪个class去读，然后读取第一个sheet 同步读取会自动finish
        List<E> list = EasyExcel.read(inputStream).head(c).sheet(sheetNum).headRowNumber(headNum).doReadSync();
        return list;
    }

    /**
     * 同步读取Excel
     * @param invoiceByte 二进制文件内容
     * @param sheetNum 读取第几个sheet
     * @param c 指定读取的bean
     * @param headNum 指定读取第一行开始
     * @return 读取到的文件内容，转换成bean的list
     */
    public List<E> syncReadExcelToBean(byte[] invoiceByte, int sheetNum, Class<E> c,Integer headNum){
        // 这里 需要指定读用哪个class去读，然后读取第一个sheet 同步读取会自动finish
        List<E> list = EasyExcel.read(byteToInputStream(invoiceByte)).head(c).sheet(sheetNum).headRowNumber(headNum).doReadSync();
        return list;
    }

    /**
     * 同步读取Excel
     * @param invoiceByte 文件内容
     * @param sheetNum 读取第几个sheet
     * @return 读取到的文件内容，转换成map的list
     */
    public static List<Map<Integer, String>> syncReadExcelToMap(byte[] invoiceByte, int sheetNum){
        // 返回一个list，然后读取第一个sheet 同步读取会自动finish
        List<Map<Integer, String>> listMap = EasyExcel.read(byteToInputStream(invoiceByte)).sheet(sheetNum).doReadSync();
        return listMap;
    }

    /**
     * 同步读取Excel表头，配合syncReadExcelToMap使用，根据Excel中的列号获取对应的表头文字，避免税局Excel模版更新读取错误
     * @param invoiceByte 文件内容
     * @param sheetNum 读取第几个sheet
     * @return 读取到的文件表头，转换成map的list
     */
    public static Map<Object, Object> syncReadHeadExcelToMap(byte[] invoiceByte, int sheetNum){
        List<Map<Integer, String>> headList = new ArrayList<>();
        EasyExcel.read(byteToInputStream(invoiceByte), new SyncReadListener() {
            @Override
            public void invokeHeadMap(Map<Integer, String> headMap, AnalysisContext context) {
                headList.add(headMap);
            }
        }).sheet(sheetNum).doRead();
        //把列号KEY 表头名称Value反转后返回
        Map<Integer, String> map = headList.get(0);
        return map.entrySet().stream().collect(Collectors.toMap(entry -> entry.getValue(), entry -> entry.getKey()));
    }

    /**
     * 获取所有sheet的名称
     * @param invoiceByte excel文件流
     * @return
     */
    public List<ReadSheet> readSheetNames(byte[] invoiceByte){
        List<ReadSheet> readSheets = EasyExcel.read(byteToInputStream(invoiceByte)).build().excelExecutor().sheetList();
        return readSheets;
    }


    /**
     * byte 转换 InputStream
     * @param invoiceByte 文件内容
     * @return 文件流
     */
    private static InputStream byteToInputStream(byte[] invoiceByte){
        return new ByteArrayInputStream(invoiceByte);
    }
}
```


调用方
```
    @GetMapping("excelImport")
    @ApiOperation("excel内容导入")
    public CommonResult excelImport() {

        File file =new File("/Users/luoxiaosheng/Downloads/武汉市基础数据wl-2024-03-04.xls");
        try {
            InputStream is = new FileInputStream(file);
            List<TrainFenceExcelDto> list = EasyExcelUtil.getInstance().syncReadExcelToBean(is, 0, TrainFenceExcelDto.class, 1);
            if (!CollectionUtils.isEmpty(list)){
                List<TrainFence> trainFences = TrainFenceMapper.INSTANCE.TrainFenceVoToTrainFence(list);
                for (TrainFence item:trainFences){
                    TrainFence one = service.getOne(Wrappers.<TrainFence>lambdaQuery()
                            .eq(StringUtils.isNotBlank(item.getInscode()), TrainFence::getInscode, item.getInscode())
                            .last("LIMIT 1"));
                    if (Objects.nonNull(one)){
                        continue;
                    }
                    service.save(item);
                }
            }
            return CommonResult.success();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        return CommonResult.failed("");
    }
```
