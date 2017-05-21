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
将数据绑定item，SimpleCursorAdapter中，dataCloums保存列名
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
### ![](https://github.com/ih8rain/NotePad-master/blob/master/images/%E6%97%B6%E9%97%B4%E6%88%B3.png)
## 2.查询
在菜单中添加按钮
```
   <item android:id="@+id/menu_search"
        android:icon="@drawable/ic_menu_compose"
        android:title="@string/menu_add"
        android:alphabeticShortcut='s'
        android:showAsAction="always" />
    <item android:id="@+id/menu_all"
        android:icon="@drawable/ic_menu_revert"
        android:title="@string/menu_add"
        android:alphabeticShortcut='A'
        android:showAsAction="always"
     />
```
在NoteList中，创建一个SearchView成员变量
```
private SearchView searchView;
```
在onCreateOptionsMenu里面初始化样式
```
   public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater inflater = getMenuInflater();
        inflater.inflate(R.menu.list_options_menu, menu);
        setSearchView(menu);//搜索框

        Intent intent = new Intent(null, getIntent().getData());
        intent.addCategory(Intent.CATEGORY_ALTERNATIVE);
        menu.addIntentOptions(Menu.CATEGORY_ALTERNATIVE, 0, 0,
                new ComponentName(this, NotesList.class), null, intent, 0, null);

        return super.onCreateOptionsMenu(menu);
    }
```
```
    public void setSearchView(Menu menu) {
        MenuItem item = menu.getItem(0);
        searchView = new SearchView(this);
        item.setActionView(searchView);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
            item.collapseActionView();
        }
        searchView.setQuery("", false);
        searchView.setIconifiedByDefault(true);
        searchView.setQueryHint("Search");
```
完成后如下图。
### ![](https://github.com/ih8rain/NotePad-master/blob/master/images/%E6%9F%A5%E8%AF%A2.png)
## 3.修改背景色
为了保存用户修改过的背景色，新建一个PreferencesService类。
```
public class PreferencesService {
    private Context context;

    public PreferencesService(Context context) {
        this.context = context;
    }

    public void save(int color,int sortType) {
        //获得SharedPreferences对象
        SharedPreferences preferences = context.getSharedPreferences("pdata", Context.MODE_PRIVATE);
        SharedPreferences.Editor editor = preferences.edit();
        editor.putInt("color", color);
        editor.putInt("sort", sortType);
        editor.commit();
    }

    public Map<String, String> getPerferences() {
        Map<String, String> params = new HashMap<String, String>();
        SharedPreferences preferences = context.getSharedPreferences("pdata", Context.MODE_PRIVATE);
        params.put("color", String.valueOf(preferences.getInt("color", 0)));
        params.put("sort", String.valueOf(preferences.getInt("sort", 0)));
        return params;
    }

}
```
在list_options_menu添加颜色按钮
```
        <item android:id="@+id/menu_color"
        android:title="@string/menu_color"
        android:alphabeticShortcut='c' />
```
注册按钮点击事件
```
        case R.id.menu_color:
            LinearLayout form=(LinearLayout)getLayoutInflater()
                    .inflate(R.layout.color,null);
            final AlertDialog dialog=new AlertDialog.Builder(this)
                    .setView(form)
                    .setTitle("Select Color")
                    .setPositiveButton("Exit", new DialogInterface.OnClickListener()
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                        }

                        })
                    .create();
            dialog.show();
            //更新背景颜色
            setTextViewOnclick(dialog);
            return true;
```
加载配置文件
```
service = new PreferencesService(this);
        Map<String, String> params = service.getPerferences();
        String value = params.get("color");
        if(!"0".equals(value))
            color= Integer.parseInt(value);
        else
            color = R.drawable.white;
        Resources res = getResources();
        Drawable bdrawable = res.getDrawable(color);
        this.getListView().setBackgroundDrawable(bdrawable);
```
写一个修改背景颜色的函数
```
    private void setColor(int color){
        Resources res = getResources();
        Drawable drawable = res.getDrawable(color);
        getListView().setBackgroundDrawable(drawable);
        this.color = color;
        
        Map<String,String> string=service.getPerferences();
        String sort=string.get("sort");
        int sortType=Integer.parseInt(sort);
        service.save(color,sortType);
    }

    private void setTextViewOnclick(final AlertDialog dialog){
        TextView color_a = (TextView)dialog.getWindow().findViewById(R.id.color_1);
        TextView color_b = (TextView)dialog.getWindow().findViewById(R.id.color_2);
        TextView color_c = (TextView)dialog.getWindow().findViewById(R.id.color_3);
        TextView color_d = (TextView)dialog.getWindow().findViewById(R.id.color_4);
        color_a.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setColor(R.drawable.white);
            }
        });
        color_b.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setColor(R.drawable.yellow);
            }
        });

        color_c.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setColor(R.drawable.green);
            }
        });
        color_d.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setColor(R.drawable.bgp1);
            }
        });
    }
}
```
在NodeEditor中的OnCreate加载时读取配置文件，实现编辑框的背景颜色同步
```
 PreferencesService service = new PreferencesService(this);
        Map<String, String> params = service.getPerferences();
        String value = params.get("color");
        int color;
        if(!"0".equals(value))
            color= Integer.parseInt(value);
        else
            color = R.drawable.white;
        Resources res = getResources();
        Drawable bdrawable = res.getDrawable(color);
        this.getWindow().setBackgroundDrawable(bdrawable);
```
完成后如下图
### ![](https://github.com/ih8rain/NotePad-master/blob/master/images/color1.png)![](https://github.com/ih8rain/NotePad-master/blob/master/images/color2.png)
