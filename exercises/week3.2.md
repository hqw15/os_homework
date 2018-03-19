## 6.1非连续内存分配的需求背景

### 1.为什么要设计非连续内存分配机制？

> 提高内存分配的利用率，可以实现内存共享。

### 2.非连续内存分配中内存分块大小有哪些可能的选择？大小与大小是否可变?

> 大块、小块？？？？大块大小可变，小块不能

### 3.为什么在大块时要设计大小可变，而在小块时要设计成固定大小？小块时的固定大小可以提供多种选择吗？

> 大块时可以改变大小，更加灵活，提高利用效率。小块时便于管理，同时小块时对内存效率的影响不大。

## 6.2 段式存储管理
### 1.什么是段、段基址和段内偏移？

> - 段表示访问方式和存储数据等属性相同的一段地址空间  
> - 段基址是段开始的地址  
> - 段内偏移是相对于段基址的偏移量

### 2.段式存储管理机制的地址转换流程是什么？为什么在段式存储管理中，各段的存储位置可以不连续？这种做法有什么好处和麻烦？

> - 地址转换：段基址+段内偏移  
> - 因为段反映了程序的存储逻辑地址，程序不会从一个段的基址访问到另外一个段，因此不同的段可以不连续。  
> - 好处是方便内存管理，提高内存利用率；麻烦事地址转换复杂了一点。

## 6.3 页式存储管理

### 1.什么是页（page）、帧（frame）、页表（page table）、存储管理单元（MMU）、快表（TLB, Translation Lookaside Buffer）和高速缓存（cache）？

> - 页：逻辑地址空间的一段划分大小相同的基本分配单元  
> - 帧：物理地址空间的一段划分大小相同的基本分配单元  
> - 页表：保存了逻辑地址到物理地址的映射关系，由页表基址作为起始地址，由页号作为下标查找相应页表项，页号的对应关系存储在页表中。  
> - 存储管理单元：用来管理虚拟存储器、物理存储器的控制线路，同时也负责虚拟地址映射为物理地址，以及提供硬件机制的内存访问授权，多用户多进程操作系统。  
> - 快表：快表就是存放在高速缓冲存储器的部分页表。它起页表相同的作用。  
> - 高速缓存：高速缓冲存储器是存在于主存与CPU之间的一级存储器， 由静态存储芯片(SRAM)组成，容量比较小但速度比主存高得多， 接近于CPU的速度。

### 2.页式存储管理机制的地址转换流程是什么？为什么在页式存储管理中，各页的存储位置可以不连续？这种做法有什么好处和麻烦？

> - 地址转换：页号+页内偏移  
> - CPU使用连续的逻辑地址，在存储访问时，逻辑地址先分成逻辑页号和页内偏移，然后通过页表定义的对应关系，把逻辑页面转换成物理页号，最后再把物理页号加页内偏移得到物理地址，因此不同的段可以不连续。  
> - 好处是内存不连续，可以方便内存的存储分配和回收；麻烦事地址转换比较复杂以及访存次数增多。

## 6.4 页表概述
### 1.每个页表项有些什么内容？有哪些标志位？它们起什么作用？

> - 有偏移量、页属性信息。
> - 存在标志：用于指明表项对地址转换是否有效。  
读/写标志：确定页面的读写权限。  
用户/超级用户标志。  
已访问标志：处理器是否访问该页表项映射的页面。  
已被修改标志。

### 2.页表大小受哪些因素影响？

> 受硬件本身的影响。

## 6.5 快表和多级页表

### 1.快表（TLB）与高速缓存（cache）有什么不同？

> 快表是部分页表；高速缓存是硬件，是寄存器。快表是存在高速缓存中的。

### 2.为什么快表中查找物理地址的速度非常快？它是如何实现的？为什么它的的容量很小？

> - 因为快表是存在高速缓存中的，而高速缓存的的速度接近于CPU速度。
> - 它是将最近使用过的页表项的附近的部分页表项存在高速缓存中，根据局部性原理，下一次使用周边的页表项的概率很大。
> - 因为高速缓存很贵。

### 3.什么是多级页表？多级页表中的地址转换流程是什么？多级页表有什么好处和麻烦？

> - 多级页表是将页表作为多次索引，成树状结构的页表。
> - 由第一个页表页号查找出第二个页表地址，以此类推，直到确定出物理地址。
> - 好处是可以减少页表的存储空间。麻烦时确定物理地址需要经过多次的页表访问。

## 6.6 反置页表

### 1.页寄存器机制的地址转换流程是什么？

> 逻辑地址进行hash,然后查找响应的页寄存器。

### 2.反置页表机制的地址转换流程是什么？

> 逻辑地址和进程号共同HASH，然后查找响应的页寄存器。

### 3.反置页表项有些什么内容？

> PID/逻辑页号/标志位

## 6.7 段页式存储管理

### 1.段页式存储管理机制的地址转换流程是什么？这种做法有什么好处和麻烦？

> - 第一次访问内存中的段表，从中取得页表起始地址，然后访问内存中的页表，从中取出该页所在的物理块号，并将该块号与页内地址一起形成指令或数据的物理地址。
> - 好处可以实现内存共享，结合了段表和页表的优点；麻烦时地址转换复杂，需要3次访存。

### 2.如何实现基于段式存储管理的内存共享？

> 段表项中的基址一样。

### 3.如何实现基于页式存储管理的内存共享？

> 页表项中的基址一样。