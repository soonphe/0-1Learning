# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-Camera

### Android调用camera
1. 权限
```
<!-- 往SDCard写入数据权限 --> 
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
<!--请求访问使用照相设备-->
<uses-permission android:name="android.permission.CAMERA" />
```
2. 常量
```
public final static int ALBUM_REQUEST_CODE = 1;
public final static int CROP_REQUEST = 2;
public final static int CAMERA_REQUEST_CODE = 3;
public static String SAVED_IMAGE_DIR_PATH = Environment.getExternalStorageDirectory().getPath() + "/AppName/camera/";// 拍照路径
String cameraPath;
```
 
3. 相机
```
    // 指定相机拍摄照片保存地址
    String state = Environment.getExternalStorageState();
    if (state.equals(Environment.MEDIA_MOUNTED)) {
        //文件名
        String fileName = "IMG_" + System.currentTimeMillis() + ".png";
        //文件完整路径
        cameraPath = SAVED_IMAGE_DIR_PATH + fileName;
        //MediaStore.ACTION_IMAGE_CAPTURE ： 指定开启系统相机的Action
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        //创建文件（文件基础路径，文件名）
        File dir = new File(SAVED_IMAGE_DIR_PATH, fileName);
        if (!dir.getParentFile().exists()) {
            dir.getParentFile().mkdirs();
        }
        // 设置系统相机拍摄照片完成后图片文件的存放地址，Uri.fromFile(dir)把文件地址转换成Uri格式
        intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(dir));
        startActivityForResult(intent, CAMERA_REQUEST_CODE);
    } else {
        Toast.makeText(getApplicationContext(), "请确认已经插入SD卡",
                Toast.LENGTH_LONG).show();
    }
 ```
4. onActivityResult，拿到拍摄地址
```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == Activity.RESULT_OK) {
        if (requestCode == CAMERA_REQUEST_CODE) {
            LogUtil.d("path=" + cameraPath);
                Bitmap bitmap = null;
                try {
                    bitmap = CommonUtil.revitionImageSize(cameraPath, getActivity());
                } catch (IOException e) {
                    e.printStackTrace();
                }
                PostMessageImageItem item = new PostMessageImageItem();
                item.setPath(cameraPath);
                item.setId(id);
                item.setImage(bitmap);
                gridViewData.add(item);
                gridadapter.notifyDataSetChanged();
        }
 }
```
 
如果不需要自定义路径，默认为Environment.DIRECTORY_PICTURES
```
   /**
    * 启动相机
    */
   public static String startCamera(Activity activity, int requestCode) {
       // 指定相机拍摄照片保存地址
       String state = Environment.getExternalStorageState();
       if (state.equals(Environment.MEDIA_MOUNTED)) {
           Intent intent = new Intent();
           // 指定开启系统相机的Action
           intent.setAction(MediaStore.ACTION_IMAGE_CAPTURE);
           File outDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
           if (!outDir.exists()) {
               outDir.mkdirs();
           }
           File outFile = new File(outDir, System.currentTimeMillis() + ".jpg");
           // 把文件地址转换成Uri格式
           Uri uri = Uri.fromFile(outFile);
           LogUtil.d("getAbsolutePath=" + outFile.getAbsolutePath());
           // 设置系统相机拍摄照片完成后图片文件的存放地址
           intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
           // 此值在最低质量最小文件尺寸时是0，在最高质量最大文件尺寸时是１
           intent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 0);
           activity.startActivityForResult(intent, requestCode);
           return outFile.getAbsolutePath();
       } else {
           Toast.makeText(activity, "请确认已经插入SD卡",
                   Toast.LENGTH_LONG).show();
           return null;
       }
   }
```

### android中文件地址与Uri地址相互转换：
 
Uri转文件地址：
```
 final String scheme = uri.getScheme();  
    String data = null;  
    if ( scheme == null )  
        data = uri.getPath();  
    else if ( ContentResolver.SCHEME_FILE.equals( scheme ) ) {  
        data = uri.getPath();  
    } else if ( ContentResolver.SCHEME_CONTENT.equals( scheme ) ) {  
        Cursor cursor = context.getContentResolver().query( uri, new String[] { ImageColumns.DATA }, null, null, null );  
        if ( null != cursor ) {  
            if ( cursor.moveToFirst() ) {  
                int index = cursor.getColumnIndex( ImageColumns.DATA );  
                if ( index > -1 ) {  
                    data = cursor.getString( index );  
                }  
            }  
            cursor.close();  
        }  
```
文件地址转Uri：
```
    File outFile = new File(outDir, System.currentTimeMillis() + ".jpg");
   // 把文件地址转换成Uri格式
   Uri uri = Uri.fromFile(outFile);
```

### 相册
```
Intent intent = new Intent(Intent.ACTION_PICK, null);
intent.setDataAndType(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, "image/*");
intent.setAction(Intent.ACTION_GET_CONTENT);
startActivityForResult(intent, ALBUM_REQUEST_CODE);
```
onActivityResult，调用系统相册，然后通过Uri拿到图片的绝对地址。
```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
       super.onActivityResult(requestCode, resultCode, data);
       if (resultCode == Activity.RESULT_OK) {
           if (requestCode == AppConstants.ALBUM_REQUEST_CODE) {
               try {
                   Uri uri = data.getData();
                   final String absolutePath= getAbsolutePath(mActivity, uri);
                   LogUtil.d("path=" + absolutePath);
               } catch (Exception e) {
                   e.printStackTrace();
               }
           }
       }
   }
```
getAbsolutePath方法
```
public String getAbsolutePath(final Context context, final Uri uri) {
    if (null == uri) return null;
    final String scheme = uri.getScheme();
    String data = null;
    if (scheme == null)
        data = uri.getPath();
    else if (ContentResolver.SCHEME_FILE.equals(scheme)) {
        data = uri.getPath();
    } else if (ContentResolver.SCHEME_CONTENT.equals(scheme)) {
        Cursor cursor = context.getContentResolver().query(uri, 
        new String[]{MediaStore.Images.ImageColumns.DATA}, null, null, null);
        if (null != cursor) {
            if (cursor.moveToFirst()) {
                int index = cursor.getColumnIndex(MediaStore.Images.ImageColumns.DATA);
                if (index > -1) {
                    data = cursor.getString(index);
                }
            }
            cursor.close();
        }
    }
    return data;
}
```
可以在onAcitivityResult回调方法中添加裁剪方法，再回调显示
```
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (resultCode == -1) {   
    if(requestCode==CUT_PICTURE){//剪裁3
                    //此处启动裁剪程序
                    Intent intent = new Intent("com.android.camera.action.CROP");
                    intent.setDataAndType(data.getData(), "image/*");        //data.getData() 获取图片的地址
                    intent.putExtra("scale", true);
                    intent.putExtra(MediaStore.EXTRA_OUTPUT, imageUri);
                    startActivityForResult(intent, SHOW_PICTURE);
 
            }else if (requestCode == SHOW_PICTURE) {        //裁剪后的回调方法
//                Uri uri = data.getData();
                Uri uri =imageUri;
                try {
                    String path;
                    String scheme = uri.getScheme();
                    if (scheme.equals("content")) {
                        path = getPath(getActivity(),uri);
                        path= URLDecoder.decode(path, "UTF-8");
                    }else {
                        path=URLDecoder.decode(uri.getEncodedPath(),"UTF-8");
                    }
                    Bitmap bitmap = null;
                    bitmap = CommonUtil.revitionImageSize(path, getActivity());
                     /* 将Bitmap设定到ImageView */
                    PostMessageImageItem item = new PostMessageImageItem();
                    item.setId(id);
                    item.setPath(path);
                    item.setImage(bitmap);
                    gridViewData.add(item);
                    picList.add(path);
                    gridadapter.notifyDataSetChanged();
                    id++;
                    imageUri=null;
                } catch (Exception e) {
                    Log.e("Exception", e.getMessage(), e);
                }
 
            }
```