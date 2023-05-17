## Android

### 1. Android初识

#### 1.1 目录结构

- mainifests子目录：下面只有一个XML文件，即AndroidMainfest.xml，它是App的运行配置文件

- Java子目录：下面有三个com.example.myapp包，其中第一个包存放当前模块的Java源代码，后面两个包存放测试用的Java代码

- res子目录：存放当前模块的资源文件，下有四个子目录

  - drawable目录存放图形描述文件与图片文件
  - mipmap目录存放App的启动图标
  - values目录存放一些常量定义文件，例如字符串常量`strings.xml`、像素常量`dimens.xml`、颜色常量`colors.xml`、样式风格定义`styles.xml`

- 配置文件参数大概说明：

  ```xml
      defaultConfig {
          // 指定该模块的应用编号，也就是App的包名
          applicationId "com.tom.myapplication"
          // 指定App适合运行的最小SDK版本号
          minSdk 24
          // 目标版本号，表示App最希望在哪个版本的Android上运行
          targetSdk 33
          // 指定App的应用版本号
          versionCode 1
          // 指定App的应用版本名称
          versionName "1.0"
  
          testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
      }
  
      dependencies {
          // 兼容性问题存放依赖
          implementation 'androidx.appcompat:appcompat:1.4.1'
  
          implementation 'com.google.android.material:material:1.5.0'
          implementation 'androidx.constraintlayout:constraintlayout:2.1.3'
          implementation 'androidx.navigation:navigation-fragment:2.4.1'
          implementation 'androidx.navigation:navigation-ui:2.4.1'
          testImplementation 'junit:junit:4.13.2'
          androidTestImplementation 'androidx.test.ext:junit:1.1.3'
          androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
      }
  ```

- `AndroidMainifest.xml`文件参数说明：

  ```xml
      <application
          // 进行刷机等操作时，是否允许应用进行备份 
          android:allowBackup="true"
          android:dataExtractionRules="@xml/data_extraction_rules"
          android:fullBackupContent="@xml/backup_rules"
          // 指定App在手机屏幕上显示的图标
          android:icon="@mipmap/ic_launcher"
          // 指定App在手机屏幕上显的圆角图标
          android:roundIcon=""
          // 手机App在屏幕上显示的名称
          android:label="@string/app_name"
          // 是否支持从右往左读的语言
          android:supportsRtl="true"
          // 指定App的风格
          android:theme="@style/Theme.MyApplication"
          tools:targetApi="31">
          <activity
              android:name=".MainActivity"
              android:exported="true"
              android:label="@string/app_name"
              android:theme="@style/Theme.MyApplication.NoActionBar">
              <intent-filter>
                  // 指定了打开程序，最先执行的Activity是哪个，也就是打开软件的首页是
                  <action android:name="android.intent.action.MAIN" />
  
                  <category android:name="android.intent.category.LAUNCHER" />
              </intent-filter>
          </activity>
      </application>
  ```


#### 1.2 设计规范

- 利用`XML`标记描绘应用界面，使用`Java`代码书写程序逻辑

### 2. 具体代码实现

#### 2.1 Activity创建与跳转

- 创建新的`App`页面有三个步骤
  1. 在`layout`目录下创建`XML`文件
  2. 创建与`XML`文件对应的`Java`代码
  3. 在`AndroidMainifest.xml`中注册页面配置

- 编译器提供了快速的创建方式：
  - 右键`Java`包 --> `New` --> `Activity` --> `EmptyActivity`，直接创建新的`Class`文件和`XML`文件

- 当前页跳转到新页面：

  ```java
  startActivity(new Intent(源页面.this, 目标页面.class));
  ```

- 当前页面回到上一个页面，相当于关闭当前页面：

  ```java
  finish();
  ```

#### 2.2 简单控件

##### 2.2.1 文本显示

- 设置文本内容有两种方式：
  - 在`XML`文件中通过属性`android:text`设置文本
  - 在`Java`代码中调用文本视图对象的`setText`方法设置文本

- 设置文本大小有两种方式：
  - 在`Java`代码中调用`setTextSize`方法，指定文本大小
  - 在`XML`中通过属性`android:textSize`指定文本大小，此时需要指定字号的单位
    - px：手机屏幕的最小显示单位，和设备的显示屏有关
    - dp：与设备无关的显示单位，置于屏幕的尺寸有关
    - sp：专门来设置字体的大小，在系统设置中可以调整字体大小
  - **一般就是用SP作为单位**

- 设置文本颜色：
  - `Java`代码中调用`setTextColor`方法设置文本颜色，具体色值可以用`Color`类中取
  - `XML`文件中通过`android:textColor`指定文本颜色

##### 2.2.2 视图

###### 2.2.2.1 设置视图的宽高

- 视图宽度通过属性`android:layout_width`表达
- 视图高度通过属性`android:layout_height`表达
- **宽高的取值有下面三种：**
  - `match_parent`：表示和上级视图保持一致
  - `wrap_content`：表示与内容自适应，字体多大显示多大
  - 以`dp`为单位的具体尺寸（可以设置组件的固定大小）

- 自定义组件的位置：
  - 首先确保`XML`中的宽高属性值为`warp_content`
  - 调用控件对象的`getLayoutParams`方法，获取该控件的布局参数
  - 布局参数的`width`表示宽度，`height`属性表示高度
  - 调用控件对象的`setLayoutParams`方法，填入修改后的布局参数使之生效

- 代码示例：

  ```java
  TextView test = findViewById(xxxx);
  ViewGroup.LayoutParams params = test.getLayoutParams();
  params.width = 100;
  params.height = 100;
  ```

###### 2.2.2.2 设置视图的间距

- 两种方式：
  - `layout_margin`属性，指定当前视图与周围平级视图之间的距离。（就是里模块和外面的边中间的距离，越小离得越近）
  - 包括：`layout_margin`、`layout_marginLeft`、`layout_marginTop`、`layout_marginRight`、`layout_marginBottom`
- 采用`padding`属性，它指定了当前视图与内部下级视图之间的距离。包括`padding`、`paddingLeft`、`paddingRight`、`paddingBottom`（我猜和上面一样，也是越小离得越近）

###### 2.2.2.3 设置视图的对齐方式

- `layout_gravity`属性，它指定了当前视图相对与上级视图的对齐方式
- `gravity`属性，它指定了下级视图相对与当前视图的对齐方式
- 这两个属性的取值包括：`left、top、right、bottom`，还可以用竖线连接取值，如：`left|top`

##### 2.2.3 线性布局LinearLayout

- `orientation`属性为`horizontal`时，内部视图在水平方向从左往右排列
- `orientation`属性为`vertical`时，内部视图在垂直方向上从上往下排列
- 如果不指定相关属性，则默认是水平方向排列
- 也可以使用权重进行页面的划分

##### 2.2.4 相对布局RelativeLayout

- 由下级视图相对的实现布局分布（几乎不用）

##### 2.2.5 网格布局

- 默认从左往右，从上到下排列，它新增了两个属性
- `columnCount`属性，它指定了网格的列，即每行能放多少个视图
- `rowCount`属性，它指定了网格的行，即每列能放多少个视图

##### 2.2.6 滚动视图ScrollView

- `ScrollView`，垂直方向的滚动视图；垂直方向滚动时，`layout_width`属性值设置为`match_parent`，`layout_height`属性值设置为`wrap_content`

- `HorizontalScrollView`，水平方向的滚动视图；水平方向滚动时，`layout_width`属性值设置为`wrap_content`，`layout_height`属性值设置为`match_parent`

##### 2.2.7 Button

- 拥有默认的按钮背景
- 内部文本默认居中对齐
- 默认将字母转为大写
- 与`TextView`相比，`Button`新增了两个属性：
  - `textAllCaps`属性，它制定了是否将英文字母转为大写，为true表示自动转为大写，false表示不做大写转换
  - `onClick`属性：用来接管用户的点击动作，制定了点击按钮是要触发哪个方法

###### 2.2.7.1 点击事件和长按事件

- 监听器：专门用于监听控件的动作行为。只有控件发生了指定的动作，监听器才会触发开关去执行对应的代码逻辑
- 按钮控件有两种常用的监听器
  - 点击监听器，通过`setOnClickListener`方法设置。按钮被按住少于500ms时，会触发点击事件
  - 长按监听器，通过`setOnLongClickListener`方法设置。按钮被按住超过500毫秒时，会触发长按事件

###### 2.2.7.2 按钮的禁用和恢复

- 是否可用由`enabled`属性控制，属性值为`true`时表示允许点击，为`false`时表示不允许点击

##### 2.2.8 ImageView

- 图像视图展示的图片通常位于 res/drawable*** 目录，设置图像视图的显示有两种方式
  - 在`XML`文件中，通过设置属性：`android：src`设置图片资源，属性值格式如：@drawable/不含扩展名的图片名称
  - 在Java代码中，调用`setImageResource`方法设置图片资源，方法参数格式形如：R.drawable.不含扩展名的图片名称

##### 2.2.9 ImageButton

- 继承自ImageView，而非继承Button

- ImageButton和Button之间的区别：

  - Button即可以显示文本也可以显示图片，ImageButton只能显示图片不能显示文本
  - ImageButton上的图像可以按照比例缩放，但Button通过背景设置的图像会拉伸变形
  - Button只能靠背景显示一张图片，而ImageButton可以分别在前景和背景显示图片，从而实现两张图片叠加的效果

- 如果想要一个按钮和一个图片坐样式，可以这样写

  ```xml
  <Button
          // 设置图片和文字距离5dp
          android：drawablePadding=“5dp”
          android：drawableLeft="图片路径"
          android:text="图片在左"/>
  ```

#### 2.3 活动Activity

##### 2.3.1 启动与结束

- 当前页跳转到新页面：

  ```java
  startActivity(new Intent(源页面.this, 目标页面.class));
  ```

- 当前页面回到上一个页面，相当于关闭当前页面：

  ```java
  finish();
  ```

##### 2.3.2 Activity的生命周期

- 一堆的内置方法：onCreate、onResume之类的

- 主要切换：
  - 打开新页面：
    - onCreate --> onStart --> onResume
  - 关闭旧页面：
    - onPause --> onStop --> onDestroy

##### 2.3.3 Activity启动模式

- 入栈出栈的顺序

- 确保每次只存在唯一的页面实例，减少了内存的损耗

  - 同时可以避免在使用时，出现多次返回还无法结束程序的情况，符合正常的业务逻辑

  - 代码示例：

    ```java
    Intent intent = new Intent(this, Target.class);
    intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    startActivity(intent);
    ```

- 用户登录成功后，不显示登录页，则使用如下做法：

  - 对于回不去的登录页面情况，可以设置标志`FLAG_ACTIVITY_CLEAR_TASK`，该标志会清空当前活动栈里的所有实例。不过全部清空之后，意味着当前栈无法使用了，必须另外找一个活动栈才可以，也就是同时设置启动标志`FLAG_ACTIVITY_NEW_TASK`，该标志用于开辟新任务的活动栈。则离开登录页面的跳转代码为如下：

    ```java
    Intent inten = new Intent(this, Target.class);
    intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK | Intent.FLAG_ACTIVITY_NEW_TASK);
    startActivity(intent);
    ```

#### 2.4 显式Intent和隐式Intent

- Intent式各个组件之间信息沟通的桥梁，它用于Android组件之间的通信，主要完成下列工作：
  - 标明本次通信请求从哪里来、到哪里去、怎么去
  - 发起方携带本次通信需要的数据内容，接收方从收到的意图中解析数据
  - 发起方若想判断接收方的处理结果，意图就要负责让接收方传回应答的数据内容

##### 2.4.1 显式Intent

- 直接指定来源活动和目的活动，属于精确匹配

```java
// 1. 在Intent的构造函数中指定
new Intent(this, Target.class);
// 2. 调用意图对象的setClass方法指定
intent.setClass(this, Target.class);
// 3. 调用意图对象的setComponent方法指定
ComponentName component = new ComponentName(this, Target.class);
intent.setComponent(component);

// 最终都是
startActivity(intent);
```

##### 2.4.2 隐式Intent

- 没有明确指定要跳转的目标活动，只给出一个动作字符串让系统自动匹配，属于模糊匹配

```java
Intent intent = new Intent();
// 设置意图动作为准备拨号---不同的动作有不同的意图
intent.setAction(Intent.ACTION_DIAL);
// 声明一个拨号的Uri
Uri uri = Uri.parse("tel:" + phoneNo);
intent.setData(uri);
startActivity(intent);

// 可以自定义 意图动作
<intent-filter>
    <action android:name="android.intent.action.NING"/>
    <category android:name="android.intent.category.DEFAULT"/>
</intent-filter>
// Java中代码需要这样写
intent.setAction(android.intent.action.NING);
intent.addCategory(Intent.CATEGORY_DEFAULT);
startActivity(intent);

// 注意点：需要吧引导XML的标签改为true，表示允许其他的APP打开
android:exported="true"
```

##### 2.4.3 Activity传递数据

- 向下一个Activity发送数据

  - Intent使用Bundle对象存放待传递的数据信息

  ```java
  // 创建一个新包裹
  Bundle bundle = new Bundle();
  bundle.putString("param1", "yourDate");
  bundle.putString("param2", "yourDate");
  intent.putExtras(bundle);
  startActivity(intent);
  
  // 接收方
  Bundle bundle = getIntent().getExtras();
  bundle.getString("param1");
  bundle.getString("param2");
  
  ```

- 向上一个Activity发送数据

  ```java
  // 下一个页面成功返回了数据，则在这个方法中进行处理，需要记住
          register = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> {
              if(result != null) {
                  Intent intent = result.getData();
                  if(intent != null && result.getResultCode() == Activity.RESULT_OK) {
                      Bundle bundle = getIntent().getExtras();
                      // 不晓得为啥，这里获取不到
                      String information = bundle.getString("information");
                  }
              }
          });
  
  
  // 发送方要用这个方法发送数据
  setResult(Activity.RESULT_OK, intent);
  ```
  

##### 2.4.4 Activity获取字符串配置信息

```java
getString(R.string.valueName);
```

##### 2.4.5 Activity获取元数据

- 在清单文件的`activity`标签下，配置属性：

  ```xml
  <!-- 这里既可以直接配置，也可以引用其他配置文件中的属性 -->
  <meta-data android:name="weather" android:value="晴天"/>
  ```

- 使用范围：
  - 例如使用第三方的SDK，整合第三方的工具包
- 使用方式：
  1. 调用`getPackageManager`方法获取当前应用的包管理器
  2. 调用包管理器的`getActivityInfo`方法获得当前活动的信息对象
  3. 活动信息对象的`metaData`是`Bundle`包裹类型，调用包裹对象的`getString`即可获得指定名称的参数值

> 拓展：元数据不仅能传递简单的字符串参数，还能传送更复杂的资源数据，如支付宝的快捷方式菜单等
>
> - 代码示例：
>
>   ```xml
>   // 快捷方式的XML代码示例
>   <?xml version="1.0" encoding="utf-8"?>
>   <shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
>       <shortcut
>           android:shortcutId="first"
>           android:enabled="true"
>           android:icon="@mipmap/ic_launcher"
>           android:shortcutShortLabel="@string/first"
>           android:shortcutLongLabel="@string/longLabel">
>           <!--指定点击了快捷方式跳转到哪个页面-->
>           <intent
>               android:action="android.intent.action.VIEW"
>               android:targetPackage="com.tom.chapter04"
>               android:targetClass="com.tom.chapter04.MetaDataActivity"/>
>           <!--快捷方式对话-->
>           <categories android:name="android.shortcut.conversation"/>
>       </shortcut>
>   </shortcuts>
>   
>   // 清单文件中需要指定meta_data
>   <meta-data android:name="android.app.shortcuts" android:resource="@xml/shortcuts"/>
>   ```
>
> - 注意点：shortCut的xml中无法直接绑定string字符串，需要从values文件中绑定

#### 2.5 中级控件

##### 2.5.1 提醒对话框

- ```java
  AlertDialog。Builder builder = new AlertDialog.Builder(this);
  builder.setTitle();
  builder.setMessage();
  // 确定
  builder.setPositiveButton("", ()->{
      // 点击确定后的触发事件
  });
  // 取消
  builder.setNegativeButton("", ()->{
      // 点击取消后的触发事件
  });
  ```

##### 2.5.2 日期选择器

- ```xml
  <DatePicker
              android: layout_width="match_Parent"
              android:layout_height="wrap_content"
              android:calendarViewShown="false"    // 标识不显示日历，只显示选择器
              android:datePickerMode="spinner"/>   //表示显示全部的（左边的选择器，后边的日历表）
  ```


> 其他组件不多赘述

#### 2.6 SharedPreferences存储

- 轻量级存储工具，采用Key-Value的键值方式存储
- 符合XML规范的配置文件。保存路径是：`/data/data/包名/shared_prefs/文件名.xml`
  - 私有目录

- 适用场景：
  - 简单且孤立的数据。如果是复杂而且相互间有关联的数据，则需要保存在数据库中
  - 文本形式的数据。若是二进制的数据，则要保存在文件中
  - 需要持久化存储的数据。在APP退出后再次启动时，之前保存的数据仍然有效
  - **实际开发中，共享参数经常存储的数据有App的个性化配置信息、用户使用App的行为信息、零食需要保存的片段信息等**

- 实际用法：

  ```java
  // 保存数据到文件
  SharedPreferences sp = getSharedPreferences("name", Context.MODE_PRIVATE);
  SharedPreferences.Editor editor = preferences.edit();
  editor.putString("name", "Tom");
  editor.putInt("age", 100);
  // 最后需要提交
  editor.commit();
  
  // 下次使用时需要从文件中读取出来
  // key--默认值，也就是说，如果从文件中读取不出东西来，那就以默认值填充
  String name = preferences.getString("name", null);
  if(name != null) {
      组件.setText(name);
  }
  ```

#### 2.7 具体控件使用说明

##### 2.7.1 RecyclerView控件

- 实现步骤：
  - 设计主界面Layout
  - 设计item layout
  - 设计Bean封装类
  - 设计Adapter
  - 设计MainActivity
    - 初始化（界面、数据）
    - 设置RecyclerView

- 关键方法：
  - RecyclerView.Adapter中的方法
    - onCreateViewHolder()：主要用于创建ViewHolder实例，加载item界面的布局文件
    - onBindViewHolder()：主要将获取的数据设置到对应的控件上
    - getItemCount()：获取列表条目的总数
  - RecycilerView中的方法：
    - setLayoutManager()：设置RecyclerView的布局方式
    - setAdapter()：设置RecyclerView的适配器





























 

























































































































