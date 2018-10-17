# Asynchronous interface {#concept_bps_nxl_42b .concept}

## Create a client {#section_jpz_pxl_42b .section}

-   Create directly \(use a Table Store endpoint to create a client\)

    ```
    Endpoint ep("YourEndpoint", "YourInstance");
    Credential cr("AccessKeyId", "AccessKeySecret");
    ClientOptions opts;
    AsyncClient* client = NULL;
    Optional<OTSError> res = AsyncClient::create(client, ep, cr, opts);
    ```

    **Note:** Avoid using the AccessKey of your primary account to access Table Store. We recommend using a temporary token or the AccessKey of a sub-account. If you use a temporary STS token, the credential object in the aforementioned code must be changed to: `Credential cr("AccessKeyId", "AccessKeySecret", "SecurityToken");`.

    See [Synchronous interfaces](reseller.en-US/SDK Reference/C++ SDK/Initialization.md#ul_n4q_hkf_2fb) for more information on the configuration items.

-   Construct from SyncClient

    ```
    SyncClient& sync = ... ;
    AsyncClient* async = AsyncClient::create(sync);
    ```


## Table operations {#section_z1s_wxl_42b .section}

A table operation is used as an example here to demonstrate how to use an asynchronous interface.

**Preparations**

Two functions must be ready:

-   Request object

    The function signature of `listTable` is as follows:

    ```
    void listTable(
      ListTableRequest&,
      const std::tr1::function<void(
          ListTableRequest&, util::Optional<OTSError>&, ListTableResponse&)>&);
    ```

    The first parameter is a mutable reference \(in contrast, a synchronous interface is an immutable reference\). After `listTable()` is returned \(at this point, the list table operation has not yet been completed\), the passed-in `ListTableRequest` object may be changed or destructed, opening the door for subtle errors that are difficult to investigate. To avoid issues of this sort, asynchronous clients transfer \(not copy\) the contents of the passed-in request object to internal storage. This means that after calling `listTable()`, passed-in objects can be changed.

-   Callback function

    The callback function does not return any value. The three types of receive parameters are as follows:

    -   Request object. This is the request object that is passed in when the user calls `listTable()`. After callback, the asynchronous client no longer needs the request object and it is returned to the user’s callback function in the form of a mutable reference. This allows the user to transfer out the contents of the request object.

    -   Error object wrapped in optional. If no errors exist, the `present()` method of this object returns false.

    -   Response object. As with request objects, response objects are also returned to the callback function in the form of a mutable reference. If an error exists, the response object must be a valid object \(one that can be destructed\). However, the contents of the response object are undefined.

    **Note:** 

    -   The asynchronous client makes sure that every request callback is only called one time.
    -   Theoretically, a callback function can be called before the `listTable()` is returned.

**Example**

```
void listTableCallback(
    ListTableRequest&,
    Optional<OTSError>& err,
    ListTableResponse& resp)
{
    if (err.present()) {
        // Error in processing
    } else {
        const IVector<string>& xs = resp.tables();
        for(int64_t i = 0; i < xs.size(); ++i) {
            cout << xs[i] << endl;
        }
    }
}
void listTable(AsyncClient& client)
{
    ListTableRequest req;
    client.listTable(req, listTableCallback);
}
```

