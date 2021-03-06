> - alloc 负责创建对象，这个过程包括分配足够的内存来保存对象，写入 isa 指针，初始化引用计数，以及重置所有实例变量。
- init 负责初始化对象，这意味着使对象处于可用状态。这通常意味着为对象的实例变量赋予合理有用的值。

alloc 方法将返回一个有效的未初始化的对象实例。每一个对这个实例发送的消息会被转换成一次 `objc_msgSend()` 函数的调用，形参 self 的实参是 alloc 返回的指针；这样 self 在所有方法的作用域内都能够被访问。 按照惯例，为了完成两步创建，新创建的实例第一个被调用的方法将是 init 方法。注意，NSObject 在实现 init 时，只是简单的返回了 self。

关于 init 的约定还有一个重要部分：这个方法可以（并且应该）通过返回 nil 来告诉调用者，初始化失败了；初始化可能会因为各种原因失败，比如一个输入的格式错误了，或者另一个需要的对象初始化失败了。 这样我们就能理解为什么总是需要调用`self = [super init]`。如果你的父类说初始化自己的时候失败了，那么你必须假定你正处于一个不稳定的状态，因此在你的实现里不要继续你自己的初始化并且也返回 nil。如果不这样做，你可能会操作一个不可用的对象，它的行为是不可预测的，最终可能会导致你的程序崩溃。
init 方法在被调用的时候可以通过重新给 self 重新赋值来返回另一个实例，而非调用的那个实例。例如类簇，还有一些 Cocoa 类为相等的（不可变的）对象返回同一个实例。
