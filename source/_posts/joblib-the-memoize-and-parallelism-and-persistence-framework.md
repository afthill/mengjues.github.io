title: joblib, the memoize and parallelism and persistence framework
date: 2015-12-24 17:07:37
tags:
- Memoize
- Parallelism
- Persistence
categories:
- 技术

---

joblib项目地址：<https://github.com/joblib/joblib>

这个库主要提供三方面的功能：
- 缓存(cache, memoize)
- 并发(parallelism)
- 序列化(persistence)

这三个是程序设计与开发中最常见的功能，而该库的主要作用是：
- 通过抽象封装，减少开发人员在程序设计与实现中容易出现的错误
- 修复python的内建库不能处理的小bug

主要包含三个类：Memory、Parallel、MemorizedFunc
<!--more-->
Memory类:
-------

Memory类是joblib提供的缓存功能的入口，生成的memory对象通过cache、clear、eval提供对
缓存功能的管理。


```python
class joblib.memory.Memory(cachedir, mmap_mode=None, compress=False, verbose=1):
"""
Memory定义的是一个context对象，该context对象用来缓存一个function的返回值；
所有缓存的值都存在文件系统里。
"""
    def __init__(cachedir, mmap_mode=None, compress=False, verbose=1):
    """
    cachedir: (string | None)
    用来指定文件存储的位置的根目录，如果设定为None，则cache机制会完全失效，
    返回的Memory对象是透明的；

    mmap_mode: (None | r+ | w+ | r | c)

    这个参数的主要作用是用来处理numpy的memory view数组，
    memoryview在python中也有专门的模块来处理，主要用来解决大块数据拷贝的性能问题，
    不同python/OS api去拷贝，而是直接开发内存操作。

    compress: (boolean | integer)
    用来指定序列化在文件系统中的内容的压缩等级，如果设定为False，就表示不压缩，
    如果设定为整数，只能是在1~9。

    verbose: (int)

    用来控制是否显示debug信息。
    """

    def cache(func=None, ignore=None, verbose=None, mmap_mode=False):
        """
        func: (callable)

        被decorator标记的function。

        ignore: (list of string)

        需要在hashing化时，排除掉的关键词列表。

        verbose: (integer)

        用来控制function是否开启verbose模式。默认使用memory对象。

        mmap_mode: (None | r+ | r | w+ | c)

        用来控制numpy array缓存的载入模式。
        """

        return decoreated_func: MemorizedFunc object
        """
        返回的是被注入的function，是一个MemorizedFunc对象。
        """

    def clear(warn=True):
        """
        用来删除cache根目录
        """

    def eval(func, *args, **kwargs):
        """
        用指定的参数（*args **kwargs），在memory的上下文环境中，执行传入的function。
        类似于原生库里的apply，除了该函数只在cache没有更新的时候调用。
        """
```

MemorizedFunc类
---------------

MemorizedFunc类是经过Memory.cache修饰后返回的修饰对象（decorated function），除了
可以提供正常的function功能之外，还可以提供对缓存的管理和内窥。

```python
class joblib.memory.MemorizedFunc(func, cachedir, ignore=None, mmap_mode=None,
compress=False, verbose=1, timestamp=None):
"""
MemorizedFunc是一个callable对象，这个callable对象用来修饰需要缓存功能的function，然
后就可以缓存该function所返回的值；
"""

    def __init__(func, cachedir, ignore=True, mmap_mode=None, compress=False,
    verbose=1, timestamp=None):
    """
    func: (callable)
    用来表示被修饰的function对象

    cachedir: (string)
    缓存数据的存储路径的根目录

    ignore: (list | None)
    不缓存的变量列表

    mmap_mode: (None | r+ | r | w+ | c)
    主要跟numpy的内存管理有关

    compress: (boolean | integer)
    缓存在文件系统中的压缩等级（1~9）

    verbose: (int)
    控制debug信息的输出等级

    timestamp: (float)
    trace信息输出时的参考时间点
    """

    def call(*args, **kwargs):
    """
    使用指定的（*args, **kwargs）执行函数
    """

    def clear():
    """
    清空function的缓存
    """

    def get_output_dir(*args, **kwargs):
    """
    返回使用指定的（*args, **kwargs）执行function时，结果被缓存所在的目录；
    """

    def load_output(output_dir):
    """
    返回之前缓存在指定的目录里的值
    """     
```

Parallel类
----------

Parallel类是一个可读性更好实现并发方案的帮助类。

```python
class Parallel(n_jobs=1, backend='multiprocessing', verbose=0,
pre_dispatch='2 * n_jobs', batch_size='auto', temp_folder=None,
max_nbytes='1M', mmap_mode='r'):

"""
njobs: (int | 1)
用来指定最大的可以并发运行的jobs，如果backend被指定为multiprocessing，
那这里的jobs就是最大的工作进程数，如果backend被指定为threading，那就是
thread pool的大小。如果指定为-1，那表示所有的cpu都被用来计算；如果为1，
那表示没有任何并发计算，这个功能主要被用来debug。如果n_jobs低于-1，那
计算的方式为：(n_cpus + 1 + n_jobs)。因此，如果n_jobs是-1，那用到的
cpu个数为：n_cpus - 1 (all cpus but one)。
"""

"""
backend: (str | None | default: multiprocessing)
指定后端并发的具体实现：
- multiprocessing: 缺省的并发使用方案，可以有更多的在communication
和memory的消耗。
- threading：由于Python中GIL的存在，如果在计算中使用了大量的Python
对象，那么使用threading将不会是好的并发方案；threading的特点是比较
低的context switch和communication的overhead。但是如果使用python的
扩展避开了GIL的限制，那使用threading将会是较优的方案。
"""

"""
veirbose: (int)
控制输出的日志级别，如果值为非0，那么任务的执行进度将会被打印；如果超
过10，所有的interation都会被打印；如果超过50，那将会把结果输出到stdout。
"""

"""
pre_dispatch: (all | integer | expression | 2 * n_jobs)
预分派的任务数，如果batch_size的大小设为auto，则任务数就是2 * n_jobs
"""

"""
batch_size: (int | auto | defaut)
每次分配给worker的原子任务数。当单个的任务数可以很快被处理的时候，多进程
可能比顺序执行的速度还要慢，因为多进程的调度消耗了overhead。批处理的任务
数的意义在于，分配给worker的任务的大小是动态的，基于计算的速度去保证每个
workder的完成时间在半秒之内。初始的batch_size大小是1。当使用batch_size
=auto而且backend="threading"的时候，将对每一个任务作为一个batch，因为
threading在context switch的时候，overhead很小，在这里用较大的batch size
毫无意义。
"""

"""
temp_folder: (str)
只在backend="multiprocessing"的时候被使用，主要用来作为大数组的内存映射的
缓存池，这些缓存池然后被worker进程使用。如果设为None，将会按照如下顺序设定：

- JOBLIB_TEMP_FOLDER所指向的文件夹
- /dev/shm存在而且是可写的，这是一个linux默认放置ramdisk的地方
- TMP、TEMP、TMPDIR环境变量所指向的文件夹
"""

"""
max_nbytes: (int | str | None | 1M by default)
用来设定传递给workder的数组大小的上限，这些数组将会自动在temp_folder中做内存
映射。可以使用int Bytes或者1 M，或者None，如果使用None，则表示不适用内存映射。
只在backend="multiprocessing"中使用。
"""

```

--------------------------------


### 按需重复计算：Memory类

Memory类定义的是一个：惰性求值（evaluation）的上下文环境，通过存储结果到操作系统
的文件系统中，保证不对传入的同样的参数（argument）重复计算两次。设计该类的主要用途
是：可以接受传入non-hashable的大数组参数而得到numpy类型的新的数组。

```
from tempfile import mkdtemp
cachedir = mkdtemp()

from joblib import Memory
memory = Memory(cachedir=cachedir, verbose=0)

@memory.cache
def f(x):
    print 'Running f(%s)' % x
    return x
```

```
print f(1)
>>> Running f(1)
>>> 1

print f(1)
>>> 1
```

### 与memoize的比较

> [The memoize decorator](http://code.activestate.com/recipes/52201/)主要通过
比较输入参数比较来缓存function的计算结果，但是它的缺点如下：

> - 每一次函数被调用时，都会比较在缓存中的input（也即传入的参数），如果是大的input
就会产生大量的overhead
> - 不能接受numpy array作为input
> - 需要大量的内存，因为cache是放在内存中

而对于joblib的Memory类，对象是被序列化到硬盘，然后使用一个persister去优化速度和对
内存的使用。

因此结论是：

memoize是比较适合于较小的input和output缓存，而Memory类则比较适合于复杂的input和
output。    

### with numpy

Memory类使用快速加密哈希算法去检查input是否已经被计算过：

```
import numpy as np

@memory.cache
def g(x):
    print 'A long running calculation, with parameter %s' % x
    return np.hamming(x)

@memory.cache
def h(x):
    print 'A second long running calculation, using g(x)'
    return np.vander(x)
```

```
>>> a = g(3)
A long-running calculation, with parameter 3
>>> a
array([ 0.08,  1.  ,  0.08])
>>> g(3)
array([ 0.08,  1.  ,  0.08])
>>> b = h(a)
A second long-running calculation, using g(x)
>>> b2 = h(a)
>>> b2
array([[ 0.0064,  0.08  ,  1.    ],
       [ 1.    ,  1.    ,  1.    ],
       [ 0.0064,  0.08  ,  1.    ]])
>>> np.allclose(b, b2)
True
```

### using memmapping

如果对numpy的数组加速，我们可以开启memory mapping：

```
>>> cachedir2 = mkdtemp()
>>> memory2 = Memory(cachedir=cachedir2, mmap_mode='r')
>>> square = memory2.cache(np.square)
>>> a = np.vander(np.arange(3)).astype(np.float)
>>> square(a)
```

```
>>> res = square(a)
>>> print(repr(res))
memmap([[  0.,   0.,   1.],
       [  1.,   1.,   1.],
       [ 16.,   4.,   1.]])
```

我们必须记得手动关闭memory mapping，以避免在windows中的文件死锁。

```
del res
```

> 如果在内存映射中使用`'r'`模式，则内存是只读的，不可能in place修改。
如果使用`'r+'`或者`'w+'`，则对内存的修改将会同时修改缓存的文件而破坏
了缓存，因此我们可以使用`'c'`模式，表示copy on write!


### Reference to cache values

当有一个大的numpy数组需要被dispatch几个worker时候，我们可以pass一个
cache的reference，而不是数据本身，然后每个worker可以通过传入的reference
自己去缓存里拿取需要的数据。


设立一个缓存的reference，可以通过一个wrapped的function：`call_and_shelve`

```
>>> result = g.call_and_shelve(4)
A long-running calculation, with parameter 4
>>> result  
MemorizedResult(cachedir="...", func="g...", argument_hash="...")
```

如果上面的reference计算完毕的时候，g的output将会从内存中删除，同时存储在文件
里，如果需要获得reference的值，可以：

```
result.get()
>>> array([ 0.08,  0.77,  0.77,  0.08])
```

cache可以被清除：

```
>>> result.clear()
>>> result.get()  
Traceback (most recent call last):
    ...
KeyError: 'Non-existing cache value (may have been cleared).\n
File ... does not exist'
```

reference存储的是MemorizedResult类型，包含了所有的必须用来读取cache的数据，
这些数据可以被pickle或者print或者copy paste。

> 如果Memory(cachedir=None)，则`call_and_shelve`返回的是NotMemorizedResult对
象，这个对象包含了所有的output，而不是reference。

### 注意点

1. 不同的session中，function的缓存是基于function name
2. lambda function，因为是匿名函数，没法把两个不同的lambda分开
3. memory cache不同被用在复杂的对象了，比如对象有`__callable__`
4. caching methods:
    - 不能decorate class method at definition time
    - 只能decorate at instantiation time

```
class Foo(object):

    @mem.cache # WRONG
    def method(self, args):
        pass
```

```
class Foo(object):
    def __init__(self, args):
        self.method = mem.cache(self.method)

    def method(self, ...):
        pass
```

### Exclude

有时候在计算input的hash的时候，我们可以忽略因为debug加入的额外的参数，
只需要把这些参数加入ignore_list即可：

```
>>> @memory.cache(ignore=['debug'])
... def my_func(x, debug=True):
...     print('Called with x = %s' % x)
>>> my_func(0)
Called with x = 0
>>> my_func(0, debug=False)
>>> my_func(0, debug=True)
```

-------------------

### Parallel for loops

Joblib提供了一个很简单的帮助类，用来并发执行loops里面的计算，核心思想是
利用python中的generator和parallel计算：

```
from math import sqrt
[sqrt(i ** 2) for i in range(10)]
>>> [0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0]
```

上面的程序可以用2个cpu来计算：

```
from math import sqrt
from joblib import Parallel, delayed
Parallel(n_jobs=2)(delayed(sqrt)(i ** 2) for i in range(10))
>>> [0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0]
```

这里的Parallel对象创建了一个多进程池，然后分叉python的解释器在多个进程里
运行。

delayed函数是一个wrapper，用来执行类似于`（func，*args， **kwargs）`的行
为。

在windows系统中，需要保护main loop去避免在使用joblib.Parallel的时候遇到
递归的子进程分叉。

```
import ...

def function1(...):
    ...

def function2(...):
    ...

if __name__ == '__main__':
    # do stuff with imports and functions defined about

```


在`if __name__ == '__main__'`之外，应该只有definitions和imports，所有代码
的运行动作都应该是在`main`中。

#### 使用threading作为计算后端

默认joblib使用multiprocessing去执行并发计算的任务，因为process之间的通信带来
的overhead，导致在执行一些nogil的任务的时候，可以采用低overhead的threading。

```
>>> Parallel(n_jobs=2, backend="threading")(
...     delayed(sqrt)(i ** 2) for i in range(10))
[0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0]
```

#### 重用工作池

由于某些计算需要处理中间过程产生的值，不停的创建和销毁工作池并维护状态是一个很大的
负担，因此，我们可以通过使用Parallel提供的context manager去重用工作池：

```
>>> with Parallel(n_jobs=2) as parallel:
...    accumulator = 0.
...    n_iter = 0
...    while accumulator < 1000:
...        results = parallel(delayed(sqrt)(accumulator + i ** 2)
...                           for i in range(5))
...        accumulator += sum(results)  # synchronization barrier
...        n_iter += 1
...
>>> (accumulator, n_iter)                            
(1136.596..., 14)
```

### 并发计算时的共享内存模式（memory mapping）

如果task的input很大，那么在并发计算时，需要把这些input分发（dispatch）给不同的
worker，在分发的时候，需要不停reallocate input产生的内存，这也是一个很大的开销，
因此我们引进了共享内存模式，在不同的进程之间共享内存数据。

#### 将数组转化为共享数组

```
import numpy as np
from joblib import Parallel, delayed
from joblib.pool import has_shared_memory

>>> Parallel(n_jobs=2, max_nbytes=1e6)(
...     delayed(has_shareable_memory)(np.ones(int(i)))
...     for i in [1e2, 1e4, 1e6])
[False, False, True]
```

缺省数据是被放在`/dev/shm`共享内存分区里，如果存在并可写的话。如果不存在，操作
系统会自动使用临时目录。

如果设定`max_nbytes=None`则表示进制数组转化为共享内存。

#### 手动管理共享内存

```
large_array = np.ones(int(1e6))
```

放到文件里用来做共享内存：

```
import tempfile
import os
from joblib import load, dump

temp_folder = tempfile.mkdtemp()
filename = os.path.join(temp_folder, 'joblib_test.mmap')
if os.path.exists(filename): os.unlink(filename)
_ = dump(large_array, filename)
large_memmap = load(filename, mmap_mode='r+')
```

```
>>> large_memmap.__class__.__name__, large_array.nbytes, large_array.shape
('memmap', 8000000, (1000000,))

>>> np.allclose(large_array, large_memmap)
True
```

可以从主进程里释放远处的数组

```
del large_array
import gc
_ = gc.collect()
```

```
small_memmap = large_memmap[2:5]
>>> small_memmap.__class__.__name__, small_memmap.nbytes, small_memmap.shape
('memmap', 24, (3,))
```

np.ndarray view:

```
>>> small_array = np.asarray(small_memmap)
>>> small_array.__class__.__name__, small_array.nbytes, small_array.shape
('ndarray', 24, (3,))
```

上面实现的3个新的数据结构，全部指向的是同一个内存buffer：

```
>>> Parallel(n_jobs=2, max_nbytes=None)(
...     delayed(has_shareable_memory)(a)
...     for a in [large_memmap, small_memmap, small_array])
[True, True, True]
```

最后要记得清洗临时文件夹：

```
>>> import shutil
>>> try:
...     shutil.rmtree(temp_folder)
... except OSError:
...     pass  # this can sometimes fail under Windows
```

------------------

### Persistence

对于大的numpy的arrays，joblib.dump和joblib.load提供了对pickle的替换。

```
from tempfile import mkdtemp
savedir = mkdtemp()
import os
filename = os.path.join(savedir, 'test.pkl')
```

```
import numpy as np
to_persist = [('a', [1, 2, 3]), ('b', np.arange(10))]
```

```
import joblib
joblib.dump(to_persist, filename)
>> ['...test.pkl', '...test.pkl_01.npy']
```

```
joblib.load(filename)
>> [('a', [1, 2, 3]), ('b', array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]))]
```

当使用joblib.dump去存储数据的时候，joblib把数据是存储在多个文件中，因此当
转移pickle文件时，需要把所有的文件一起拷贝。

#### 压缩joblib pickle

```
>>> joblib.dump(to_persist, filename, compress=True)  
['.../test.pkl']
```

### 兼容性

joblib不支持不同python版本之间的兼容性。

···end···
