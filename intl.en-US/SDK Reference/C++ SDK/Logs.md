# Logs {#concept_67826_zh .concept}

The client provides a logger by default. If your application has its own logger, we recommend using it, as this facilitates log management.

A logger has the following four elements, as defined in `tablestore/util/logger.hpp`.

-   Logger interface

    The Logger is used to assemble the contents of a log into a Record object that is forwarded to and written out by the Sinker. The client also organizes the Logger into a tree structure, the root of which is the logger defined by the user in ClientOptions. The request logic and the networking logic use different sub-loggers derived from this root logger.

    ```language-cpp
    class Logger
    {
    public:
    enum LogLevel
    {
        kDebug,
        kInfo,
        kError,
    };
    
    virtual ~Logger() {}
    virtual LogLevel level() const =0;
    virtual void record(LogLevel, const std::string&) =0;
    virtual Logger* spawn(const std::string& key) =0;
    virtual Logger* spawn(const std::string& key, LogLevel) =0;
    };
    
    ```

    -    `level()` returns the log grade accepted by the Logger. Logs of lower grades are not transferred to the Sinker.
    -    `record()` accepts a log and its grade, and uses them to form a Record object that is sent to the corresponding Sinker of the Logger.
    -    `spawn()` derives a sub-logger.
-   Record interface

    Logger uses the Record object to transfer the contents of the log to the Sinker.

    The Record interface does not provide a method itself. The Logger and Sinker decide which methods the Record class provides.

-   Sinker interface

    Sinker is tasked with writing out Record objects.

    ```language-cpp
    class Sinker
    {
    public:
    virtual ~Sinker() {}
    virtual void sink(Record*) =0;
    virtual void flush() =0;
    };
    
    ```

    -    `sink()` writes out a Record. The Record can only be written to a cache.
    -    `flush()` clears the cache to make sure that every log is stored.
-   SinkerCenter singleton object

    SinkerCenter holds all Sinker objects and associates them with certain keys.

    ```language-cpp
    class SinkerCenter
    {
    public:
    virtual ~SinkerCenter() {}
    static std::tr1::shared_ptr<SinkerCenter> singleton();
    
    virtual Sinker* registerSinker(const std::string& key, Sinker*) =0;
    virtual void flushAll() =0;
    };
    
    ```

    -    `singleton()` obtains SinkerCenter singleton objects.
    -    `registerSinker()` registers a Sinker in the SinkerCenter.
    -    `flushAll()` clears all Sinkers in the SinkerCenter.

