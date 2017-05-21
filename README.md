# NotePad
功能如下：
* 添加时间戳
* 查询
* 修改背景色

## 1.添加时间戳
在notelist_item.xml中添加一个textview
```java
    <TextView
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:singleLine="true"
        android:id="@+id/text2"
        android:paddingLeft="20dip"
        android:gravity="bottom"
        android:textColor="@android:color/darker_gray"
        android:layout_alignLeft="@android:id/text1"/>
```
