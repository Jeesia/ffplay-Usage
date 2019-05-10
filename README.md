# ffplay-Usage
ffplay 使用中遇到的一些问题及解决方法



## ffplay 正常关闭
ffplay有event_loop函数产生消息（event）获取的循环，处理接收到的消息（event）。
程序需要关闭时，event_loop中会接收到SDL_QUIT或者FF_QUIT_EVENT类型的event，并调用do_exit（内部调用exit(0)）来结束程序。

如果需要直接集成ffplay为dll，上述消息处理后会直接退出整个程序。
添加标志进行判断结束后退出消息循环，ffplay线程也随之退出。不影响主程序正常运行。

```
**static int ffplay_exit_flag = 0;**
...
... event_loop(...)
{
 ...
   //for (;;) {
   **for (;!ffplay_exit_flag;)** {
   ...
   }
}
```
```
... void do_exit(...)
{    
    ...
    //exit(0);
    **ffplay_exit_flag = 1;**
}
 ```
