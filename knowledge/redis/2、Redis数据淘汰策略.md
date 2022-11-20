# 1、数据淘汰策略

## 1.1 引入

​	MySQL里有2000w数据，Redis中只存20w的数据，如何保证Redis中的数据都是热点数据（redis有哪些数据淘汰策略？？？）

相关知识：redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略（回收策略）。redis 提供 6种数据淘汰策略：

## 1.2 淘汰策略

1. **volatile-lru**：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰 
2.  **volatile-ttl**：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰 
3.  **volatile-random**：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰 
4. **allkeys-lru**：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰 
5. **allkeys-random**：从数据集（server.db[i].dict）中任意选择数据淘汰 
6. **no-enviction**（驱逐）：禁止驱逐数据

