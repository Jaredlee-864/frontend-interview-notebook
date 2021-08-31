### vue的插槽以及插槽有哪几种

> 插槽`<slot>`可以理解为一个占位符，这个占位符将来可以承接组件标签内部的内容。

- 分类
  1. 默认插槽
   ```html
   <a
      v-bind:href="url"
      class="nav-link"
    >
      <slot></slot>
    </a>
   ```

  2. 具名插槽
   ```html
   // 定义
    <div class="container">
      <header>
        <slot name="header"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>

    // 使用
    <base-layout>
      <template slot="header">
        <h1>Here might be a page title</h1>
      </template>

      <p>A paragraph for the main content.</p>
      <p>And another one.</p>

      <template slot="footer">
        <p>Here's some contact info</p>
      </template>
    </base-layout>

    // 或者直接在元素上使用
    <base-layout>
      <h1 slot="header">Here might be a page title</h1>

      <p>A paragraph for the main content.</p>
      <p>And another one.</p>

      <p slot="footer">Here's some contact info</p>
    </base-layout>
   ```

  3. 作用于插槽（注：slot-scope 已被废弃，由 v-slot 替代）
   ```html
   // 定义
    <ul>
      <li
        v-for="todo in todos"
        v-bind:key="todo.id"
      >
        <!-- 我们为每个 todo 准备了一个插槽，-->
        <!-- 将 `todo` 对象作为一个插槽的 prop 传入。-->
        <slot v-bind:todo="todo">
          <!-- 回退的内容 -->
          {{ todo.text }}
        </slot>
      </li>
    </ul>

    // 使用
    <todo-list v-bind:todos="todos">
      <!-- 将 `slotProps` 定义为插槽作用域的名字 -->
      <template slot-scope="slotProps">
        <!-- 为待办项自定义一个模板，-->
        <!-- 通过 `slotProps` 定制每个待办项。-->
        <span v-if="slotProps.todo.isComplete">✓</span>
        {{ slotProps.todo.text }}
      </template>
    </todo-list>
   ```

### computed 和 watch 的区别
- computed 计算属性
  - 是为了解决模板中出现太多的逻辑，导致模板过重和难以维护问题。
  - 监听所依赖的值，当依赖值发生变化时，才会重新求值。（这是与method 的区别）
- watch 方法
  - 当需要在某个数据变化时执行异步或者开销较大的操作时，使用watch

- 两者区别
  - watch 监听的是属性值，只要属性值发生变化，都会触发相应的逻辑
  - computed 监听的依赖值，依赖值不变的情况下会直接读取缓存进行复用，变化时会重新计算
  - watch 可以执行异步操作，而 computed 不能，必须是同步的
  - 本质上来讲，computed 和 watch 都是一个 watcher，当所依赖的数据发生变化时，就会发送通知，通知 watcher 进行更新。不同的是，computed 属性可能会依赖一个甚至多个值，每当其中一个任意一个变化时，都会触发这个计算属性watcher进行更新。而 watch 则是依赖一个特定的值，当这个值发生变化时，会触发这个侦听器的watcher 进行更新。
  - 计算属性适合用在模板渲染中，某个值是依赖其他的响应式对象甚至是计算属性计算而来；而侦听器适用于观测某个值的变化去完成一系列复杂的业务逻辑

### vue 父子组件的生命周期是怎样的？
- 加载顺序：父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate -> 子created -> 子beforeMount -> 子mounted -> 父mounted
- 更新顺序：父beforeUpdate->子beforeUpdate->子updated->父updated
- 销毁顺序：父beforeDestroy->子beforeDestroy->子destroyed->父destroyed
