---
title: (未解決) AndroidX RoomのTransactionのテストが失敗する
---

AndroidX RoomのTransactionをrunBlockingTestでテストしようとすると下記例外が投げられる対応です。
```
java.lang.IllegalStateException: This job has not completed yet
```

!!! Env "環境"
    - compileSdkVersion 30
    - Kotlin 1.5.10
    - androidx.core.core-ktx 1.6.0
    - androidx.room 2.3.0
    - kotlinx-coroutines-test 1.5.1

## 現象

```kotlin
@Dao
interface SomeDao{

  @Insert
  suspend fun addFoo(foo: FooEntity)

  @Insert
  suspend fun addBar(bar: BarEntity)

  @Transaction
  suspend fun addFooAndBar(foo: FooEntity, bar: BarEntity){
    addFoo(foo)
    addBar(bar)
  }
}
```
こういう中断可能なTransactionを持ったDaoに対して、runBlockingTest[^1]で意気揚々とテストしようとすると例外が出て困りました。

```kotlin
    private lateinit var db: MyDatabase
    private lateinit var dao: SomeDao

    @Before
    fun createDb(){
        db = Room
            .inMemoryDatabaseBuilder(context, MyDatabase::class.java)
            .build()
        dao = db.someDao()
    }

    @After
    fun closeDb(){
        db.close()
    }

    @Test
    @Throws(Exception::class)
    fun someTest() = runBlockingTest {  //  not passed
      val foo = FooEntity()
      val bar = BarEntity()
      dao.addFooAndBar(foo, bar)
    }
```

```
java.lang.IllegalStateException: This job has not completed yet
	at kotlinx.coroutines.JobSupport.getCompletionExceptionOrNull(JobSupport.kt:1189)
	at kotlinx.coroutines.test.TestBuildersKt.runBlockingTest(TestBuilders.kt:53)
	at kotlinx.coroutines.test.TestBuildersKt.runBlockingTest$default(TestBuilders.kt:45)
	at com.hassakulab.example.SomeTest.someTest ...
```

[^1]: coroutineをいい感じにテストできるいいやつです [runBlockingTest](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-test/kotlinx.coroutines.test/run-blocking-test.html)

## 対応

とりあえずrunBlockingを使えばテストはできます。

```kotlin
    private lateinit var db: MyDatabase
    private lateinit var dao: SomeDao

    @Before
    fun createDb(){
        db = Room
            .inMemoryDatabaseBuilder(context, MyDatabase::class.java)
            .setTransactionExecutor(Dispatchers.Default.asExecutor())   // 追加
            .setQueryExecutor(Dispatchers.Default.asExecutor())  //  これはついで
            .build()
        dao = db.someDao()
    }

    @After
    fun closeDb(){
        db.close()
    }

    @ExperimentalCoroutinesApi
    @Test
    @Throws(Exception::class)
    fun someTest() = runBlocking {  //  runBlockingTest　→ runBlocking
      val foo = FooEntity()
      val bar = BarEntity()
      dao.addFooAndBar(foo, bar)
    }
```

待ちがある場合とかタイムアウトはどうやってテストすればいいんだ...

