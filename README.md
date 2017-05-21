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
NodePadProvider
```
       public void onCreate(SQLiteDatabase db) {
           db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
                   + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
                   + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " TEXT"
                   + ");");
       }
```
获取时间戳
```
        // Gets the current system time in milliseconds
        //Long now = Long.valueOf(System.currentTimeMillis());
        SimpleDateFormat formatter    =   new    SimpleDateFormat    ("yyyy-MM-dd HH:mm:ss");
        Date curDate    =   new Date(System.currentTimeMillis());//获取当前时间
        String    now    =    formatter.format(curDate);
```
在NoteList的PROJECTION加一列
```
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_CREATE_DATE,
    };
```
绑定item，SimpleCursorAdapter中，dataCloums保存列名
```java
    private SimpleCursorAdapter adapter;
    ···
    //SimpleCursorAdapter adapter
    adapter  = new SimpleCursorAdapter(
               this,                             // The Context for the ListView
               R.layout.noteslist_item,          // Points to the XML for a list item
               cursor,                           // The cursor to get items from
               dataColumns,
               viewIDs
     );

        // Sets the ListView's adapter to be the cursor adapter that was just created.
        setListAdapter(adapter);
    }
````
改dataCloums
```
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE,NotePad.Notes.COLUMN_NAME_CREATE_DATE,} ;
        int[] viewIDs = { android.R.id.text1,R.id.text2,};
```
完成后如下图。
![](https://github.com/ih8rain/NotePad-master/blob/master/images/%E6%97%B6%E9%97%B4%E6%88%B3.png)
