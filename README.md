# NotePadApplication  
### 实现记事本的一些功能，包括 时间戳、搜索功能、更换背景、记事本分类。  
### （一）增加时间戳  
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
#### 每一条笔记本条目都会显示最近修改的时间，结果截图：  
<img src="https://github.com/2017023633/image/blob/master/image/1.png" width="300" />   
