### 游戏内存修改器原理及游戏敏感数值加密思路

主流游戏内存修改器有三大内存搜索功能:
* 基础数值搜索
* 模糊搜索
* 反加密搜索

基础数值搜索是这么一个工作流程：首先搜一个特定的数值，比如金钱100就搜100，待金钱变化后，比如110就再搜110，如此重复3到4步操作，内存修改器就能定位到金钱所在的内存地址，接着就可以进行修改了。

模糊搜索是针对看不到具体数值的数值的修改（比如柱状血量、柱状气槽以及某些加密了的数值等）。模糊搜索是这么一个工作流程：首先启动模糊搜索，然后在游戏中等待要修改的数值发生变化，搜索“变大”、“变小”、“变化了”、“没变化”等操作，进行内存筛选，如此重复7到10步操作，就能定位到要修改的数值的内存地址，接着就可以进行修改了。

反加密搜索是更为强悍的内存搜索功能，和基础数值搜索是同样一个使用流程，但根据反加密强度，可以轻松破解多种常见的数值加密算法：

* 倍值加密算法，`y = n * x + m`。其中`x`为加密前的数值，`y`为内存中的数值，`n`为任意正负整数。 `y = (x << n) + m`也属于这种算法，`m`为任意正负整数。
* 异或加密算法，`y = n ^ x + m`。其中`x`为加密前的数值，`y`为内存中的数值，`n`为任意正负整数，`m`为任意正负整数。

前述三大内存搜索功能，都基于一个前提：要修改的数值，在内存中的地址是不变的或者在定位过程中是不变的。

所以，防御内存修改器的方法是：不断变化敏感数值的内存存储地址，敏感数值不留明文并随时更换密钥（比如将读写变量的方法封装成set和get两方法， 调用set方式时自动切换变量存储地址、变化加密密钥等）。

此外，游戏修改器在找不到直接修改游戏中的金钱、血量、经验值等敏感数据的时候，通常还采用“曲线救国”的方法：比如修改商店中物品的价格，改为负数，买商品，越买钱越多；比如修改每一局游戏中打到的怪物数量，结算的时候就可以获得更多的奖励；比如修改某一物品的数量，然后定位到该物品所在地址，修改该物品代码，把垃圾装备变成黄金装备等。这些数据，都是要格外注意保护的。
