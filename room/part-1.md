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

@Entity(tableName = "Table1")
data class Table1Entity (
    @PrimaryKey(autoGenerate = true)
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

@Dao
interface Table1Dao {

    @Insert(onConflict = OnConflictStrategy.ABORT)   // onConflit optional. Default - ABORT
    suspend fun insertRecord(record: Table1Entity)

    @Delete
    suspend fun deleteRecord(record: Table1Entity)

    @Update
    suspend fun updateRecord(record: Table1Entity)

    @Query("SELECT * FROM Table1")
    suspend fun getAllRecords(): List<Table1Entity>

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

@Database(entities = [Table1Entity::class], version = 1)
abstract class AppDb: RoomDatabase() {
    abstract fun table1Dao(): Table1Dao
}

// ===================================================================================
// ===================================================================================
// ===================================================================================
// ===================================================================================
// ===================================================================================






































</pre>
