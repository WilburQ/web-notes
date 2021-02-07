## 1.axios请求拦截和响应拦截  
```javascript
// 请求拦截
axios.interceptors.request.use(
  config => {
    const ukToken = sessionStorage.getItem('ukToken')
    config.headers.token = token
    config.headers.ukToken = ukToken
    return config
  },
  error => {
    return Promise.error(error)
  }
)

// 响应拦截
axios.interceptors.response.use(
  response => {
    if (response.status === 200) {
      return response
      // console.log(response)
    } else {
      Promise.reject()
    }
  },
  error => {
    console.log(error)
    if (error.response.status === 403) {
      alert('账号已在其它地方登陆！')
      window.location.href = '/'
    }
    if (error.response.status === 401) {
      alert('未授权！')
      window.location.href = '/'
    }
    return Promise.reject(error)
  }
)
```
