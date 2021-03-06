## 什么是Actor引用

Actor引用是ActorRef的子类，其最重要的目的是支持向它所代表的actor发送消息。每个actor通过self字段来访问自己的标准引用；在给其它actor发送的消息中也缺省包含这个引用。反过来，在消息处理过程中，actor可以通过sender()方法来访问到当前消息的发送者的引用。

## 什么是Actor路径

由于actor是以一种严格的树形结构样式来创建的，所以沿着子actor到父actor的监管链，一直到actor系统的根存在一条唯一的actor名字序列。这个序列可以被看做是文件系统中的文件路径，所以我们称之为路径。就像在一些真正的文件系统中一样，也存在所谓符号链接，即一个actor也许能通过不同的路径访问到，除了原始路径外，其它路径都涉及到对actor实际监管祖先链的某个路径进行转换的方法。

### Actor引用和路径之间的区别

Actor引用标明了一个actor，其生命周期和actor的生命周期保存匹配；actor路径表示一个名称，其背后可能有也可能没有真实的actor，而且路径本身不具有生命周期，它永远不会失效。你可以创建一个actor路径，而无需创建一个actor，但你不能在创建actor引用时不创建相应的actor。
