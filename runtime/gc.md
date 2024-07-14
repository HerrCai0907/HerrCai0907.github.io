### 标记清除算法（Mark Sweep GC）

- 广度优先搜索（BFS）和深度优先搜索（DFS）
  - 扫描时采用 DFS，较 BFS 内存占用较低
- 保守式 GC：对象不可移动
- 碎片化：fragmentation
- 不兼容 copy-on-write（写时复制技术）
  - 即使没有修改对象，GC 会不停修改所有活动对象的标志位，导致 fork 的进程无法共享内存
- 利用多个分块大小不同的空闲链表加速内存分配
- BiBOP（Big Bag of Pages）解决碎片化
  - 将堆分割为固定大小的块，每个块只能配置同样大小的对象
  - 降低堆的使用效率
- 位图表格解决写时复制
  - 收集对象的标记位并表格化，与对象分离进行管理
  - 一般一字对应一位
  - 使用多个堆对象地址不连续时一般一个堆配置一个位图表格
- 延迟清除法
  - 在对象分配过程中使用`lazy_sweep`进行内存分配，将清理过程拆散至每一个分配过程，减少停机时间

### 引用计数法

- 循环引用
- 延迟引用计数避免从根的引用计数频繁更新
  - 将从根的引用的指针变化不反映中计数器上
  - 使用 ZCT（zero count table）储存引用计数为 0 的对象（待回收 和 从根节点引用）
  - `scan_zct`时恢复从根引用指针的计数，清理，恢复引用计数
- Sticky 引用计数：减少计数器位宽
- 计数器溢出
  - 不继续处理，引用数为 0 也不再回收
  - 使用标记清除算法进行管理

```cpp
void mark_phase() {
  for(r : roots) {
    mark_stack.push_back(r);
  }
  while (mark_stack.empty() == false) {
    obj = mark_stack.pop();
    obj.ref_cnt++;
    if (obj.ref_cnt == 1) {
      for (child : obj.children) {
        mark_stack(child);
        }
    }
  }
}

void sweep_phase() {
 while (sweeping < heap_end) {
   if (sweeping.ref_cnt == 0) {
     reclaim(sweeping);
   }
   sweeping += sweeping.size;
 }
}
```
