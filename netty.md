Netty是JBoss提供的一个Java开源框架，netty提供异步、事件驱动的网络应用程序框架和工具，用以快速开发高性能，高可用的网络服务器和客户端程序，也就是说netty是一个基于nio的编程框架，使用netty可以快速开发一个网络应用。

由于java自带的nio api 使用起来非常复杂，而且还可能出现Epoll Bug，这使得我们使用原生的nio来进行网络编程存在很大的难度且非常耗时，但是netty良好的设计可以使得开发人员快速高效的进行网络应用开发。

netty的核心是支持零拷贝的bytebuf缓冲对象，通用通信api和可扩展的事件模型，它支持多种传输服务并且支持HTTP,Protobuf,二进制，文本，WebSocket等一系列常见协议，也支持自定义协议。