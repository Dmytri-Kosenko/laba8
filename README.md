# laba8

1.Создание проекта

```java

int i = 1;

```
![image](https://user-images.githubusercontent.com/90166910/142362740-3fd50fc3-e0b4-4e31-b22e-e26822963e02.png)

2.Добавим на форму 3 компонента «EditText*», а также 5 компонентов «Button» с соответствующими именами «ID», «Name», «E-mail», «Добавить», «Удалить», «Очистить», «Считать», «Обновить».
![image](https://user-images.githubusercontent.com/90166910/142363296-899192f9-d5ae-478a-9e62-844edfbee139.png)

3.Добавим новый класс для работы с базой данных и назовём его «DBHelper».
![image](https://user-images.githubusercontent.com/90166910/142363471-6cf45016-fa83-4d06-b28f-227c774a0dee.png)

4.Расширим этот класс методами «onCreate» и «onUpdate».
5.Выберем конструктор суперкласса «SQLLiteOpenHelper» с параметрами – «Context», «Name», «Factory», «Version» и удалим лишние параметры:
```java

public DBHelper(@Nullable Context context)
{
    super(context, DATABASE_NAME, null, DATABASE_VERSION);
}


```
6.Запишем код:
```java

public class DBHelper extends SQLiteOpenHelper
{
    public static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "myBase";
    public static final String TABLE_PERSONS = "persons";
    public static final String KEY_ID = "_id";
    public static final String KEY_NAME = "name";
    public static final String KEY_MAIL = "mail";

    public DBHelper(@Nullable Context context)
    {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db)
    {
        db.execSQL("create table " + TABLE_PERSONS + "(" + KEY_ID + " integer primary key," + KEY_NAME + " text," + KEY_MAIL + " text" + ")");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
    {
        db.execSQL("drop table if exists " + TABLE_PERSONS);
        onCreate(db);
    }
}

```
7.Расширим класс «MainActivity» и добавим наши переменные:
8.
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener
{
     Button buttonAdd, buttonDelete, buttonClear, buttonRead, buttonUpdate;
     EditText etName, etEmail, etID;
     DBHelper dbHelper;

    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        buttonAdd = (Button) findViewById(R.id.buttonAdd);
        buttonAdd.setOnClickListener(this);

        buttonRead = (Button) findViewById(R.id.buttonRead);
        buttonRead.setOnClickListener(this);

        buttonClear = (Button) findViewById(R.id.buttonClear);
        buttonClear.setOnClickListener(this);

        buttonUpdate = (Button) findViewById(R.id.buttonUpdate);
        buttonUpdate.setOnClickListener(this);

        buttonDelete = (Button) findViewById(R.id.buttonDelete);
        buttonDelete.setOnClickListener(this);

        etID = (EditText) findViewById(R.id.etID);
        etName = (EditText) findViewById(R.id.etName);
        etEmail = (EditText) findViewById(R.id.etEmail);

        dbHelper = new DBHelper(this);
    }

```
9.Создадим 1 свой обработчик на все кнопки:
```java
@Override
public void onClick(View v)
{
    String ID = etID.getText().toString();
    String name = etName.getText().toString();
    String email = etEmail.getText().toString();

    SQLiteDatabase database = dbHelper.getWritableDatabase();
    ContentValues contentValues = new ContentValues(); // класс для добавления новых строк в таблицу

    switch (v.getId())
    {
        case R.id.buttonAdd:
            contentValues.put(DBHelper.KEY_NAME, name);
            contentValues.put(DBHelper.KEY_MAIL, email);
            database.insert(DBHelper.TABLE_PERSONS, null, contentValues);
            break;

        case R.id.buttonRead:
            Cursor cursor = database.query(DBHelper.TABLE_PERSONS, null, null, null,
                    null, null, null); // все поля без сортировки и группировки

            if (cursor.moveToFirst())
            {
                int idIndex = cursor.getColumnIndex(DBHelper.KEY_ID);
                int nameIndex = cursor.getColumnIndex(DBHelper.KEY_NAME);
                int emailIndex = cursor.getColumnIndex(DBHelper.KEY_MAIL);
                do {
                    Log.d("mLog", "ID =" + cursor.getInt(idIndex) +
                            ", name = " + cursor.getString(nameIndex) +
                            ", email = " + cursor.getString(emailIndex));

                    } while (cursor.moveToNext());
            } else
                Log.d("mLog", "0 rows");

            cursor.close(); // освобождение памяти
            break;

        case R.id.buttonClear:
            database.delete(DBHelper.TABLE_PERSONS, null, null);
            break;

        case R.id.buttonDelete:
            if (ID.equalsIgnoreCase(""))
            {
                break;
            }
            int delCount = database.delete(DBHelper.TABLE_PERSONS, DBHelper.KEY_ID + "= " + ID, null);
            Log.d("mLog", "Удалено строк = " + delCount);

        case R.id.buttonUpdate:
            if (ID.equalsIgnoreCase(""))
            {
                break;
            }
            contentValues.put(DBHelper.KEY_MAIL, email);
            contentValues.put(DBHelper.KEY_NAME, name);
            int updCount = database.update(DBHelper.TABLE_PERSONS, contentValues, DBHelper.KEY_ID + "= ?", new String[] {ID});
            Log.d("mLog", "Обновлено строк = " + updCount);
    }
        dbHelper.close(); // закрываем соединение с БД
}
```
10.Найти созданную вами базу данных через путь: View–>Tool Windows–>Device File Explorer–>…..databases–>имя_БД.
![image](https://user-images.githubusercontent.com/90166910/142367146-e01bb76e-cb14-4a24-89f7-6fb3c9de78f6.png)

11.Проверить работоспособность всех кнопок через окно «Logcat».
![image](https://user-images.githubusercontent.com/90166910/142368437-50088f35-33a2-4551-90ab-e0cbc58bfe27.png)
