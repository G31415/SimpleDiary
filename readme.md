# SimpleDiary

## 简述

这是我的课设项目。

本项目是一个简单的本地日记应用，不过它支持联网获取记录当天的地址与天气信息并自动记录。基本遵循Material Design Guidelines实现。

## 运行截图



![image-20210731144141377.png (927×853) (raw.githubusercontent.com)](https://raw.githubusercontent.com/MogoMec/SimpleDiary/main/img/image-20210731144141377.png)

![image-20210731144308622.png (916×840) (raw.githubusercontent.com)](https://raw.githubusercontent.com/MogoMec/SimpleDiary/main/img/image-20210731144308622.png)

## Actiyity

### MainActivity

启动活动，包括日记列表（RecyclerView）、日历、导航菜单、编辑按钮。承担包括读取日记数据库、日记重排、日历导航、刷新、数据统计等工作。

### DiaryActivity

浏览与编辑日记的活动，使用了ViewPager动态加载不同的fragment，一个负责浏览、一个负责编辑。

## View

### RecyclerView及DiaryAdapter

日记列表。DiaryAdapter监听子项点击与长按事件和数据适配。

### com.haibin.calendarview.CalendarView

[huanghaibin-dev/CalendarView](https://github.com/huanghaibin-dev/CalendarView)

第三方控件，能够动态绘制日历视图，进行标记等。在本APP中使用它标记已经有日记记录的日期，以及实现日期选择的功能。

## 数据库（db）

### DBHelper

用于数据库的创建升级，继承SQLiteOpenHelper抽象类，重写onCreate() 和 onUpgrade() 。

日记记录表使用自增长主键_id，除主键外有五个字段。

```java
    protected static final String DB_PRIMARY_KEY = "_id";
    protected static final String DB_TABLE_NAME = "diary";

    protected static final String DB_TABLE_COLUMN_MOOD = "mood";
    protected static final String DB_TABLE_COLUMN_CONTENT = "content";
    protected static final String DB_TABLE_COLUMN_DATE = "date";
    protected static final String DB_TABLE_COLUMN_WEATHER = "weather";
    protected static final String DB_TABLE_COLUMN_LOCATION = "location";
    public static final String SQL_CREATE_TABLE_DIARY =
            "CREATE TABLE " + DB_TABLE_NAME + "(_id integer primary key autoincrement,"//主键，自增长
            +DB_TABLE_COLUMN_MOOD+" text,"
            +DB_TABLE_COLUMN_CONTENT+" text,"
            +DB_TABLE_COLUMN_DATE +" integer,"
            +DB_TABLE_COLUMN_WEATHER+" text,"
            +DB_TABLE_COLUMN_LOCATION+" text);";

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(SQL_CREATE_TABLE_DIARY);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS " + DB_TABLE_NAME);
        onCreate(db);
    }
```

### DBManager

用于数据库管理，封装各种增删改查函数，使用单例模式。

### DiaryItem

日记记录数据对象，承担包括加载列表，存储中间信息的工作，同时封装了一些数据处理函数，如得到格式化日期，比较记录是否相同。

## Fragment

### DiaryEditorFragment 

编辑器模式，可编辑日记内容。

### DiaryViewerFragment

浏览模式fragment，不可编辑日记内容。

### CommonDialog 

抽象类，强制字类实现clickOnOkButton()，clickOnCancelButton()两个按钮点击方法。

### DiaryDeleteDialog 

删除日记提示dialog。

### DiarySaveDialog 

保存日记提示dialog，在有新内容时触发。

### ProfileDialog

用户名更改dialog，使用SharedPrefrence实现持久化存储。

## 高德SDK

[获取定位数据-获取位置-开发指南-Android 定位SDK | 高德地图API (amap.com)](https://lbs.amap.com/api/android-location-sdk/guide/android-location/getlocation)

[获取天气数据-获取地图数据-开发指南-Android 地图SDK | 高德地图API (amap.com)](https://lbs.amap.com/api/android-sdk/guide/map-data/weather/#note)

使用了其定位和天气获取API。

## 遇到的问题及解决

- 问题：键盘顶掉了一些view。

    解决：AndroidManifest.xml中在对应Activity标签中设置android:windowSoftInputMode="adjustPan"属性。

    ```xml
    <activity android:name=".DiaryActivity"    android:windowSoftInputMode="adjustPan"/>
    
    ```

- 问题：日记列表页卡片视图，日记缩略文字。
    解决：设置TextView属性，限制行数，多出内容用省略号表示。

    ```xml
    <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="内容"
                    android:maxLines="4"
                    android:ellipsize="end"
                    android:textSize="15dp"
                    android:layout_marginTop="10dp"
                    android:layout_marginLeft="15dp"
                    android:id="@+id/TV_content"/>
    ```
