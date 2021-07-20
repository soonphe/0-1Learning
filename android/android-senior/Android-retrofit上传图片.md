android图片上传
 
 
后台请求路径
    public UploadResult uploads(@RequestParam(value = "file", required = false) MultipartFile[] files, String file_type) {
 
多图上传——数量不确定
  @Multipart
  @POST()
  Observable<ResponseBody> uploadFiles(
        @Url String url,
        @PartMap() Map<String, RequestBody> maps);        //RequestBody
 
 
    @Multipart
    @POST("common/uploads")
    Call<ResponseBody> uploadFile(@PartMap Map<String, RequestBody> params );        //RequestBody
 
RequestBody requestBody3 = RequestBody.create(MediaType.parse(""), "123456");        //第一个参数
        Map<String, RequestBody> params = new HashMap<>();
        params.put("file_type", requestBody3);
 
        for(int i = 0; i<fileList.size();i++){
            File file = fileList.get(i);
            RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
            params.put("file\"; filename=\""+ file.getName(), requestBody);
        }
 
/**
    
 * 通过 List<MultipartBody.Part> 传入多个part实现多文件上传
     
 * @param parts 每个part代表一个
    
 * @return 状态信息
    
 */
    
@Multipart
    
@POST("users/image")
    
Call<BaseResponse<String>> uploadFilesWithParts(@Part() List<MultipartBody.Part> parts);
 
//@Part List<MultipartBody.Part> partList
 
public static List<MultipartBody.Part> filesToMultipartBodyParts(List<File> files) {
        
List<MultipartBody.Part> parts = new ArrayList<>(files.size());
       
for (File file : files) {
            
// TODO: 16-4-2  这里为了简单起见，没有判断file的类型
           
  RequestBody requestBody = RequestBody.create(MediaType.parse("image/png"), file);
          
  MultipartBody.Part part = MultipartBody.Part.createFormData("file", file.getName(), requestBody);
          
  parts.add(part);
       
 }
        
return parts;
    
}
 
 
——————————————————————————————————————————————————————
 
图片上传——数量已知：
    @Multipart
    @POST("card/upload")    //uploadFile(@Part("file\"; filename=\"avatar.png\"") RequestBody file)
    Observable<FileUploadBean> uploadFile(@Part MultipartBody.Part file);        //MultipartBody
 
            File file=new File(s);
            RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
            MultipartBody.Part photoPart = MultipartBody.Part.createFormData("file", file.getName(), requestBody);
            RetrofitUtils.getInstance().getRetrofit_Post().uploadFile(photoPart).
 
 
 
同时上传一张图片和字符串
  @Multipart    
    
  @POST("user/updateAvatar.do")
    
  Call<Response> updateAvatar (@Query("des") String description, @Part("uploadFile\"; filename=\"test.jpg\"") RequestBody imgs );
}
 
  //RequestBody
 
        final File file = new File(filename);
        RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
Call<Response> call = service.updateInfo(msg, requestBody );
 
 
