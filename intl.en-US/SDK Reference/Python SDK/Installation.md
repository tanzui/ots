# Installation {#concept_31725_zh .concept}

## Prerequisites {#section_wdr_tv2_2fb .section}

Applicable to Python 2 and Python 3.

## Installation { .section}

-   Method 1: Install Python by using pip.

    The command is as follows.

    ```language-shell
    	sudo pip install tablestore
    
    ```

-   Method 2: Install Python by using GitHub.

    Make sure you have installed [git](https://git-scm.com/downloads), and then run the following command.

    ```language-shell
    git clone https://github.com/aliyun/aliyun-tablestore-python-sdk.git
    sudo python setup.py  install
    
    ```

-   Method 3: Install Python by using source code.
    1.  Download the [Python SDK](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27352/cn_zh/1526039079957/aliyun-tablestore-python-sdk-4.3.7.tar.gz).
    2.  Decompress the SDK, and then run the following command.

        ```language-shell
        sudo python setup.py install
        
        ```


## Verify SDK { .section}

Enter python on the command line and press ENTER to check the version.

```language-shell
>>> import tablestore
>>> tablestore.__version__
'4.3.7'

```

## Uninstall SDK { .section}

Uninstall python SDK by using pip.

```language-shell
sudo pip uninstall tablestore

```

