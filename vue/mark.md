## 1.做到一个需求，在类似ant-design-vue-admin工作台界面的卡片需要实现自定义，例如：增加、移除、拖动排序。
在使用`vue` `is`渲染动态组件的时候，发现使用`v-for`循环的时候，设置`:key`为后端返回接口的id，若id不连续，会产生空白；若id相差数值过多，则会显示不出该卡片。  
一般情况下，我们使用`v-for`做渲染的时候，尽量不使用`index`作为`key`，但是该场景下，`index`只作为渲染显示的下标使用，因此改为`key="index"`  
例：`<component v-bind:is="currentTabComponent" v-for="(item, index) of tagName" :key="index"></component>`
## 2.虽然IE都被微软自己淘汰了，但是保不准还是会遇到兼容IE的需求，这不遇到了要兼容IE9的需求了。  
* 文件上传
一般上传都是使用`<input type="file">`，然后`new formData`  
```javascript
uploadLicense($event) {
  // 获取上传图片信息
  const file = $event.target.files[0]
  if (file.type.indexOf('image') === -1) {
    this.$toast({
      text: '请上传图片文件！！！',
      type: 'danger'
    })
  } else {
    const that = this
    let formData = new FormData()
    formData.append('file', file)
    // 其它数据，json格式上传
    let fileInfo = {
      fileName: file.name,
      businessNo: that.businessNo,
      fileModule: 'project_module',
      fileBusinessName: 'business_license'
    }
    // 以文件流的形式在请求主体中发送json
    formData.append('params', new Blob([JSON.stringify(fileInfo)], { type: 'application/json' }))
    const tagName = 'license'
    this.uploadFile(formData, tagName)
  }
  $event.target.value = ''
},
```
