## 1.做到一个需求，在类似ant-design-vue-admin工作台界面的卡片需要实现自定义，例如：增加、移除、拖动排序。
在使用`vue` `is`渲染动态组件的时候，发现使用`v-for`循环的时候，设置`:key`为后端返回接口的id，若id不连续，会产生空白；若id相差数值过多，则会显示不出该卡片。
一般情况下，我们使用`v-for`做渲染的时候，尽量不使用`index`作为`key`，但是该场景下，`index`只作为渲染显示的下标使用，因此改为`key="index"`  
`<component v-bind:is="currentTabComponent" v-for="(item, index) of tagName" :key="index"></component>`
