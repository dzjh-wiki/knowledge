# Python GIL

  * 因为GIL的存在，只有IO Bound场景下的多线程会得到较好的性能
  * 如果对并行计算性能较高的程序可以考虑把核心部分换成C模块，或者索性用其他语言实现
  * GIL在较长一段时间内将会继续存在，但是会不断对其进行改进

----

`GIL(Global Interpreter Lock)`并不是Python的特性，它是在实现Python解析器(CPython)时所引入的一个概念。  

`GIL`是一个防止多线程并发执行机器码的一个Mutex。  

`In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once. This lock is necessary mainly because CPython’s memory management is not thread-safe. (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces.)`