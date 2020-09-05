# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 内容提供者content provider

Content Provider
对于每一个应用程序来说，如果想要访问内容提供器中共享的数据，就一定要借助ContentResolver 类，可以通过Context中的getContentResolver() 方法获取该类的实例。ContentResolver中提供了一系列的方法用于对数据进行CRUD操作，其中insert() 方法用于添加数据，update() 方法用于更新数据，delete() 方法用于删除数据，query() 方法用于查询数据。
不同于SQLiteDatabase，ContentResolver 中的增删改查都是接收一个URl参数，这个参数被称为内容URL。内容URL给内容提供器中的数据建立了唯一标识符，它主要由两部分组成：authority 和 path 。authority 是用于对不同的应用程序做区分的，一般为了避免冲突，都会采用程序包名的方式进行命名。path则是用于对同一应用程序中不同的表做区分，通常都会添加到authority后面：

content://com.example.app.provider/table1
content://com.example.app.provider/table2
1
2
在使用内容URL作为参数的时候，需要将URL转换成URL对象：

Uri uri = Uri.parse("content://com.example.app.provider/table1")
1
现在我们就可以使用这个uri对象来查询talbe1表中的数据了：

Cursor cursor = getContentResolver().query（
	uri,
	projection,
	selection,
	selectionArgs,
	sortOrder
）;
1
2
3
4
5
6
7
对应参数的解释：

query()方法参数	对应SQL部分	描述
uri	from table_name	指定查询某个应用程序下的某个表
projection	select column1, column2	指定查询的列名
selection	where column=value	指定where约束条件
selectArgs	-	为where中的占位符提供具体的值
orderBy	order by column1, column2	指定查询结果的排序方式
查询完之后，就可以从游标中取值了：

if(cursor != null){
	while(cursor.moveToNext()) {
		String column1 = cursor.getString(cursor.getColumnIndex("column1"));
		int column2 = cursor.getInt(cursor.getColumnIndex("column2"));
	}
	cursor.close();
}
1
2
3
4
5
6
7
增删改查
添加数据

ContentValues values = new ContentValues();
values.put(“column1”, "text");
values.put("column2", 1);
getContentResolver().insert(uri, values);
1
2
3
4
更新数据

ContentValues valuse = new ContentValues();
valuse.put("column1", "");
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", new String[]{"text", 1});
1
2
3
删除数据

getContentResolver().delete(uri , "column2 = ?", new String[]{ "1"});
1
实例.
读取系统联系人
读取系统联系人需要声明权限，如果系统是6.0以后的，需要申请运行时权限

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
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
创建自己的内容提供器
创建自己的内容提供器，需要去继承 ContentProvider 类，ContentProvider 类中有6个抽象方法，我们在使用子类继承它的时候，需要将这6个方法全部重写。

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
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
URI 的主要格式有以下两种

content://com.example.app.provider/table1
content://com.example.app.provider/table1/1

* : 表示匹配任意长度的任意字符
# : 表示匹配任意长度的数字

//一个能够匹配任意表的内容URI格式就可以写成:
content://com.example.app.provider/*
//一个能够匹配表中任意一行数据的内容URI格式就可以写成：
content://com.example.app.provider/table1/#

