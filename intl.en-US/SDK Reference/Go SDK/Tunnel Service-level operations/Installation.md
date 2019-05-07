# Installation {#concept_gck_f2j_kgb .concept}

## Download the source code package {#section_u3m_x2j_kgb .section}

```
go get github.com/aliyun/aliyun-tablestore-go-sdk/tunnel
```

## Install dependencies {#section_mbl_y2j_kgb .section}

-   You can go to the tunnel directory and use the dep tool to install dependencies.
    -   Install the [dep](https://github.com/golang/dep#installation) tool.
    -   Run the dep ensure -v command.
-   You can also directly run the go get command to install the dependencies:

    ```
    go get -u go.uber.org/zap
    go get github.com/cenkalti/backoff
    go get github.com/golang/protobuf/proto
    go get github.com/satori/go.uuid
    go get github.com/stretchr/testify/assert
    go get github.com/smartystreets/goconvey/convey
    go get github.com/golang/mock/gomock
    go get gopkg.in/natefinch/lumberjack.v2
    ```


