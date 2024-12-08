# 工作窃取式调度

每个执行器维护一个就绪队列，以先进先出的方式执行任务。当该队列为空时，选取随机选取一个其他执行器，该执行器从选择的执行器的队列尾端偷取一个任务进行执行。

## References

- [cilk](https://zh.wikipedia.org/zh-sg/Cilk)
- [Scheduling Multithreaded Computations by Work Stealing](papers/Scheduling Multithreaded Computations by Work Stealing.pdf)
