## 1.做到一个需求，在类似ant-design-vue-admin工作台界面的卡片需要实现自定义，例如：增加、移除、拖动排序。
在使用`vue` `is`渲染动态组件的时候，发现使用`v-for`循环的时候，设置`:key`为后端返回接口的id，若id不连续，会产生空白；若id相差数值过多，则会显示不出该卡片。  
一般情况下，我们使用`v-for`做渲染的时候，尽量不使用`index`作为`key`，但是该场景下，`index`只作为渲染显示的下标使用，因此改为`key="index"`  
例：`<component v-bind:is="currentTabComponent" v-for="(item, index) of tagName" :key="index"></component>`
## 2.虽然IE都被微软自己淘汰了，但是保不准还是会遇到兼容IE的需求，这不遇到了要兼容IE9的需求了。  
* 文件上传
一般上传都是使用`<input type="file">`，然后`new formData`,我这个例子稍微复制了点，因为后端需要我在上传的时候要在formdata中带参数  
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
但是呢，IE9不支持`formdata`，`e.target.files[0]`也拿不到文件的值（IE10及以上才可以支持），后来找到一个不错的办法，要使用layui的upload组件
* 日历  
`<input type="date">`IE9也不支持，所以可以使用layui的date组件  
* vue中如何使用layui，虽然我不是很想这么做  
（1）在脚手架的public文件夹下将下载好的layui文件夹拷贝过去  
（2）在index.html中这样  
``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>logo.png">
    <title><%= htmlWebpackPlugin.options.title %></title>
    <link rel="stylesheet" href="/layui/css/layui.css">
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
    <script src="/layui/layui.js"></script>
  </body>
</html>
```
（3）在组件中这样使用  
要在mounted中调用，更多使用方法可以看官方api文档，如果要修改成自己想要的样式，可以修改源文件，vue打包的时候会直接将public中文件打包成静态文件，或者你也可以将自己修改好的源文件打包上传到npm。  
[layui]官方文档(https://www.layui.com/doc/)
```javascript
mounted() {
    window.layui.use('laydate', () => {
      const laydate = window.layui.laydate
      laydate.render({
        elem: '#test',
        type: 'datetime',
        done: value => {
          this.createTime = value
        }
      })
    })
  }
```
```javascript
window.layui.use('upload', () => {
  const upload = window.layui.upload
  upload.render({
    elem: '#uploadLicensePop',
    headers: {
      token: obj.token,
      ukToken: obj.ukToken
    },
    data: {
      fileModule: obj.fileModule,
      fileBusinessName: 'BUSINESS_LICENSE',
      businessNo: obj.businessNo,
      fileName: `license${obj.time}`
    },
    url: obj.url,
    done: function(res) {
      if (res.code === 200) {
        that.$toast({
          text: '图片上传成功',
          type: 'success'
        })
        that.text.licenseImgName = 'licenseok'
        that.islicenseUrl = true
      } else {
        if (res.data) {
          that.$toast({
            text: res.data.message,
            type: 'danger'
          })
        }
      }
      if (res.data) {
        window.layui
          .$('#uploadLicenseView')
          .removeClass('layui-hide')
          .find('img')
          .attr('src', `${res.data.fileAbsoluteUrl}&ukToken=${obj.decodeukToken}`)
      }
      console.log(res)
    }
  })
})
```
