
## MySQL常用排序规则utf8mb4_general_ci、utf8mb4_unicode_ci、utf8mb4_bin、utf8mb4_0900_ai_ci和存储字符集 utf8 和 utf8mb4


## utf8和utf8mb4的区别
* utf8用3Byte保存，不能存储emoji(Emoji 是一种特殊的 Unicode 编码)
* utf8mb4用4Byte存储，是utf8的超集完全兼容utf8，要看mysql的版本(5.5.3以后支持)，更占空间了。

## 常见的utf8mb4排序规则说明

* utf8mb4_unicode_ci：
是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序，Unicode排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。
* utf8mb4_general_ci（简单理解不能用于unicode的比对，精度不够可能出错）：
是一个遗留的 校对规则，不支持扩展，它仅能够在字符之间进行逐个比较。utf8_general_ci校对规则进行的比较速度很快，但是与使用 utf8mb4_unicode_ci的校对规则相比，比较**正确性较差**。
* utf8mb4_bin：
将字符串每个字符用二进制数据编译存储，区分大小写，而且可以存二进制的内容。
* utf8mb4_0900_ai_ci（是utf8mb4_unicode_ci的升级版本，mysql8默认）：
MySQL 8.0 默认的是 utf8mb4_0900_ai_ci，属于 utf8mb4_unicode_ci 中的一种，具体含义如下：
uft8mb4 表示用 UTF-8 编码方案，每个字符最多占 4 个字节。
0900 指的是 Unicode 校对算法版本。（Unicode 归类算法是用于比较符合 Unicode 标准要求的两个 Unicode 字符串的方法）。
ai 指的是口音不敏感。也就是说，排序时 e，è，é，ê 和 ë 之间没有区别。
ci 表示不区分大小写。也就是说，排序时 p 和 P 之间没有区别。
utf8mb4 已成为默认字符集，在 MySQL 8.0.1 及更高版本中将 utf8mb4_0900_ai_ci 作为默认排序规则。以前，utf8mb4_general_ci 是默认排序规则。由于 utf8mb4_0900_ai_ci 排序规则现在是默认排序规则，因此默认情况下新表格可以存储基本多语言平面之外的字符。现在可以默认存储表情符号。如果需要重音灵敏度和区分大小写，则可以使用 utf8mb4_0900_as_cs 代替。

## 排序性能
* utf8mb4_general_ci 在比较和排序的时候**更快**
* utf8mb4_unicode_ci在比较和排序的时候**更准**