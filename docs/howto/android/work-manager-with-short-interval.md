# WorkManagerを用いて短い間隔で定期的に処理を実行する

WorkManagerはwork-runtime-ktx:2.6.0の時点で15分より長い間隔でないと
定期実行できません。[^1]
それを無理やり短い間隔で定期実行する方法です。

[^1]: [PeriodicWorkRequest  |  Android Developers](https://developer.android.com/reference/androidx/work/PeriodicWorkRequest#MIN_PERIODIC_INTERVAL_MILLIS)

!!! warning
    推奨されない方法です。どうしても必要なときだけ仕方なく使いましょう。
    ユーザに明示するためにForegroundServiceを利用するのがいいと思います。

!!! Env "環境"
    - androidx.work:work-runtime-ktx:2.6.0
    - ランタイム: Android 9

## 方法

OneTimeWorkRequestでリクエストした単発タスクの中で、次のタスクをスケジュールするようにします。例えば1分ごとに実行されるタスクは次のように書けます。

```kotlin
/**
 * sample of worker
 */
class LogWorker(context: Context, workerParameters: WorkerParameters)
    : Worker(context, workerParameters) {

    companion object{

        /**
         * create work request
         */
        fun createWorkRequest(): WorkRequest{
            return OneTimeWorkRequestBuilder<LogWorker>()
                .setInitialDelay(1, TimeUnit.MINUTES)
                .build()
        }
    }

    override fun doWork(): Result {
        Log.d("LogWorker", "worker doWork")

        //  request next work
        WorkManager.getInstance(applicationContext)
            .enqueue(createWorkRequest())

        return Result.success()
    }
}
```

最初の1回をリクエストしてあげれば以後1分ごとにバックグラウンドで実行されます。

```kotlin
class SomeActivity: AppCompatActivity(){
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        WorkManager.getInstance(applicationContext)
            .enqueue(LogWorker.createWorkRequest())
    }
}

```
