# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java处理文件上传


### 代码实现
```
@RestController
@RequestMapping(value = {"api/common", "common"})
@Api(tags = {"Upload"}, description = "上传文件")
public class UploadController extends AppBaseController {

    private static Logger logger = LoggerFactory.getLogger("controllerLog");

    @Value("${imageUploadPath}")
    private String uploadPath;

    /**
     * 单文件上传
     *
     * @param file
     * @param file_type
     * @return
     */
    @PostMapping("/upload")
    @ApiOperation("单文件上传")
    public JsonEntity upload(@ApiParam(required = true, value = "文件") @RequestParam(value = "file") MultipartFile file,
                             @ApiParam(value = "文件类型") @RequestParam(value = "file_type", required = false) String file_type) {
        String result = saveFile(file, file_type);
        if (StrUtil.isNotEmpty(result)) {
            json.setObject(result);
        } else {
            json.setErrorCode(CodeEnum.FILEUPLOADFAIL);
        }
        return json;

    }

    /**
     * 多文件上传
     *
     * @param files     文件数组
     * @param file_type 文件类型（顶级文件夹）
     * @return
     */
    @PostMapping("/uploads")
    @ApiOperation("多文件上传")
    public JsonEntity uploads(@ApiParam(required = true, value = "文件") @RequestParam(value = "file") MultipartFile[] files,
                              @ApiParam(value = "文件类型") @RequestParam(value = "file_type", required = false) String file_type) {
        if (files.length > 0) {
            String filesResult = "";
            for (MultipartFile file : files) {
                String result = saveFile(file, file_type);
                //判断是否为第一个返回路径
                if (filesResult.equals("")) {
                    filesResult += result;
                } else {
                    filesResult += "," + result;
                }
            }
            logger.info("上传文件成功");
            json.setObject(filesResult);
        } else {
            json.setErrorCode(CodeEnum.FILEMISS);
        }
        return json;
    }

    /**
     * 封装公共存储文件方法
     *
     * @param file      文件
     * @param file_type 文件类型
     */
    public String saveFile(MultipartFile file, String file_type) {
        //生成文件存储路径（年月日生成文件夹）
        String storePath = "/" + file_type + "/" + DateUtil.format(new Date(), "yyyyMMdd") + "/";
        //获取文件原始名称
        String fileName = file.getOriginalFilename();
        //获取文件后缀名
        String suffix = fileName.substring(fileName.lastIndexOf(".") + 1, fileName.length());
        //生成文件名称（毫秒值 + .原文件后缀）
        String newFileName = DateUtil.currentSeconds() + "." + suffix;
        //生成目标文件
        File targetFile = new File(uploadPath + storePath, newFileName);
        // 检测目标文件是否存在目录
        if (!targetFile.getParentFile().exists()) {
            targetFile.getParentFile().mkdirs();
        }
        try {
            file.transferTo(targetFile);
            logger.info("上传文件成功");
            return storePath + newFileName;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return "";
    }

    /**
     * 删除文件
     *
     * @param path 文件路径
     * @return
     */
    @GetMapping("delFile")
    @ApiOperation("删除文件")
    public JsonEntity delFile(@ApiParam(required = true, value = "文件路径") @RequestParam(value = "path") String path) {
        File file = Paths.get(uploadPath, path).toFile();
        if (file.isFile()) {
            file.delete();
            json.setObject(null);
        } else {
            json.setErrorCode(CodeEnum.FILEMISS);
        }
        return json;
    }
}
```
