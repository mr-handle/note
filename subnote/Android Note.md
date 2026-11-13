# Android Note

## Android Studio

AVD：Android Virtual Device

### 安卓虚拟机镜像介绍

- Google Play Store 镜像
    - 内置了完整的 Google Mobile Services (GMS)，包括 Google Play 商店、Google Play 服务、Google 地图、登录等全套 Google 官方应用和 API
    - 体积大
    - 无法获取 Root 权限
    - 用于海外应用开发、Google 生态测试

- Android Open Source (AOSP) 镜像
    - 仅包含 Android 开源项目（Android Open Source Project）的原生系统，完全没有任何 Google 的专有应用或服务
    - 体积小
    - 支持获取 Root 权限
    - 用于国内应用开发、基础调试、去Google化测试

### 运行日志

- Android采用Log工具打印日志，各类日志划分为5个等级
    - Log.e：错误日志，比如可能导致程序崩溃的异常
    - Log.w：警告日志
    - Log.i：一般日志
    - Log.d：调试日志，可以把程序运行时的变量值打印出来，方便跟踪调试
    - Log.v：冗余日志

- 在Android Studio的左下角点击Logcat可以查看日志

```java
// debugLog是自定义标签，用于通过标签过滤日志信息
Log.d("debugLog", "app start!");
```

#### 安卓应用

```java
// 普通类MainActivity继承了AppCompatActivity就具有了Activity的特性了
// Activity就相当于一个窗口
public class MainActivity extends AppCompatActivity {
    // 安卓程序入口
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        // R：为每个资源文件按类别分配一个索引
        // 通过R.类别名.资源名操作对应资源
        // 在Android视图下为：res/layout/activity_main.xml
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });
    }
}
```

## 视图控件（View）

### 视图的宽高

layout_width、layout_height属性指定视图的宽高

- 属性值包括：
    - match_parent，表示与上级视图保持一致
    - wrap_content，表示与内容自适应
    - 以dp为单位的具体尺寸

### 视图的间距

layout_margin、layout_marginLeft、layout_marginTop、layout_marginRight、layout_marginBottom属性，指定当前视图与周围平级视图之间的距离

padding、paddingLeft、paddingTop、paddingRight、paddingBottom属性，指定当前视图与内部下级视图之间的距离

### 视图的对齐方式

layout_gravity属性指定当前视图相对于上级视图的对齐方式

gravity属性指定下级视图相对于当前视图的对齐方式

它们的属性值包括：left、top、right、bottom，还可以用竖线连接属性值，如："left|top"表示左上

## 布局

### LinearLayout（线性布局）

- orientation属性
    - vertical，内部视图在垂直方向从上往下排列
    - horizontal，内部视图在水平方向从左往右排列，默认排列方式

- layout_weight属性，指线性布局的下级视图各自拥有多大比例的宽高
    - layout_weight属性不在LinearLayout节点设置，而是在其直接下级视图设置
    - layout_width填0dp时，layout_weight表示水平方向的宽度比例
    - layout_height填0dp时，layout_weight表示垂直方向的高度比例

### RelativeLayout（相对布局）

相对布局的下级视图位置由其它视图（作为参照物）决定

- 作为参照物的视图分两种
    - 与该视图平级的视图
    - 该视图的上级视图（也就是该视图归属的RelativeLayout）

如果不设置参照物，那么该视图默认显示在RelativeLayout内部的左上角

|相对位置的属性|描述|
|:-|:-|
|layout_toLeftOf|当前视图在指定视图的左边|
|layout_toRightOf|当前视图在指定视图的右边|
|layout_above|当前视图在指定视图的上方|
|layout_below|当前视图在指定视图的下方|
|layout_alignLeft|当前视图与指定视图的左侧对齐|
|layout_alignRight|当前视图与指定视图的右侧对齐|
|layout_alignTop|当前视图与指定视图的顶部对齐|
|layout_alignBottom|当前视图与指定视图的底部对齐|
|layout_centerInParent|当前视图在上级视图中间|
|layout_centerHorizontal|当前视图在上级视图的水平方向居中|
|layout_centerVertical|当前视图在上级视图的垂直方向居中|
|layout_alignParentLeft|当前视图与上级视图的左侧对齐|
|layout_alignParentRight|当前视图与上级视图的右侧对齐|
|layout_alignParentTop|当前视图与上级视图的顶部对齐|
|layout_alignParentBottom|当前视图与上级视图的底部对齐|

写法例子：android:layout_toLeftOf="@id/指定视图的id"

### GridLayout（网格布局）

网格布局支持多行多列的表格排列，默认从左往右、从上到下排列

columnCount属性指定网格的列数，即每行能放多少个视图

rowCount属性指定网格的行数，即每列能放多少个视图

还可以用layout_columnWeight、layout_rowWeight这两个权重来来配置宽高比例

### 滚动视图

#### ScrollView

垂直滚动视图

layout_width设置为match_parent

layout_height设置为wrap_content

#### HorizontalScrollView

水平滚动视图

layout_width设置为wrap_content

layout_height设置为match_parent

## USB调试

手机开启开发者模式，然后设置开发者选项，打开USB调试，将数据线插上手机端和电脑端（也可以用无线连接进行调试），然后手机端要选择文件传输模式

## Android Debug Bridge(ADB)

- archlinux安装adb命令工具

```sh
sudo pacman -S android-tools
```

- 手机开启开发者模式并连接电脑（usb或者wifi）

```sh
# 如果输出设备是unauthorized，留意手机弹窗然后授权即可
adb devices

# 进入安卓设备内部的shell
# 这样就可以直接执行"pm enable 包名"，而不是每次都执行“adb shell pm enable 包名”
adb shell

# 退出adb shell
exit

# 进入adb shell后，获取root权限
su
```

- 可以通过手机的系统设置-Apps-点击某个具体的app-右上角点击菜单，然后查看app信息得到包名（APK name）

- adb常用命令

```sh
# 停用某个应用 
pm disable-user 包名

# 重新启用某个应用 
pm enable 包名

# 卸载某个系统应用，不加“--user 0”选项参数是真正的物理卸载，是需要root权限的
# --user 0：指定操作仅对主用户（也就是你）生效，加上此选项参数后，
# 并不会真正从系统中删除应用文件，而是将其从你的用户账户中移除，使其对你“不可见”，效果上等同于停用，并且可以随时恢复，相当于伪卸载
pm uninstall -k --user 0 包名

# 恢复（伪卸载）应用
pm install-existing 包名

# 仅列出第三方（用户自行安装）的应用
pm list packages -3

# 仅列出系统应用
pm list packages -s

# 模糊搜索包含关键词的包名
pm list packages 关键词

# 显示包名及其对应的 APK 文件路径
pm list packages -f
```
