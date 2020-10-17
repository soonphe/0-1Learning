
调用方式一：获取dialog 的Window，然后调用setContentView方法
1.编写自定义Dialog View

2.程序中调用
~~~~
String info = cityData.getPointerList().get(position).toString();  
AlertDialog alertDialog = new AlertDialog.Builder(CityActivity.this).create();  
alertDialog.show();  

Window window = alertDialog.getWindow();  
window.setContentView(R.layout.dialog_main_info);  

TextView tv_title = (TextView) window.findViewById(R.id.tv_dialog_title);  
tv_title.setText(详细信息);  
TextView tv_message = (TextView) window.findViewById(R.id.tv_dialog_message);  
tv_message.setText(info);

~~~~

调用方式二：使用AlertDialog.Builder中的setView方法
~~~~
AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
View view = View.inflate(getActivity(), R.layout.custom_dialog, null);
builder.setView(view);
builder.setCancelable(true);

TextView title= (TextView) view.findViewById(R.id.title);//设置标题
EditText input_edt= (EditText) view.findViewById(R.id.dialog_edit);//输入内容
Button btn_cancel=(Button)view.findViewById(R.id.btn_cancel);//取消按钮
Button btn_comfirm=(Button)view.findViewById(R.id.btn_comfirm);//确定按钮

//取消或确定按钮监听事件处理
AlertDialog dialog = builder.create();
dialog.show();  
~~~~