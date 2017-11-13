A warehouse used to store images, mainly used for the study notes.

## MVVM原理图描述
创建一个MVVM对象（即new MVVM），MVVM对象有两个角色，一个创建Observer，另一个是创建Compile。Observer用来深度监视data中所有的属性（即：劫持data中所有层次的属性），该对象内部添加了getter、setter方法，同时还为每一个属性创建了一个对象Dep（Dep与data中的属性一一对应），此时dep中的方法只是创建不会调用。

Compile有两方面的功能：  
一个是页面的初始化显示，通过编译指令和表达式，实现页面的初始化界面视图，初始化视图依赖于Updater对象；  
另一个功能是在每次解析表达式或指令的时候都会为每个表达式创建对应的Watcher对象（Watcher对象是与表达式一一对应的，但是排除了事件指令表达式，因为事件指令表达式采用的是原生的DOM方法），一旦watcher创建完毕，就会建立与dep之间的关系（双向、一对多关系）。

watcher要添加到对应的dep中去，只有添加到dep中，在下一次变化才能指定到所有的watcher(一个Watcher可能对应多个dep，如多层结构表达式就会对应着多个属性)，同时Watcher中也关联着dep，用于判断两者之间的关联是否建立过，如果没有就会重新建立，至此就完成了整个界面的初始化过程。

当数据更新（如this.msg = 'TT'），Observer就会接收到的新的数据变化（Observer对象内部有一个setter方法用于接收数据变化），该方法就会通知dep数据发生了变化，而dep则会通知所有相关的watcher去更新（相关方法的是放在subs中的，一个sub对应的就是一个watcher），Watcher则会调用Updater去更新界面。