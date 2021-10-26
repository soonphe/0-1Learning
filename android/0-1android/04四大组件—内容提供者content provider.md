# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 内容提供者content provider

### 前言
    我们知道物种之间存在生殖隔离，同理，数据的隔离也是如此。

    APP在内部互相访问数据是容易的，但是一个APP中的数据要提供给外部使用就没那么容易了。

    内容提供器（Content Provider）主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访数据的安全性。
    
### 访问其他程序中的数据
当一个应用程序通过内容提供器对其数据提供了外部访问接口，任何其他的应用程序就都可以对这部分数据进行访问。
Android 系统中自带的电话簿、短信、媒体库等程序都提供了类似的访问接口

### ContentResolver 的基本用法
对于每一个应用程序来说，如果想要访问内容提供器中共享的数据，就一定要借助ContentResolver 类，可以通过Context中的getContentResolver() 方法获取该类的实例。
ContentResolver中提供了一系列的方法用于对数据进行CRUD操作，其中insert() 方法用于添加数据，update() 方法用于更新数据，delete() 方法用于删除数据，query() 方法用于查询数据。
不同于SQLiteDatabase，ContentResolver 中的增删改查都是接收一个URl参数，这个参数被称为内容URL。
内容URL给内容提供器中的数据建立了唯一标识符，它主要由两部分组成：authority 和 path 。
authority 是用于对不同的应用程序做区分的，一般为了避免冲突，都会采用程序包名的方式进行命名。
path则是用于对同一应用程序中不同的表做区分，通常都会添加到authority后面：

内容URI 最标准的格式写法如下：
content://com.example.app.provider/table1
content://com.example.app.provider/table2/1

内容URI 的格式主要就只有以上两种，以路径结尾就表示期望访问该表中所有的数据，以id 结尾就表示期望访问该表中拥有相应id 的数据。
我们可以使用通配符的方式来分别匹配这两种格式的内容URI，规则如下:
* : 表示匹配任意长度的任意字符
\# : 表示匹配任意长度的数字

//一个能够匹配任意表的内容URI格式就可以写成:
content://com.example.app.provider/*
//一个能够匹配表中任意一行数据的内容URI格式就可以写成：
content://com.example.app.provider/table1/#

将内容URI 字符串解析成Uri 对象：
Uri uri = Uri.parse("content://com.example.app.provider/table1")

现在我们就可以使用这个uri对象来查询talbe1表中的数据了：
```
Cursor cursor = getContentResolver().query（
	uri,
	projection,
	selection,
	selectionArgs,
	sortOrder
）;
```

参数解释：
query()方法参数	对应SQL部分	描述
uri	from table_name	指定查询某个应用程序下的某个表
projection	select column1, column2	指定查询的列名
selection	where column=value	指定where约束条件
selectArgs	-	为where中的占位符提供具体的值
orderBy	order by column1, column2	指定查询结果的排序方式

查询完之后，就可以从游标中取值了：
```
if(cursor != null){
	while(cursor.moveToNext()) {
		String column1 = cursor.getString(cursor.getColumnIndex("column1"));
		int column2 = cursor.getInt(cursor.getColumnIndex("column2"));
	}
	cursor.close();
}
```

添加数据
```
ContentValues values = new ContentValues();
values.put(“column1”, "text");
values.put("column2", 1);
getContentResolver().insert(uri, values);
```

更新数据
```
ContentValues valuse = new ContentValues();
valuse.put("column1", "");
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", new String[]{"text", 1});
```

删除数据
```
getContentResolver().delete(uri , "column2 = ?", new String[]{ "1"});
```

### 读取系统联系人
读取系统联系人需要声明权限，如果系统是6.0以后的，需要申请运行时权限
```
if(ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS) 
	!= PackageManager.PERMISSION_GRANTED) {
		ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.READ_CONTACTS}, 1);
	}else {
		readContacts();  //读取联系人
	}

private void readContacts(){
	Cursor cursor = null;
	try{
		//查询联系人数据
		cursor = getContentResolver().query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI,null,null,null,null);
		if(cursor!=null){
			while(cursor.moveToNext()){
				//获取联系人姓名
				String name = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME));
				//获取联系人电话号码
				String number = cursor.getString(cursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
				list.add(name+"\n"+number);
			}
		}
	}catch(Exception e){
		e.printStackTrace()
	}finally{
		if(cursor != null){
			cursor.close();
		}
	}
}

@Override
public void onRequestPermissionResult(int requestCode, String[] permissions, int[] grantResults){
	switch(requestCode){
		case 1:
			if(grantResults.length >0 && grantResults[0] == PackageManager.PERMISSION_GRANTED){
				readContacts();
			}else {
				//您拒绝了权限
			}
	}
}
```


### 创建自己的内容提供器
创建自己的内容提供器，需要去继承 ContentProvider 类，ContentProvider 类中有6个抽象方法，我们在使用子类继承它的时候，需要将这6个方法全部重写。
```
public class MyProvider extends ContentProvider{
	@Override
	public boolean onCreate() {
		return false;
	}
	@Override
	public Cursor query(Uri uri, String[] projection, Stirng selection, String[] selectionArgs, String sortOrder){
		return null;
	}
	@Overrride
	public Uri insert(Uri uri , ContentValues values){
		return null;
	}
	@Override
	public int update(Uri uri, ContentValuse values, String selection, String[] selectionArgs){
		return 0;
	}
	@Override
	public int delete(Uri uri, String selection, String[] selectionArgs){
		return 0;
	}
	@Override
	public String getType(Uri uri）{
		return null；
	}
}
```



