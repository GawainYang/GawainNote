
# 异常和错误处理 #

## 3 异常的最佳实践 ##

- 1）为可恢复的错误使用检查型异常，为编程错误使用非检查型错误。
- 2）在finally程序块中关闭或者释放资源
- 3）在堆栈跟踪中包含引起异常的原因
- 4）始终提供关于异常的有意义的完整的信息
- 5）避免过度使用检查型异常
- 6）将检查型异常转为运行时异常
- 7）记住对性能而言，异常代价高昂
- 8）避免catch块为空
- 9）使用标准异常
- 0）记录任何方法抛出的异常


## 参考文献 ##
1. Java 编程中关于异常处理的 10 个最佳实践: 该博客给出了Java异常处理时的一些准则，其中文翻译地址为：[https://www.oschina.net/translate/10-exception-handling-best-practices-in-java-programming](https://www.oschina.net/translate/10-exception-handling-best-practices-in-java-programming "Java 编程中关于异常处理的 10 个最佳实践 ") 