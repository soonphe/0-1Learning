# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 碎片Fragment

### 碎片是什么

碎片（Fragment）是一种可以嵌入在活动当中的UI 片段，它能让程序更加合理和充分地利用大屏幕的空间，因而在平板上应用的非常广泛。
碎片和活动一样，都能包含布局，同样都有自己的生命周期。


### 碎片的使用方式

#### 碎片的简单用法
1.编写碎片布局R.layout.left_fragment
2.实现Fragment代码
例：
```
public class LeftFragment extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
        Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.left_fragment, container, false);
        return view;
    }
}
```
3.引用fragment
```
<fragment
    android:id="@+id/left_fragment"
    android:name="com.example.fragmenttest.LeftFragment"
    android:layout_width="0dp"
    android:layout_height="match_parent"
    android:layout_weight="1" />
```

#### 动态添加碎片

1.编写碎片布局R.layout.left_fragment
2.实现Fragment代码
3.在主Activity动态操作fragment
```
AnotherRightFragment fragment = new AnotherRightFragment();
FragmentManager fragmentManager = getFragmentManager(); //fragment管理器
FragmentTransaction transaction = fragmentManager.beginTransaction();   //开启事务
transaction.replace(R.id.right_layout, fragment);   //替换布局为fragment
transaction.commit();       //提交事务
```

#### 碎片添加返回栈
transaction.addToBackStack(null);

### 碎片和活动之间进行通信

活动调用碎片：
RightFragment rightFragment = (RightFragment) getFragmentManager().findFragmentById(R.id.right_fragment);

碎片调用活动：
MainActivity activity = (MainActivity) getActivity();
获取Context 对象时，也可以使用getActivity()方法，因为获取到的活动本身就是一个Context对象

碎片调用碎片：
先获取关联的活动，再获取另一个碎片

### 碎片的状态和回调
1. 运行状态
当一个碎片是可见的，并且它所关联的活动正处于运行状态时，该碎片也处于运行状态。

2. 暂停状态
当一个活动进入暂停状态时（由于另一个未占满屏幕的活动被添加到了栈顶），与它相关联的可见碎片就会进入到暂停状态。

3. 停止状态
当一个活动进入停止状态时，与它相关联的碎片就会进入到停止状态。或者通过调
用FragmentTransaction 的remove()、replace()方法将碎片从活动中移除，但有在事务提
交之前调用addToBackStack()方法，这时的碎片也会进入到停止状态。总的来说，进入
停止状态的碎片对用户来说是完全不可见的，有可能会被系统回收。

4. 销毁状态
碎片总是依附于活动而存在的，因此当活动被销毁时，与它相关联的碎片就会进入到销毁状态。
或者通过调用FragmentTransaction 的remove()、replace()方法将碎片从活
动中移除，但在事务提交之前并没有调用addToBackStack()方法，这时的碎片也会进入到销毁状态

回调方法：
1. onAttach()
当碎片和活动建立关联的时候调用。
2. onCreateView()
为碎片创建视图（加载布局）时调用。
3. onActivityCreated()
确保与碎片相关联的活动一定已经创建完毕的时候调用。
4. onDestroyView()
当与碎片关联的视图被移除的时候调用。
5. onDetach()
当碎片和活动解除关联的时候调用。
