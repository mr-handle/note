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
