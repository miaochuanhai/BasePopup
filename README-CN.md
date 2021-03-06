# BasePopup
抽象出一个方便自定义的Basepopup类


##请注意：

因为在使用过程中发现了一些方法的命名存在误导性，这个问题是因为在初期写下了方法名之后一直忘了改，因此在 **v 1.3.0**之后这些名字将会改变，具体改动如下：

| 原方法名 | 现方法名 | 备注 |
| -------- | :-----------: | :---------: |
| getPopupView() | onCreatePopupView() | 本方法改名为的是减少误导性 |
| getAnimaView() | initAnimaView() | 理由同上 |
| mContext | getContext() | context将会改为private，需要使用方法获取 |
| mPopupView | getPopupWindowView() | mPopupView将会改为private，需要用方法获取 |
| getShowAnimation()/getExitAnimation() |**【protect】** initShowAnimation()/initExitAnimation() | getShowAnimation()/getExitAnimation()将会改为获取进行过初始化的animation |
| getShowAnimator()/getExitAnimator() |**【protect】** initShowAnimator()/initExitAnimator() | getShowAnimator()/getExitAnimator()将会改为获取进行过初始化的animator |


事实上在下也清楚有很多方法命名还是不太好，如果您有更好的命名欢迎提交pr，同时这次的改动对您的使用造成万分不便，在下深表歉意，希望得到您的谅解。


---

### 最低SDK版本要求 : API 11

# 依赖  [![](https://jitpack.io/v/razerdp/BasePopup.svg)](https://jitpack.io/#razerdp/BasePopup)
**Step 1.**  添加Jitpack到您的root gradle

Add it in your root build.gradle at the end of repositories:
```xml
	allprojects {
		repositories {
			...
			maven { url "https://jitpack.io" }
		}
	}
```	

**Step 2.** 添加依赖
```xml
	dependencies {
	        compile 'com.github.razerdp:BasePopup:v1.3.0'
	}
```

# 使用方法

----------

**Step 1:**
像您平时定制activity布局文件一样定制您的popup布局

etc.
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#8f000000">

    <RelativeLayout
        android:id="@+id/popup_anima"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/bg_dialog"
        android:layout_centerInParent="true"
        android:layout_margin="25dp">
        <TextView
            android:id="@+id/title"
            android:layout_width="match_parent"
            android:layout_height="50dp"
            android:textColor="#3dd1a5"
            android:text="This is title"
            android:gravity="left|center_vertical"
            android:textSize="18sp"
            android:paddingLeft="15dp"
            />
        <View
            android:id="@+id/line"
            android:layout_width="match_parent"
            android:layout_height="0.5dp"
            android:background="#3dd1a5"
            android:layout_below="@id/title"
            android:layout_marginLeft="5dp"
            android:layout_marginRight="5dp"/>
        <TextView
            android:id="@+id/content"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_below="@id/line"
            android:paddingTop="20dp"
            android:paddingLeft="15dp"
            android:paddingRight="15dp"
            android:paddingBottom="15dp"
            android:textSize="14sp"
            android:textColor="#1a1a1a"
            android:lineSpacingMultiplier="1.2"
            android:text="Warning:nuclear silo detected.\nWarning:nuclear silo detected.\nWarning:nuclear silo detected."
            />
        <View
            android:id="@+id/line2"
            android:layout_width="match_parent"
            android:layout_height="0.5dp"
            android:layout_below="@id/content"
            android:layout_marginLeft="15dp"
            android:layout_marginRight="15dp"
            android:background="@color/line_bg"
            android:layout_marginTop="15dp"/>
        <TextView
            android:id="@+id/cancel"
            android:layout_width="60dp"
            android:layout_height="40dp"
            android:gravity="center"
            android:padding="3dp"
            android:textColor="#bfbfbf"
            android:textSize="14sp"
            android:layout_below="@id/line2"
            android:layout_alignParentRight="true"
            android:layout_marginRight="15dp"
            android:text="CANCEL"/>
        <TextView
            android:id="@+id/ok"
            android:layout_width="60dp"
            android:layout_height="40dp"
            android:gravity="center"
            android:padding="3dp"
            android:textColor="#3dd1a5"
            android:textSize="14sp"
            android:layout_below="@id/line2"
            android:layout_toLeftOf="@id/cancel"
            android:layout_marginRight="15dp"
            android:text="OK"/>

    </RelativeLayout>
</RelativeLayout>
```
![image](https://github.com/razerdp/BasePopup/blob/master/img/etc.png)


**Step 2:**
新建一个类继承Basepopup

**Step 3:**
实现必要的几个方法

例如

```java
public class DialogPopup extends BasePopupWindow implements View.OnClickListener{

    private TextView ok;
    private TextView cancel;

    public DialogPopup(Activity context) {
        super(context);

        ok= (TextView) findViewById(R.id.ok);
        cancel= (TextView) findViewById(R.id.cancel);

        setViewClickListener(this,ok,cancel);
    }

    @Override
    protected Animation initShowAnimation() {
        AnimationSet set=new AnimationSet(false);
        Animation shakeAnima=new RotateAnimation(0,15,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
        shakeAnima.setInterpolator(new CycleInterpolator(5));
        shakeAnima.setDuration(400);
        set.addAnimation(getDefaultAlphaAnimation());
        set.addAnimation(shakeAnima);
        return set;
    }

    @Override
    protected View getClickToDismissView() {
        return getPopupWindowView();
    }

    @Override
    public View onCreatePopupView() {
        return createPopupById(R.layout.popup_dialog);
    }

    @Override
    public View initAnimaView() {
        return findViewById(R.id.popup_anima);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.ok:
                Toast.makeText(getContext(),"click the ok button",Toast.LENGTH_SHORT).show();
                break;
            case R.id.cancel:
                Toast.makeText(getContext(),"click the cancel button",Toast.LENGTH_SHORT).show();
                break;
            default:
                break;
        }

    }
}
```

**Step 4:**把您刚才实现的popup给new出来并调用show方法

例如

```java
    DialogPopup popup = new DialogPopup(context);
    popup.showPopupWindow();
```

# 一些例子
![image](https://github.com/razerdp/BasePopup/blob/master/img/comment_popup_with_exitAnima.gif)
![image](https://github.com/razerdp/BasePopup/blob/master/img/scale_popup.gif)
![image](https://github.com/razerdp/BasePopup/blob/master/img/slide_from_bottom_popup.gif)
![image](https://github.com/razerdp/BasePopup/blob/master/img/input_popup.gif)
![image](https://github.com/razerdp/BasePopup/blob/master/img/list_popup.gif)
![image](https://github.com/razerdp/BasePopup/blob/master/img/menu_popup.gif)

例子更新日志:

https://github.com/razerdp/BasePopup/blob/master/UpdateLog.md

# 方法介绍：
本项目拥有的方法如下：

 - 必须实现的抽象方法：
	+ onCreatePopupView()：得到popupwindow的主体，一般是在xml文件写好然后inflate出来并返回，推荐使用createPopupById()方法以减少代码
	+ initAnimaView()：得到用于展示动画的view，一般为了做到蒙层效果，我们的xml都会给一个灰色半透明的底层然后才是给定展示的popup（详情见demo）
	+ initShowAnimation()：展示popup的动画
	+ getClickToDismissView()：点击触发dismiss的view
 - 非必须实现的公有方法：
	+ initShowAnimator()：同getShowAnimation，不过有些时候用animator更加的丰富
	+ getInputView()：得到给定需要输入的view，一般用于包含edittext的popup
	+ initExitAnimation()：popup执行dismiss时的退出动画
	+ initExitAnimator()：同上
	+ setAutoShowInputMethod()：是否自动弹出输入法
	+ setAdjustInputMethod()：popup是否随着输入法弹出而自适应
	+ getPopupViewById()：工具方法，不用写那么多LayoutInflate.from(context)
	+ setViewClickListener()：工具方法，用于方便您设置onClickListener（多个View共用一个listener哦）
	+ setNeedPopupFade()：设置popup是否淡入淡出，默认为淡入淡出(这个参数将会对整个popup动画哦)
	+ setPopupAnimaStyle()：设定您喜欢的popup动画style(就跟您平时使用popup一样弄得动画style)
	+ 以及各种getter/setter
 - show方法：
	+ showPopupWindow():默认将popup显示到当前窗口
	+ showPopupWindow(int res)：将popup显示到对应的id控件上
	+ showPopupWindow(View v)：将popup显示到view上
 - 一些别的方法：
 	+ setPopupWindowFullScreen(boolean)：popup是否可以覆盖状态栏（全屏）

# 代码解析：
http://www.jianshu.com/p/069f57e14a9c

#License
MIT
