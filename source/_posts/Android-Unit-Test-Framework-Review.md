layout: post
title: "Android Unit Test Framework Review"
date: 2014-04-16 11:15:28
tags:
- 单元测试
- 安卓单元测试
categories:
- 安卓

------

### Robolectric

网址：http://robolectric.org/

现在比较流行的测试框架，主要特点是：

-	对所有的内生的方法、SDK、资源进行模拟，不需要模拟器
-	不需要mock框架
-	可以直接在IDE中运行

示例：

```
@RunWith(RobolectricTestRunner.class)
public class MyActivityTest {

  @Test
  public void clickingButton_shouldChangeResultsViewText() throws Exception {
    MyActivity activity = Robolectric.setupActivity(MyActivity.class);

    Button button = (Button) activity.findViewById(R.id.button);
    TextView results = (TextView) activity.findViewById(R.id.results);

    button.performClick();
    assertThat(results.getText().toString()).isEqualTo("Robolectric Rocks!");
  }
}
```

### Espresso

网址：https://google.github.io/android-testing-support-library/docs/espresso/index.html

是Google提供一个UI测试框架，本身是用来做黑盒测试，但是用于Unit Testing也是很方便，它的特点是：

-	运行于真实的设备中或者模拟器中
-	速度可靠性较好
-	但是需要Android的开发能力较高一点

### Android Junit Runner

网址：https://google.github.io/android-testing-support-library/docs/androidjunitrunner-guide/index.html

是android里面最基础的测试框架，它的特点是：

-	支持Junit4语法
-	Instrumentation Registry
-	Test Filter
-	Test Timeout
-	Sharding of tests
-	RunListener supporting the testing cycle
-	Activity and Applicatioon life cycle monitoring
-	Intent Monitoring and Stubbing

### Android Junit Runner with ATSL

网址：https://google.github.io/android-testing-support-library/docs/rules/index.html

是android里面最基础的测试框架，它的特点是：

-	除了上面的之外还有很多Rules
	-	ActivityTestRules
	-	ServiceTestRules

### UI Automator

网址：https://google.github.io/android-testing-support-library/docs/uiautomator/index.html

是android提供的最底层的测试框架，它的特点是：

-	主要支持UI测试
-	可以跨APPs测试，比如经典的P2P测试（Driver->Host->User）
-	不能测试hybrid view，可以使用espresso替代

### Accessibility Checking

网址：https://google.github.io/android-testing-support-library/docs/accesibility-checking/index.html

是android用来检查view的可访问性的，比如是否可以点击或者是否可以使用一些助听设备等，可以使用的测试框架包括：

-	AndroidJunit
-	Espresso
