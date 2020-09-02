# fed-e-task-03-02

### 一、简答题

#### 1、请简述 Vue 首次渲染的过程。
- ~~1.在Vue实例方法init中首先设置_isVue标记，将来在响应式处理中不进行响应式处理~~
- ~~2.判断当前Vue实例是否是组件，如果是则合并组件options选项
- ~~3.否则合将用户传进来得options与Vue构造函数中的options进行合并
- ~~4.设置渲染代理对象（如果是开发环境调用initProxy否则将vue实例设置给_renderProxy）
- ~~5.initProxy方法中判断了当前环境是否支持Proxy，如果支持将_renderProxy设置new Proxy代理否则将vue实例设置给_renderProxy
- 6.如果传入el参数，开始调用$mount方法传入el
- 7.$mount方法中先判断options中是否有render函数，如果没有则拿到options中template进行判断
- 8.通过判断template如果是字符串，并且模板是id选择器则调用idToTemplate方法获取dom元素的innerHTML返回作为模板
- 9.如果template是nodeType，则直接调用其innerHTML作为模板
- 10.如果都不是则返回当前实例，并给出警告当前模板不合法
- 11.如果没有传入template参数，获取el的outerHTML作为模板
- 12.拿到template后开始调用compileToFunctions把template编译成render函数，并存储到options中
- 13.调用mount方法
- 14.如果是运行时版本则调用的是runtime/index中的$mount方法，此方法中调用mountComponent方法
- 15.在mountComponent中判断options中是否有render函数，如果是运行时环境并且传入了模板则给出警告
- 16.然后调用callHook方法触发生命周期函数beforeMount
- 17.定义updateComponent函数，其内部调用了实例中的render函数返回虚拟dom，然后调用_update函数将虚拟dom传入，然后_update将虚拟dom转换成真实dom更新到界面
- 18.创建渲染Watcher对象，把上面定义的updateComponent函数传递进去
- 19.在Watcher中判断了第二个参数是否是function，然后将传进去的updateComponent直接赋值给了getter
- 20.在赋值this.value时，调用了this.get方法，get方法中调用了getter方法，而getter就是传进去的updateComponent方法，此时调用了_update方法完成渲染
- 21.最后Watcher执行完成，调用callHook方法触发生命周期函数mounted，挂载完毕，首次渲染完成
#### 2、请简述 Vue 响应式原理。

#### 3、请简述虚拟 DOM 中 Key 的作用和好处。

#### 4、请简述 Vue 中模板编译的过程。
