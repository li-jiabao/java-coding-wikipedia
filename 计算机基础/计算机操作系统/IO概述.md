# IO概述

网络IO：实质上就是应用程序和内核内部的队列之间的IO（读取写入）（socket连接怎么区分是哪个和哪个的连接，采用四元组（ip:port+ip:port）来区分）

本地IO：实质上是指应用程序和本地磁盘之间的IO，只是因为内核会将磁盘的内容写入到内核的pagecache缓存当中保存以重复使用，有缓存就会涉及到一致性的问题（<font color='red'>双写一致性</font>）