# Intent隐式打开网页

## 1.实验结果
<div align="center">
<img src="https://github.com/MartinZou0/Android_EXP_5/blob/master/shortcut/1.jpg" width="300"/><img src="https://github.com/MartinZou0/Android_EXP_5/blob/master/shortcut/2.jpg" width="300"  />
</div>

## 2.注意事项

```xml
需要在配置清单文件中加入访问权限
<uses-permission android:name="android.permission.INTERNET"></uses-permission>

Activity的跳转也需要在清单文件中设置
   <activity android:name=".WebViewActivity" >
            <intent-filter>
                <action android:name="com.action.webview"/>
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="com.action.webviewcategory" />
            </intent-filter>
        </activity>
```

```java
//webview需要进行进一步的设置
public class WebViewActivity extends AppCompatActivity{
    private WebView webView;
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.webview);
        webView=(WebView)findViewById(R.id.webview);
        String url=getIntent().getExtras().getString("url");
        webView.loadUrl(url);
        // 覆盖WebView默认通过第三方或者是系统浏览器打开网页的行为，使得网页可以在WebVIew中打开
        webView.setWebViewClient(new WebViewClient(){

            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                // TODO Auto-generated method stub
                //返回值是true的时候控制网页在WebView中去打开，如果为false调用系统浏览器或第三方浏览器去打开
                view.loadUrl(url);
                return true;
            }
            //WebViewClient帮助WebView去处理一些页面控制和请求通知

        });
        //启用支持JavaScript
        WebSettings settings = webView.getSettings();
        settings.setJavaScriptEnabled(true);
        //WebView加载页面优先使用缓存加载
        settings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);
    }
}

```

```java
package com.example.martinzou.android_exp_6;

import android.content.Intent;
import android.net.Uri;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

//主活动源代码
public class MainActivity extends AppCompatActivity {
    private Button button;
    private EditText editText;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        button=(Button)findViewById(R.id.button);
        editText=(EditText)findViewById(R.id.editText) ;
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //对intent进行设置
                Intent intent=new Intent("com.action.webview");
                String url="http://"+editText.getText().toString();
                intent.addCategory("com.action.webviewcategory");
                intent.putExtra("url",url);
                startActivity(intent);
            }
        });
    }
}
```

## 3.Intent及WebView常用属性

### Intent属性

Intent作为联系各Activity之间的纽带，其作用并不仅仅只限于简单的数据传递。通过其自带的属性，其实可以方便的完成很多较为复杂的操作。例如直接调用拨号功能、直接自动调用合适的程序打开不同类型的文件等等。诸如此类，都可以通过设置Intent属性来完成。

Intent主要有以下四个重要属性，它们分别为：

**Action**：Action属性的值为一个字符串，它代表了系统中已经定义了一系列常用的动作。通过setAction()方法或在清单文件AndroidManifest.xml中设置。默认为：DEFAULT。

**Data：**Data通常是URI格式定义的操作数据。例如：tel:// 。通过setData()方法设置。

**Category：**Category属性用于指定当前动作（Action）被执行的环境。通过addCategory()方法或在清单文件AndroidManifest.xml中设置。默认为：CATEGORY_DEFAULT。

**Extras**：Extras属性主要用于传递目标组件所需要的额外的数据。通过putExtras()方法设置。

 四个属性各自的常用值如下所示：

**常用Action：**

|    Action     | 说明                                                         |
| :-----------: | ------------------------------------------------------------ |
| ACTION_MAIN： | Android Application的入口，每个Android应用必须且只能包含一个此类型的Action声明。 |
| ACTION_VIEW： | 系统根据不同的Data类型，通过已注册的对应Application显示数据。 |
| ACTION_EDIT： | 系统根据不同的Data类型，通过已注册的对应Application编辑示数据。 |
| ACTION_DIAL： | 打开系统默认的拨号程序，如果Data中设置了电话号码，则自动在拨号程序中输入此号码。 |
|  ACTION_CALL  | 直接呼叫Data中所带的号码。                                   |
| ACTION_SEND： | 由用户指定发送方式进行数据发送操作。                         |

**常用Category：**

|       Category        | 说明                                                         |
| :-------------------: | ------------------------------------------------------------ |
|  CATEGORY_DEFAULT：   | Android系统中默认的执行方式，按照普通Activity的执行方式执行。 |
|    CATEGORY_HOME：    | 设置该组件为Home Activity。                                  |
| CATEGORY_PREFERENCE： | 设置该组件为Preference。                                     |
|  CATEGORY_LAUNCHER：  | 设置该组件为在当前应用程序启动器中优先级最高的Activity，通常为入口ACTION_MAIN配合使用。 |
| CATEGORY_BROWSABLE：  | 设置该组件可以使用浏览器启动。                               |
|   CATEGORY_GADGET：   | 设置该组件可以内嵌到另外的Activity中。                       |

**常用Extras:**

|     Extras      | 说明                             |
| :-------------: | -------------------------------- |
|   EXTRA_BCC：   | 存放邮件密送人地址的字符串数组。 |
|   EXTRA_CC：    | 存放邮件抄送人地址的字符串数组。 |
|  EXTRA_EMAIL：  | 存放邮件地址的字符串数组。       |
| EXTRA_SUBJECT： | 存放邮件主题字符串。             |
|  EXTRA_TEXT：   | 存放邮件内容。                   |

但是常用的一般都是自己定义的键值对

```java
putExtra(String,byte)      putExtra(String,byte[])   putExtra(String,short) putExtra(String,short[])   putExtra(String,int)      putExtra(String,int[])   putExtra(String,long)      putExtra(String,long[])   putExtra(String,float) putExtra(String,float[])   putExtra(String,double)   putExtra(String,double[])   putExtra(String,boolean)   putExtra(String,boolean[])  putExtra(String,char) putExtra(String,char[])    putExtra(String,string)  putExtra(String,string[]) putExtra(String,Bundle)  putExtra(String,Serializable)      putExtras(Bundle)
```

**常用Data:**

|     Data     | 说明                               |
| :----------: | ---------------------------------- |
|   tel://：   | 号码数据格式，后跟电话号码。       |
| mailto://：  | 邮件数据格式，后跟邮件收件人地址。 |
|  smsto://：  | 短息数据格式，后跟短信接收号码。   |
| content://： | 内容数据格式，后跟需要读取的内容。 |
|  file://：   | 文件数据格式，后跟文件路径。       |



```java
// 拨打电话114 (设置data为Uri格式的数据)
Intent intent = new Intent();
intent.setAction(Intent.ACTION_DIAL);
intent.setData(Uri.parse("tel://114"));
startActivity(intent);
// 发送短信给10086 (设置data为Uri格式的数据)
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SENDTO);
intent.setData(Uri.parse("smsto://10086"));
startActivity(intent);
```





