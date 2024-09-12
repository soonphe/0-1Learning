# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android Log

### 为什么需要日志框架
如果我们需要用三方库，那就意味着基于原生方案会存在一些痛点，我们不得不使用某种手段去解决这些痛点。那原生 Logcat 存在哪些痛点，我们来聊一聊：

- 日志不能持久化，缓冲区日志很容易丢失
- 如果系统压力大有可能会导致日志折叠、丢失
- 无法定义日志输出格式，如：json、xml
- 无法快速定位日志输出时的代码位置

### 几种常见的日志框架
Logger、Timber、XLog

### Logger
Logger orhanobut/logger是一个比较早期的日志框架，积累到现在的人气超高。这个库非常轻量，满打满算整个库只有 13 个类

集成比较简单，只需要指定输出日志的 Adapter
```
FormatStrategy formatStrategy = PrettyFormatStrategy.newBuilder()  
    .showThreadInfo(true) // (Optional) Whether to show thread info or not. Default true  
    .methodCount(1) // (Optional) How many method line to show. Default 2  
    // .methodOffset(3) // (Optional) Skips some method invokes in stack trace. Default 0  
    .tag("My custom tag") // (Optional) Custom tag for each log. Default PRETTY_LOGGER  
    .build();  
  
Logger.addLogAdapter(new AndroidLogAdapter(formatStrategy));  
  
Logger.addLogAdapter(new DiskLogAdapter());
```
日志输出格式还是挺好看的，可以直接输出 Collection、json、xml 类型数据，但是不能自定义输出格式。 日志可以保存到磁盘，但不能配置文件相关策略（文件名、备份、删除等），可以理解为，有存储文件功能，但不多。

这个框架非常轻量，只有十多个类，它可以满足我们基本的日志需求，将日志保存到文件，且不会丢失。

日志输出格式也还不错。但相对而言，对于个性化的支持就比较欠缺了。比如，输出格式是不能简单自定义的，比如我如果只想输出一行日志，不输出表格线，那就会比较麻烦。

### Timber
Timber JakeWharton/timber是 Jake Wharton 大神出品。他原话是：老是要把打日志这部分代码拷来拷去太麻烦了，所以以库的形式开源出来。Timber 与其他日志库不太一样的是它并没有提供很多功能，而是搭建了一个日志功能框架，大家可以按照自己的需求来构建自己的Tree。

Timber 更简单，只有一个类文件，使用 Kotlin 语言。不过这些代码主要是框架代码，只有一个实现类DebugTree用来实现原生控制台输出日志,可以自已自定义输出格式，可以不用指定 TAG，默认 TAG 为类名
```
if (BuildConfig.DEBUG) {  
    Timber.plant(new DebugTree());  
} else {  
    Timber.plant(new CrashReportingTree());  
}

Timber.i("A button with ID %s was clicked to say '%s'.", button.getId(), button.getText());
```
一个很优秀的日志框架，如果你需要输出到文件、云端，那可以定义自己的FileLoggingTree、CloudFileTree,然后初始化时用 Timber.plant()方法，把自定的 Tree "种植"下去就行。

### 如果需要保存自定义地址：
日志保存：
```
    init {
        Timber.plant(Timber.DebugTree())
        Timber.plant(FileLoggingTree())
        Timber.plant(CrashReportTree())
    }
```
自定义LoggingTree：
```
class FileLoggingTree : DebugTree() {

    override fun isLoggable(priority: Int): Boolean {
        return priority > Log.DEBUG
    }

    /**
     * 按日期格式生成日志文件
     */
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        try {
            val path = "Log"
            val fileNameTimeStamp = SimpleDateFormat(
                "yy-MM-dd",
                Locale.getDefault()
            ).format(Date())
            val logTimeStamp = SimpleDateFormat(
                "E MMM dd yyyy 'at' hh:mm:ss:SSS aaa",
                Locale.getDefault()
            ).format(Date())
            val fileName = "$fileNameTimeStamp.txt"

            // Create file
            val logPath = File(MyApplication.app.environmentProvider.invoke().sdcardPath, "Log");

            val file = generateFile(logPath.absolutePath + "/train", fileName)

            // If file created or exists save logs
            if (file != null) {

                val isFirstLine: Boolean = !file.exists()


                val writer = FileWriter(file, true)

                if (isFirstLine) {
                    writer.append(
                        String.format(
                            "code: %s imei:%s deviceType:%s version:%s time:%s",
                            BuildConfig.COMMIT_SHA,
                            DeviceUtils.getIMEI(MyApplication.app),
                            Build.DEVICE,
                            DeviceUtils.getVersionCode(MyApplication.app),
                            DateUtils.getCurrentDateTime()
                        )
                    )
                }

                //TODO: format as Android Log.
                writer.append(logTimeStamp)
                    .append('\t')
                    .append(android.os.Process.myPid().toString())
                    .append('\t')
                    .append(Thread.currentThread().getName())
                    .append('\t')
                    .append(Thread.currentThread().getId().toString())
                    .append('\t')
                    .append(tag)
                    .append('\t')
                    .append(message)
                    .appendLine()
                writer.flush()
                writer.close()
            }
        } catch (e: Exception) {
            MyLog.e(LOG_TAG, Log.getStackTraceString(e))
        }
    }

    override fun createStackElementTag(element: StackTraceElement): String? {
        // Add log statements line number to the log
        return super.createStackElementTag(element) + " - " + element.lineNumber
    }

    companion object {
        private val LOG_TAG = FileLoggingTree::class.java.simpleName

        /*  Helper method to create file*/
        private fun generateFile(path: String, fileName: String): File? {
            var file: File? = null
            val root = File(path)
            var dirExists = true
            if (!root.exists()) {
                dirExists = root.mkdirs()
            }
            if (dirExists) {
                file = File(root, fileName)
            }
            return file
        }
    }
}
```

### XLog
轻量、美观强大、可扩展的 Android 和 Java 日志库，可同时将日志打印在如 Logcat、Console 和文件中。如果你愿意，你可以将日志打印到任何地方

配置代码：
```
LogConfiguration config = new LogConfiguration.Builder()
    .logLevel(BuildConfig.DEBUG ? LogLevel.ALL             // 指定日志级别，低于该级别的日志将不会被打印，默认为 LogLevel.ALL
        : LogLevel.NONE)
    .tag("MY_TAG")                                         // 指定 TAG，默认为 "X-LOG"
    .enableThreadInfo()                                    // 允许打印线程信息，默认禁止
    .enableStackTrace(2)                                   // 允许打印深度为 2 的调用栈信息，默认禁止
    .enableBorder()                                        // 允许打印日志边框，默认禁止
    .jsonFormatter(new MyJsonFormatter())                  // 指定 JSON 格式化器，默认为 DefaultJsonFormatter
    .xmlFormatter(new MyXmlFormatter())                    // 指定 XML 格式化器，默认为 DefaultXmlFormatter
    .throwableFormatter(new MyThrowableFormatter())        // 指定可抛出异常格式化器，默认为 DefaultThrowableFormatter
    .threadFormatter(new MyThreadFormatter())              // 指定线程信息格式化器，默认为 DefaultThreadFormatter
    .stackTraceFormatter(new MyStackTraceFormatter())      // 指定调用栈信息格式化器，默认为 DefaultStackTraceFormatter
    .borderFormatter(new MyBoardFormatter())               // 指定边框格式化器，默认为 DefaultBorderFormatter
    .addObjectFormatter(AnyClass.class,                    // 为指定类型添加对象格式化器
        new AnyClassObjectFormatter())                     // 默认使用 Object.toString()
    .addInterceptor(new BlacklistTagsFilterInterceptor(    // 添加黑名单 TAG 过滤器
        "blacklist1", "blacklist2", "blacklist3"))
    .addInterceptor(new MyInterceptor())                   // 添加一个日志拦截器
    .build();

Printer androidPrinter = new AndroidPrinter(true);         // 通过 android.util.Log 打印日志的打印器
Printer consolePrinter = new ConsolePrinter();             // 通过 System.out 打印日志到控制台的打印器
Printer filePrinter = new FilePrinter                      // 打印日志到文件的打印器
    .Builder("<日志目录全路径>")                             // 指定保存日志文件的路径
    .fileNameGenerator(new DateFileNameGenerator())        // 指定日志文件名生成器，默认为 ChangelessFileNameGenerator("log")
    .backupStrategy(new NeverBackupStrategy())             // 指定日志文件备份策略，默认为 FileSizeBackupStrategy(1024 * 1024)
    .cleanStrategy(new FileLastModifiedCleanStrategy(MAX_TIME))     // 指定日志文件清除策略，默认为 NeverCleanStrategy()
    .flattener(new MyFlattener())                          // 指定日志平铺器，默认为 DefaultFlattener
    .writer(new MyWriter())                                // 指定日志写入器，默认为 SimpleWriter
    .build();

XLog.init(                                                 // 初始化 XLog
    config,                                                // 指定日志配置，如果不指定，会默认使用 new LogConfiguration.Builder().build()
    androidPrinter,                                        // 添加任意多的打印器。如果没有添加任何打印器，会默认使用 AndroidPrinter(Android)/ConsolePrinter(java)
    consolePrinter,
    filePrinter);

```
XLog 的配置非常丰富、灵活，所以它比前两个库类要多一些，50+左右，也算非常轻量。如果你不想自定义配置，只需要XLog.init(LogLevel.ALL);即可完成初始化，全部使用 XLog 缺省配置。

XLog 的配置非常多，主要分为几大类，我们来看下配置接口的目录结构：
```
├── formatter  
│ ├── Formatter.java  // 日志输出格式化接口
│ ├── border  
│ │ └── BorderFormatter.java  // 装饰线格式化接口
│ ├── message  
│ │ ├── json  
│ │ │ └── JsonFormatter.java  // json 格式化接口
│ │ ├── object  
│ │ │ └── ObjectFormatter.java  // 对象格式化接口
│ │ ├── throwable  
│ │ │ └── ThrowableFormatter.java  // 异常格式化接口
│ │ └── xml  
│ │ └── XmlFormatter.java  // xml 格式化接口
│ ├── stacktrace  
│ │ └── StackTraceFormatter.java  // 堆栈格式化接口
│ └── thread  
│ └── ThreadFormatter.java  // 线程id、name 输出格式化
├── interceptor  
│ └── Interceptor.java  // 拦截器
└── printer  
│ └── Printer.java //日志输出接口  
├── file  
│ ├── backup  
│ │ ├── BackupStrategy.java  // 日志备份策略接口
│ │ └── BackupStrategy2.java  
│ ├── clean  
│ │ └── CleanStrategy.java  // 日志清除策略接口  
│ ├── naming  
│ │ └── FileNameGenerator.java  // 文件命名接口
│ └── writer  
│   └── Writer.java  // 文件输入接口
└── flattener  
  └── LogFlattener.java // 日志字段排列接口
```

配置主要分为以下几类：
- 日志打印
- 日志格式化
- 文件输入
- 文件备份策略
- 文件清除策略
- 日志字段排列

XLog 的架构思想与 Timber 差不多是一致的，日志框架基于功能接口管理所有日志输出，框架本身的日志输出实现一样是基于框架定义的接口。不同的是，XLog 接口定义得更细致，有二十多个接口。同时，框架本身也有所有接口的全部默认实现，这些实现就已经可以满足部分开发者。如果不满足，那 XLog 的高可配置性就体现出来了，你可以基于框架定义自己的实现。
```
public static void init(LogConfiguration logConfiguration, Printer... printers) {  
    ......
    sPrinter = new PrinterSet(printers);  
    sLogger = new Logger(sLogConfiguration, sPrinter);  
}
```
此方法是初始化入口，假如你不想用默认的文件输出方式，你可以基于Printer接口重新实现一个CustomFilePrinter。或者你想将日志输出到云端，可以定义个CloudLogPrinter,然后初始化时使用自定义的Printer，像这样：
```
XLog.init( 
    config, 
    androidPrinter,   
    CustomFilePrinter(),
    CloudLogPrinter()
);
```
另外，如果你的工程里有其他第三方日志或 Android 原生日志输出，也希望这些地方的日志也能输出到文件，XLog可以提供一个简单的方式，让你不需要改代码便能输出日志到文件。
```
// Intercept all logs(including logs logged by third party modules/libraries) and print them to file.  
LibCat.config(true, filePrinter);
```
