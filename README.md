# GregorianLunarCalendar
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-GregorianLunarCalendar-brightgreen.svg?style=flat)](http://android-arsenal.com/details/1/3818)<br>

`GregorianLunarCalendar`提供了农历+公历的日期选择模式，同时支持公历+农历的无缝切换

## 控件截图
![Example Image][1]<br>

效果图

![Example Image][2]<br>

静态截图以及渐变效果

## 说明
### 上一个版本
上一个版本的`GregorianLunarCalendar`通过`NumberPicker`构造的日期选择器，可定制性较差，如：<br>
1.每个`NumberPicker`只能显示3个选项;<br>
2.`setValue()`以及滑动时时没有过渡动效;<br>
3.滑动fling的速度较慢等等。<br>

### 最新版本
前段时间抽空写了一个`NumberPickerView`控件，算是一个比原生的`NumberPicker`在使用上更加优美的控件，`NumberPickerView`的项目地址在此处：
https://github.com/Carbs0126/NumberPickerView<br>


此版本的`GregorianLunarCalendar`只是使用`NumberPickerView`替换`NumberPicker`，做成了一个更加优美的日期选择控件。
由于我将`NumberPicker`中常用的方法如`setValue()` `setMinValue()` `setMaxValue()` `setDisplayedValues()`等函数均在`NumberPickerView`中实现了，因此移植起来非常简单。
`GregorianLunarCalendar`控件效果如下：

## 具有的特性
### UI显示效果的改进：
1.内容的重新设定时可以选择是否添加动效，即从公历转换到农历时，dayPicker肯定是需要重新设置value的，如果设置为添加动效，那么在切换时可以看到滚动效果；<br>
2.在切换年/月时，如果月/日需要改变时（如每个月的天数不同），具有滚动效果，且滚动的路径是选择最短路径；<br>
### 具有的功能：
1.公历年月日选择，年月改变时会联动改变相对应的月日时期显示，确保公历日期显示正确；<br>
2.农历年月日选择，同样会有联动改变对应日期的效果，确保日期符合农历历法，包括闰月、大小月等；<br>
3.公历农历互相转换时，实现无缝切换，如2016年2月29日切换为农历则显示二零一六年一月廿二日，农历转公历同样效果；<br>
4.显示范围为1901年-2100年，满足大部分使用需求；<br>
5.在1901年与2100年，确保公历农历转换时的边界限制。<br>

## 使用方法：<br>
1.xml中声明：
```
    <cn.carbs.android.gregorianlunarcalendar.library.view.GregorianLunarCalendarView
        android:id="@+id/calendar_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:glcv_GregorianThemeColor="@color/colorGregorian"//公历主题颜色
        app:glcv_LunarThemeColor="@color/colorLunar"//农历主题颜色
        app:glcv_NormalTextColor="#FF777777"//文字颜色
        app:glcv_ScrollAnimation="true"/>//是否开启滚动动画效果，默认true
```
2.java代码中的使用：
```java
  //找到View
  GregorianLunarCalendarView mGLCView = (GregorianLunarCalendarView) this.findViewById(R.id.calendar_view)
  
  //3种方式进行初始化：
  //1.默认日期今天，默认模式公历
  mGLCView.init();
  //2.指定日期，默认模式公历
  mGLCView.init(Calendar c);
  //3.指定日期，指定公历/农历模式。这里的公历模式/农历模式是指显示时采用的显示方式，
  //  和前面的参数Calendar无关，无论使用Calendar还是ChineseCalendar，GregorianLunarCalendarView需要的只是某一指定的年月日。
  mGLCView.init(Calendar c, boolean isGregorian);
  
  //获取数据：
  //采用GregorianLunarCalendarView.CalendarData内部类来存储当前日期，使用getCalendarData()函数获取选中date数据
  GregorianLunarCalendarView.CalendarData calendarData = mGLCView.getCalendarData();
  //进一步获取日期
  Calendar calendar = calendarData.getCalendar();//返回的是ChineseCalendar对象
  //若当前时间是： 公历2016年06月20日 农历二零一六年五月十六，那么获取的返回值如下：
  int yearG = calendar.get(Calendar.YEAR);//获取公历年 2016
  int monthG = calendar.get(Calendar.MONTH) + 1;//获取公历月 6
  int dayG = calendar.get(Calendar.DAY_OF_MONTH);//获取公历日 20
  int yearL = calendar.get(ChineseCalendar.CHINESE_YEAR);//获取农历年 2016
  int monthL = calendar.get(ChineseCalendar.CHINESE_MONTH));//获取农历月 5//注意，如果是闰五月则返回-5
  int dayL = calendar.get(ChineseCalendar.CHINESE_DATE);//获取农历日 20
  
  //添加日期改变的回调
  mGLCView.setOnDateChangedListener(new GregorianLunarCalendarView.OnDateChangedListener(){
        @Override
        public void onDateChanged(GregorianLunarCalendarView.CalendarData calendarData) {
            Calendar calendar = calendarData.getCalendar();
            String showToast = "Gregorian : " + calendar.get(Calendar.YEAR) + "-"
                         + (calendar.get(Calendar.MONTH) + 1) + "-"
                         + calendar.get(Calendar.DAY_OF_MONTH) + "\n"
                         + "Lunar     : " + calendar.get(ChineseCalendar.CHINESE_YEAR) + "-"
                         + (calendar.get(ChineseCalendar.CHINESE_MONTH)) + "-"
                         + calendar.get(ChineseCalendar.CHINESE_DATE);
            mChangedDate.setText(showToast);
           }
        }
    );
```
3.自定义属性说明：
```
    //自定义属性包括如下几种
    <declare-styleable name="GregorianLunarCalendarView">
        <attr name="glcv_ScrollAnimation" format="reference|boolean" />//切换公农历模式时，是否开启动画效果
        <attr name="glcv_GregorianThemeColor" format="reference|color" />//公历主题颜色
        <attr name="glcv_LunarThemeColor" format="reference|color" />//农历主题颜色
        <attr name="glcv_NormalTextColor" format="reference|color" />//普通状态下的文字颜色
    </declare-styleable>
```
注：<br>
sample中的公农历切换控件使用的是`IndicatorView`，项目地址在此处：
https://github.com/Carbs0126/IndicatorView

## TODO
由于这种选择样式是滚动选择，因此，不支持设置日期选择区间。如果想要控制日期选择的区间，请使用DatePicker或其他第三方控件，当然我现在正打算写一个DatePicker控件。

## License

    Copyright 2016 Carbs.Wang (GregorianLunarCalendar)

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.


[1]: https://github.com/Carbs0126/Screenshot/blob/master/gregorian_refine.gif
[2]: https://github.com/Carbs0126/Screenshot/blob/master/gregorian.jpg
