# Windows下PHP扩展编译环境搭建

参考 Build your own PHP on Windows[](https://wiki.php.net/internals/windows/stepbystepbuild)

## 编译器

Visual C++ 9.0（Visual Studio 2008 或 Visual C++ 2008，用于编译PHP5.3、PHP5.4）
Visual C++ 11.0（Visual Studio 2012，用于编译PHP5.5+）

## 步骤

1. 安装Visual Studio（2008和2012）
2. C盘根目录下手动创建PHP编译目录，结构如下：

    ```
    c:/
        phpsdk/
            php53/
                vc9/
                    x86/
                        deps/
                        pecl/
            php54/
                vc9/
                    x86/
                        deps/
                        pecl/
            php55/
                vc11/
                    x86/
                        deps/
                        pecl/
            php56/
                vc11/
                    x86/
                        deps/
                        pecl/
    ```

3. 解压sdk、deps和PHP源码文件至指定目录

    ```
    php-sdk-binary-tools-20110915.zip   c:/phpsdk/

    deps-5.3-vc9-x86.7z                 c:/phpsdk/php53/vc9/x86/deps/
    deps-5.4-vc9-x86.7z                 c:/phpsdk/php54/vc9/x86/deps/
    deps-5.5-vc11-x86.7z                c:/phpsdk/php55/vc11/x86/deps/
    deps-5.6-vc11-x86.7z                c:/phpsdk/php56/vc11/x86/deps/

    php-5.3.X.tar.bz2                   c:/phpsdk/php53/vc9/x86/
    php-5.4.X.tar.bz2                   c:/phpsdk/php54/vc9/x86/
    php-5.5.X.tar.bz2                   c:/phpsdk/php55/vc11/x86/
    php-5.6.X.tar.bz2                   c:/phpsdk/php56/vc11/x86/
    ```

4. 拷贝扩展源码至c:/phpsdk/php5X/vcX/x86/pecl/XXX
5. 打开Windows SDK 6.1 shell（vc9，PHP5.3、PHP5.4）或Developer Command Prompt for VS2012（vc11，PHP5.5+）
6. 执行以下命令进行编译

```
    # 进入PHP编译主文件夹
    cd C:\phpsdk

    # 设置编译需要的环境变量
    bin\phpsdk_setvars.bat

    # 进入PHP源码目录
    cd phpsdk\php5X\vcX\x86\php-5.X.X

    # 编译TS版扩展
    buildconf --force
    configure --disable-all --enable-cli --enable-pdo --enable-XXX=shared
    nmake
    upx Release_TS\php_XXX.dll
    copy /y Release_TS\php_XXX.dll /path/to/build/target/directory/

    # 编译NTS版扩展
    buildconf --force
    configure --disable-all --enable-cli --enable-pdo --enable-XXX=shared --disable-zts
    nmake
    upx Release\php_XXX.dll
    copy /y Release\php_XXX.dll /path/to/build/target/directory/
    ```

## 备注

* 第一次编译的时候，需要编译PHP核心，所以时间会比较长。
* 再次编译时，只需重复执行5、6步即可。
* 若扩展代码已经修改，不必执行nmake clean，只需删除扩展相关的编译中间文件即可。
* 可以使用自动编译脚本，参考如下：

    ```
    @echo off

    c:
    cd \phpsdk && ^
    bin\phpsdk_setvars.bat && ^
    cd \phpsdk\php5X\vcX\x86\php-5.X.X && ^
    cls

    rd /s /q Release_TS\pecl\XXX
    del /q Release_TS\php_XXX.*

    rd /s /q Release\pecl\XXX
    del /q Release\php_XXX.*

    svn up ..\pecl\XXX && ^

    buildconf --force && ^
    configure --disable-all --enable-cli --enable-pdo --enable-XXX=shared && ^
    nmake && ^
    upx Release_TS\php_XXX.dll && ^
    copy /y Release_TS\php_XXX.dll /path/to/build/target/directory/ts/ && ^

    buildconf --force && ^
    configure --disable-all --enable-cli --enable-pdo --enable-XXX=shared --disable-zts && ^
    nmake && ^
    upx Release\php_XXX.dll && ^
    copy /y Release\php_XXX.dll /path/to/build/target/directory/nts/ && ^

    echo.
    ```
