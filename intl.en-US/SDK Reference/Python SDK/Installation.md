# Installation {#concept_31725_zh .concept}

## Prerequisites {#section_wdr_tv2_2fb .section}

Applicable to Python 2 and Python 3.

## Installation {#section_y55_j4h_1m5 .section}

-   Method 1: Install Python by using pip.

    The command is as follows.

    ``` {#codeblock_ljf_ut5_kzf .language-shell}
        sudo pip install tablestore
    ```

-   Method 2: Install Python by using GitHub.

    Make sure you have installed [git](https://git-scm.com/downloads), and then run the following command.

    ``` {#codeblock_5nm_yj6_z6z .language-shell}
    git clone https://github.com/aliyun/aliyun-tablestore-python-sdk.git
    sudo python setup.py  install
    ```

-   Method 3: Install Python by using source code.
    1.  Download the [Python SDK](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/python/aliyun-tablestore-python-sdk-5.1.0.tar.gz).
    2.  Decompress the SDK, and then run the following command.

        ``` {#codeblock_wgz_3t5_xqn .language-shell}
        sudo python setup.py install
        ```


## Verify SDK {#section_w56_oq4_8bf .section}

Enter python on the command line and press ENTER to check the version.

``` {#codeblock_vqp_hmr_xfy .language-shell}
>>> import tablestore
>>> tablestore.__version__
'5.1.0'
```

## Uninstall SDK {#section_fjb_czo_ibc .section}

Uninstall python SDK by using pip.

``` {#codeblock_rd5_8gl_3z2 .language-shell}
sudo pip uninstall tablestore
```

