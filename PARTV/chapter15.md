# CHAPTER 15 Context Managers and else Blocks
说两个上下文用法
1. with:
2. for else:
## Do This, Then That: else Blocks Beyond if
**for** 中的**else**只有当 **for** loop 没有因为**break**中断才会运行。
**while** 中的**else**只有当 **while** 没有因为**break**中断才会运行。
**try**中的**else**当没有**exception**发生时候运行。

**EAFP**: Easier to ask for forgiveness than permission。乐观执行，错误后处理，而不是先检测是否可行。

**LBYL**:Look before you leap 悲观执行，先查看权限然后执行。

## Context Managers and with Blocks
**with**表达式是用来简化**try finally**语句的。
**with**获得的是**\_\_enter\_\_**中的内容。结束后调用**\_\_exit\_\_**方法。里面可以规定特定的异常处理流程。

## The contextlib Utilities
最常用的是contextmanager

## Using @contextmanager
不用实现包含enter exit的繁琐的类，只要一个yield就能解决一切。yield前的为enter的内容，yield后为exit的内容，最好将yield包含在try:中，这样如果异常就会运行finally中的内容。

