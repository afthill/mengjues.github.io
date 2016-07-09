title: Android Unit Test Without Emulator
date: 2015-12-19 21:46:47
tags:
- 单元测试
- 安卓单元测试
categories:
- 安卓

---

### 项目地址

<http://robolectric.org/>

### 项目介绍

想直接在Android IDE中运行andorid test？会得到如下结果：

```
java.lang.RuntimeException: Stub!
```

如果引入了TDD的开发模式，在android中就需要不停启动关闭emulator，因为ide没有办法直接模拟运行android test cases，所有才有了Robolectric方案：

#### 示例App

1. layout

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
  xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

<Button
  android:id="@+id/login"
  android:text="Login"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"/>
</LinearLayout>
```

2. main activity

```
public class WelcomeActivity extends Activity {
  @Overrid
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.welcome_activity);

    final View button = findViewById(R.id.login);
    button.setOnClickListener(new View.OnClickListener() {

      @Override
      public void onClick(View view) {
        startActivity(new Intent(WelcomeActivity.this, LoginActivity.class));
      }
    });
  }
}
```

#### 测试用例，直接在ide里执行

```
@RunWith(RobolectricTestRunner.class)
public class WelcomeActivityTest {
  @Test
  public void clickingLogin_shouldStartLoginActivity() {
    WelcomeActivity activity = Robolectric.setupActivity(WelcomeActitity.class);
    activity.findViewById(R.id.login).performClick();
    Intent expectedIntent = new Intent(activity, WelcomeActivity.class);
    assertThat(shadowOf(activity).getNextStartedActivity()).isEqualTo(expectedIntent);
  }
}
```
