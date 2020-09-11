# fed-e-task-03-02

### 一、简答题

#### 1、请简述 Vue 首次渲染的过程。
- ~~1.在Vue实例方法init中首先设置_isVue标记，将来在响应式处理中不进行响应式处理~~
- ~~2.判断当前Vue实例是否是组件，如果是则合并组件options选项~~
- ~~3.否则合将用户传进来得options与Vue构造函数中的options进行合并~~
- ~~4.设置渲染代理对象（如果是开发环境调用initProxy否则将vue实例设置给_renderProxy）~~
- ~~5.initProxy方法中判断了当前环境是否支持Proxy，如果支持将_renderProxy设置new Proxy代理否则将vue实例设置给_renderProxy~~
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
- 1.Vue的响应式从Vue的实例init()方法开始，在init()方法中先调用initState()初始化Vue实例的状态，在initState方法中调用了initData()， initData()是把data属性注入到Vue实例上，并且调用observe(data)将data对象转化成响应式的对象
- 2.observe是响应式的入口, 在observe(value)中，首先判断传入的参数value是否是对象，如果不是对象直接返回。然后判断value对象是否有__ob__这个属性标记，如果有说明做过了响应式处理，则直接返回，如果没有，创建observer对象，最后返回observer对象
- 3.在observer构造函数中，首先为当前的value对象定义不可枚举的__ob__属性，并将observer对象记录__ob__属性中，然后再进行数组的响应式处理和对象的响应式处理，数组的响应式处理就是拦截数组的几个特殊的方法，push、pop、shift、sort等，当这些方法被调用会发送通知，然后找到数组对象中的__ob__对象中的dep，调用dep的notify()方法，再遍历数组中每一个成员，对每个成员调用observer()，如果这个成员是对象的话，也会转换成响应式对象。对象的响应式处理，是调用walk方法，walk方法就是遍历对象的属性，对每个属性调用defineReactive方法
- 4.defineReactive会为每一个属性创建对应的dep对象，让dep去收集依赖，如果当前属性的值是对象，会调用observe转换成响应式对象。defineReactive中最核心的是getter 和 setter。getter 的作用是收集依赖，收集依赖时, 为每一个属性收集依赖，如果这个属性的值是对象，那也要为子对象收集依赖，最终返回属性的值。在setter 中，先保存新值，如果新值是对象，也要调用 observe ，把新设置的对象也转换成响应式的对象，然后发送通知，调用dep.notify()
- 5.收集依赖时，在watcher对象的get方法中调用pushTarget，记录Dep.target属性，访问data中的成员的时候收集依赖，defineReactive的getter中收集依赖，把属性对应的 watcher 对象添加到dep的subs数组中，为childOb（数组中发送变化时用到的）收集依赖，目的是当子对象发送变化（添加和删除成员）时发送通知。
- 6.Watcher中，在数据发生变化的时候，会调用dep.notify()发送通知，dep.notify()会调用watcher对象的update()方法，update()中的调用的queueWatcher()会去判断watcher是否被处理，如果这个watcher对象没有的话添加到queue队列中，并调用flushScheduleQueue()刷新任务队列，flushScheduleQueue()触发beforeUpdate钩子函数调用watcher.run()，此方法会调用结构 run() --> get() --> getter() --> updateComponent()
- 7.然后清空上一次的依赖，重置watcher中状态
- 8.触发actived的钩子函数
- 9.触发updated钩子函数

#### 3、请简述虚拟 DOM 中 Key 的作用和好处。
- 在数据更新时可减少dom操作
- 在列表渲染时提高列表渲染效率

#### 4、请简述 Vue 中模板编译的过程。
- compileToFunctions入口函数中，先从缓存中加载编译好的render函数，如果缓存中没有则调用compile开始编译
- compile函数中，核心功能是合并options选项，然后调用baseCompile将options传递
- baseCompile函数中，首先把template字符串转换成AST对象（抽象语法树），然后对AST tree进行优化标记，标记其中的所有静态跟节点（不需要在每次重新渲染的时候重新生成节点，patch过程中会跳过根节点），最后把优化后的AST对象转换成js创建代码
- 回到compileToFunctions中，通过调用createFunction继续把上一步生成的字符串js转换为函数，当render和staticRenderFns创建化完成，最终会被挂载到Vue实例的options对应的属性中
