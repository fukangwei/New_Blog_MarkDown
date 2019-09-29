---
title: DatePicker日期选择器
categories: Android
abbrlink: 4761eb81
date: 2018-12-28 12:31:05
---
&emsp;&emsp;日期选择器可供我们使用的属性如下：

- `android:calendarTextColor`：日历列表的文本的颜色。
- `android:calendarViewShown`：是否显示日历视图。
- `android:datePickerMode`：组件外观，可选值有`spinner`和`calendar`。
- `android:dayOfWeekBackground`：顶部星期几的背景颜色。
- `android:dayOfWeekTextAppearance`：顶部星期几的文字颜色。
- `android:endYear`：设置最后一年(例如`2010`)。
- `android:firstDayOfWeek`：设置日历列表以星期几开头。
- `android:headerBackground`：整个头部的背景颜色。
- `android:headerDayOfMonthTextAppearance`：头部日期字体的颜色。
- `android:headerMonthTextAppearance`：头部月份的字体颜色。
- `android:headerYearTextAppearance`：头部年的字体颜色。
- `android:maxDate`：显示在这个日历视图的最大日期(`mm/dd/yyyy`格式)。
- `android:minDate`：显示在这个日历视图的最小日期(`mm/dd/yyyy`格式)。
- `android:spinnersShown`：是否显示`spinner`。
- `android:startYear`：设置第一年(例如`1994`年)。
- `android:yearListItemTextAppearance`：列表的文本出现在列表中。
- `android:yearListSelectorColor`：年列表选择的颜色。

&emsp;&emsp;`XML`文件如下：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
​
    <DatePicker
        android:id="@+id/datePicker"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:calendarViewShown="true"
        android:endYear="2027"
        android:startYear="2007" />
</LinearLayout>
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.DatePicker;
import android.widget.Toast;
import java.util.Calendar;
​
public class MainActivity extends AppCompatActivity {
    int year;
    int monthOfYear;
    int dayOfMonth;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        DatePicker dp_test = (DatePicker) findViewById(R.id.datePicker);
        Calendar calendar = Calendar.getInstance();
        year = calendar.get(Calendar.YEAR);
        monthOfYear = calendar.get(Calendar.MONTH);
        dayOfMonth = calendar.get(Calendar.DAY_OF_MONTH);
        dp_test.init(year, monthOfYear, dayOfMonth, new DatePicker.OnDateChangedListener() {
            @Override
            public void onDateChanged(DatePicker view, int year, int monthOfYear, int dayOfMonth) {
                MainActivity.this.year = year;
                MainActivity.this.monthOfYear = monthOfYear;
                MainActivity.this.dayOfMonth = dayOfMonth;
                show(year, monthOfYear, dayOfMonth);
            }
        });
    }

    private void show(int year, int monthOfYear, int dayOfMonth) {
        String str = year + "年" + (monthOfYear + 1) + "月" + dayOfMonth + "日";
        Toast.makeText(MainActivity.this, str, Toast.LENGTH_SHORT).show();
    }
}
```