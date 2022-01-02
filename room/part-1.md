# Room - Part 1 - Introduction

[Developer document](https://developer.android.com/training/data-storage/room)  

## Get the dependencies
https://developer.android.com/training/data-storage/room#setup
```
def roomVersion = "2.4.0"

implementation("androidx.room:room-runtime:$roomVersion")
annotationProcessor("androidx.room:room-compiler:$roomVersion")

// To use Kotlin annotation processing tool (kapt)
kapt("androidx.room:room-compiler:$roomVersion")

// optional - Kotlin Extensions and Coroutines support for Room
implementation("androidx.room:room-ktx:$roomVersion")
```

## Full sample code

<pre>
// <b>Entity</b>
// ===================================================================================

                                                    // Entity defines table inside the database.
                                                    // An entity data class can be used to refer
                                                    // to individual entries (row) in the table.
                                                    
                                                    // This is similar to model class.

<i>@Entity(tableName = "Table1")</i>
data class Table1Entity (
    <i>@PrimaryKey(autoGenerate = true)</i>
    val id: Int,
    val name: String
)

// ===================================================================================



// <b>DAO</b>
// ===================================================================================

                                                    // DAO - Data Access Objects
                                                    // This is not an object though.
                                                    // DAO is an interface,
                                                    // having methods for operations on table.
                                                    
                                                    // IMPORTANT!
                                                    // 1. Needs to be anotated with <i>@Dao</i>
                                                    // 2. Appropriate annotation.
                                                    // 3. <i>@Insert, @Delete, @Update</i>
                                                    //    methods cannot have any other argument 
                                                    //    other than type of the table entity.
                                                    // 4. <i>@Query</i> - needs to have an 
                                                    //    SQL query statement.

<i>@Dao</i>
interface Table1Dao {

    <i>@Insert(onConflict = OnConflictStrategy.ABORT)</i>   // onConflit optional. Default - ABORT
    suspend fun insertRecord(record: Table1Entity)

    <i>@Delete</i>
    suspend fun deleteRecord(record: Table1Entity)

    <i>@Update</i>
    suspend fun updateRecord(record: Table1Entity)

    <i>@Query("SELECT * FROM Table1")</i>
    suspend fun getAllRecords(): List<Table1Entity>
                                                    // Note the table name in SQL query matches
                                                    // with <i>@Entity</i> annotation "tableName",
                                                    // present in the "Table1Entity" data class.
                                                    
    
    <i>@Query("SELECT EXISTS(SELECT * FROM Table1 WHERE name is <b>:name</b>)")</i>
    suspend fun isPresent(name: String): Boolean
                                                    // This method checks if the name entry exists.
                                                    // Check how the method is passed to the query
                                                    // (using a colon :)
                                                    // We cannot use in argument like ${record.name}
                                                    // as dot operator is not supported.
                                                    // <i>@Query</i> can take only primary type variables.
}

// ===================================================================================



// <b> Database class</b>
// ===================================================================================

                                                    // This "connects" the entity data class
                                                    // and the DAO interface.
                                                    
                                                    // IMPORTANT!
                                                    // 1. To be annotated with <i>@Database</i>,
                                                    //    which takes input of array of entities,
                                                    //    (thus it knows the table schemas).
                                                    //    <b>"version" is also mandatory.</b>
                                                    // 2. Must be <b>abstract</b> class, 
                                                    //    extending "RoomDatabase" class.
                                                    // 3. Must have abstract functions with 
                                                    //    return type as DAO interface.

<i>@Database(entities = [Table1Entity::class], version = 1)</i>
abstract class AppDb: RoomDatabase() {
    abstract fun table1Dao(): Table1Dao
}

// ===================================================================================



// <b>Create instance of Database class</b>
// For singleton, create in a kotlin file, not inside class.
// ===================================================================================

                                                    // Needs context for Room.databaseBuilder
                                                    // Argument 1 - context
                                                    // Argument 2 - previuosly defined 
                                                    //    database class extending RoomDatabase()
                                                    // Argument 3 - name of database file
                                                    
                                                    // Creating database instance 
                                                    // is an expensive operation.
                                                    // Hence we create once and 
                                                    // store that in a variable.

private var DbSingleton : AppDb? = null
fun <b><i>getAppDb</i></b>(context: Context): AppDb {
    return DbSingleton ?:
    Room.databaseBuilder(context, AppDb::class.java, "Name.db").build().apply {
        DbSingleton = this
    }
}

// ===================================================================================



// <b>Call the DAO interface functions</b>
// Call inside a coroutine scope.
// ===================================================================================

                                                    // Call the previously defined <i>getAppDb()</i>
                                                    // function.
                                                    // This is an instance of "AppDb" class
                                                    // which contained "table1Dao()" function.
                                                    // Use that method to get the instance of
                                                    // DAO interface and then use the methods
                                                    // defined in "Table1Dao" interface.

class MainActivity: AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        CoroutineScope(IO).launch {
            getAppDb(this@MainActivity).table1Dao().run {
                insertRecord(
                    Table1Entity(0, "sample_name_776")
                )
                println("DB result: ${getAllRecords()}, size: ${getAllRecords().size}")
            }
        }
    }
    
}

// ===================================================================================
</pre>
