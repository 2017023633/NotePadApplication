# NotePadApplication  
### 实现记事本的一些功能，包括 时间戳、搜索功能、更换背景、记事本分类。  
### (一) 增加时间戳  
#### ①由于原本的数据库中的表已经有创建时间和修改时间的相关字段，所以这里只要直接在NoteEditor.java文件中的updateNote函数中加入相关代码：  
```
Date nowTime = new Date(System.currentTimeMillis());
SimpleDateFormat sdFormatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String retStrFormatNowDate = sdFormatter.format(nowTime);
ContentValues values = new ContentValues();
values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, retStrFormatNowDate);
```
#### ②在notelist_item的布局文件中增加显示时间戳的listview  
```
<TextView
        android:id="@+id/text2"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:textSize="12dp"
        android:gravity="center_vertical"
        android:paddingLeft="10dip"
        android:singleLine="true"
        android:layout_weight="1"
        android:layout_margin="0dp"
        />
```
#### ③每一条笔记本条目都会显示最近修改的时间，结果截图：  
<img src="https://github.com/2017023633/image/blob/master/image/1.png" width="300" />   
  
### (二) 搜索功能：  
#### ①在listview布局文件中加入SearchView控件  
```
<android.support.v7.widget.SearchView
        android:id="@+id/sv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        >

    </android.support.v7.widget.SearchView>
 ```
 #### ②在NoteList.java文件中加入搜索功能的函数,然后将函数放到onCreate函数中：  
 ```
 private void SearchView(){
        searchView=findViewById(R.id.sv);
        searchView.onActionViewExpanded();
        searchView.setQueryHint("搜索笔记");
        searchView.setSubmitButtonEnabled(true);
        searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
            @Override
            public boolean onQueryTextSubmit(String s) {
                return false;
            }

            @Override
            public boolean onQueryTextChange(String s) {
                if(!s.equals("")){
                    String selection=NotePad.Notes.COLUMN_NAME_TITLE+" GLOB '*"+s+"*'";
                    updatecursor = getContentResolver().query(
                            getIntent().getData(),            // Use the default content URI for the provider.
                            PROJECTION,                       // Return the note ID and title for each note.
                            selection,                             // No where clause, return all records.
                            null,                             // No where clause, therefore no where column values.
                            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
                    );
                    if(updatecursor.moveToNext())
                        Log.i("daawdwad",selection);
                }
               else {
                    updatecursor = getContentResolver().query(
                            getIntent().getData(),            // Use the default content URI for the provider.
                            PROJECTION,                       // Return the note ID and title for each note.
                            null,                             // No where clause, return all records.
                            null,                             // No where clause, therefore no where column values.
                            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
                    );
                }
                adapter.swapCursor(updatecursor);

               // adapter.notifyDataSetChanged();
                return false;
            }
        });
    }
  ```
  #### ③在搜索框中输入想要查找的笔记的标题即可查找到，结果截图：  
  <img src="https://github.com/2017023633/image/blob/master/image/2.png" width="300" />  
  
### (三) 更改笔记本背景    
#### ①在list_option文件中加入背景颜色选择的菜单  
```
<item android:id="@+id/back_color"
           android:title="@string/back_title">
        <menu>
            <item  android:id="@+id/yellow"
                android:title="浅黄" />
            <item android:id="@+id/violet"
                android:title="浅紫" />
            <item android:id="@+id/blue"
                android:title="浅蓝" />
        </menu>

    </item>
```
#### ②在NoteList.java文件中的onOptionsItemSelected函数里加入菜单被选中的相关代码：  
```
case R.id.yellow:
listViewLayout.setBackgroundColor(getResources().getColor(R.color.lightYellow));
return true;
case R.id.violet:
listViewLayout.setBackgroundColor(getResources().getColor(R.color.lightViolet));
return true;
case R.id.blue:
listViewLayout.setBackgroundColor(getResources().getColor(R.color.lightBlue));
return true;
```
#### ③选择主界面菜单中的更换背景颜色即可更换背景，结果截图：  
<img src="https://github.com/2017023633/image/blob/master/image/3.png" width="300" />  
<img src="https://github.com/2017023633/image/blob/master/image/4.png" width="300" />  
  
### (四) 笔记本分类  
#### ①在NotePad.java中加入存储分类信息的变量  
```
public static final String COLUMN_NAME_LABEL="label";
```
#### ②在NotePadProvider.java文件中的创建表的函数中加入label字段  
```
public void onCreate(SQLiteDatabase db) {
           db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
                   + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
                   + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
                   + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " Text,"
                   + NotePad.Notes.COLUMN_NAME_LABEL+ " TEXT);");
       }
```
#### ③在笔记本编辑界面中加入分类的菜单  
```
<item android:title="分类">
        <menu>
            <item android:title="运动"
                android:id="@+id/sport"></item>
            <item android:title="学习"
                android:id="@+id/study"></item>
            <item android:title="生活"
                android:id="@+id/life"></item>
        </menu>
    </item>
```
#### ④在NoteEditor.java中加入label的私有变量，用来存储分类信息，然后将信息存入数据库中的label字段  
```
case R.id.sport:
this.label="运动";
break;
case R.id.study:
this.label="学习";
break;
case R.id.life:
this.label="生活";
break;
```
```
values.put(NotePad.Notes.COLUMN_NAME_LABEL,label);

getContentResolver().update(
                mUri,    // The URI for the record to update.
                values,  // The map of column names and new values to apply to them.
                null,    // No selection criteria are used, so no where columns are necessary.
                null     // No where columns are used, so no where arguments are necessary.
        );
```
#### ⑤在笔记本主界面添加分类的菜单，代码同步骤③一样，然后在NoteList.java中加入分类的相关代码，sort函数实现分类显示功能  
```
private void sort(String label){
        updatecursor=getContentResolver().query(getIntent().getData(),PROJECTION,
                NotePad.Notes.COLUMN_NAME_LABEL+"=?",new String[]{label},NotePad.Notes.DEFAULT_SORT_ORDER);
        adapter.swapCursor(updatecursor);
    }
```
```
case R.id.sport2:
sort("运动");
return true;
case R.id.study2:
sort("学习");
return true;
case R.id.life2:
sort("生活");
return true;
```
#### ⑥结果截图：  
<img src="https://github.com/2017023633/image/blob/master/image/5.png" width="300" />  
<img src="https://github.com/2017023633/image/blob/master/image/6.png" width="300" />
<img src="https://github.com/2017023633/image/blob/master/image/7.png" width="300" />
