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
