# Android Note

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
