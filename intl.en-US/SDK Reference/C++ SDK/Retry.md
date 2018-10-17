# Retry {#concept_67833_zh .concept}

## Common retry strategies {#section_bg2_y5f_2fb .section}

The SDK provides the following common retry strategies:

-   Default retry strategy

    An SDK error automatically triggers retries. The maximum retry interval is 10 seconds.

-   Counting retry strategy

    Retry at user-defined intervals. If the retry strategy is secure, the maximum number of retry attempts cannot exceed the user-defined number.

-   No retry


## Custom retry strategy { .section}

You can customize your retry strategy by modifying `RetryStrategy`.

```language-cpp
class RetryStrategy
{
public:
    virtual ~RetryStrategy() {}

    virtual RetryStrategy* clone() const =0;
    virtual int64_t retries() const throw() =0;
    virtual bool shouldRetry(Action, const OTSError&) const =0;
    virtual util::Duration nextPause() =0;
};

```

-    `clone()` replicates a new object. The new object must be of the same type and internal status \(including the number of retries\) as the current one.

-    `retries()`, number of already completed retries.

-    `shouldRetry()` specifies operations and errors to determine whether a retry is needed.

    Table Store provides two tool functions for easier operation:

    -   The first retriable divides errors into three groups.

        -   Completely harmless retries, such as OTSTableNotReady.
        -   Harmful or meaningless retries, such as with parameter errors.
        -   Cannot be judged based on errors alone, such as OTSRequestTimeout.
    -   The second retriable determines whether a retry can be performed based on the idempotence principles of an operation, while also taking operations and errors into consideration. In other words, both RETRIABLE errors and the DEPENDS errors of a read operation are judged to be retriable.

        ```
        enum RetryCategory
        {
          UNRETRIABLE,
          RETRIABLE,
          DEPENDS,
        };
        static RetryCategory retriable(const OTSError&);
        static bool retriable(Action, const OTSError&);
        ```

        `nextPause()` is the interval until the next retry, if retriable.


