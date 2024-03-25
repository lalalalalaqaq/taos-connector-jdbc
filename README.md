# TDengine Java Connector

简体中文

[官方仓库](https://github.com/taosdata/taos-connector-jdbc)

`taos-jdbcdriver` 是 TDengine 的官方 Java 语言连接器，Java 开发人员可以通过它开发存取 TDengine 数据库的应用软件。`taos-jdbcdriver` 实现了 JDBC driver 标准的接口，并提供两种形式的连接器。一种是通过 TDengine 客户端驱动程序（taosc）原生连接 TDengine 实例，支持数据写入、查询、订阅、schemaless 接口和参数绑定接口等功能，一种是通过 taosAdapter 提供的 REST 接口连接 TDengine 实例（2.0.18 及更高版本）。REST 连接实现的功能集合和原生连接有少量不同。

注:
[这个连接的常见问题"2"](https://docs.taosdata.com/connector/java/#%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)的解决方案实在太怪了,在[issues 69](https://github.com/taosdata/taos-connector-jdbc/issues/69)也被人提及

这个仓库主要就是为了简单解决对应的问题(为了方便本地编译，删掉了测试代码还有maven信息)

核心解决步骤 : 

1. src/main/resource/native 上保留多个系统的本地函数库文件(去官网下载驱动包解压即可)
2. TSDBJNIConnector的static 代码块修改

主要是要自主指定本地函数库从哪里加载, 直接从taos-jdbc的jar包加载最方便

这样自己的项目不需要额外的代码，还能适配多种操作系统调用
```agsl
    static {
        String osName = System.getProperty("os.name");
        if (osName.contains("Windows")) {
            // 没编译，手头上没有windows
        } else if (!osName.contains("Mac") && !osName.contains("Darwin")) {
            if (osName.contains("Linux")) {
                // linux
                String libraryPath = Main.class.getClassLoader().getResource("./native/libtaos.so").getPath();
                System.load(libraryPath);
            }
        } else {
            // mac
            String libraryPath = Main.class.getClassLoader().getResource("./native/libtaos.dylib").getPath();
            System.load(libraryPath);
        }
    }
```
