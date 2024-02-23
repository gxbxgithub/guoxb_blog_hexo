---
title: Vue环境搭建
categories: Vue
tags:
- 前端框架
- Vue
---

创建一个Vue项目，遇到一些问题，比如：请求接口存在跨域问题怎么办？

这篇文章是我自学Vue遇到的一些问题及总结。

后续会不断更新...
<!-- more -->

### Vue环境搭建

```vue init webpack vuedemo01```

```vue init webpack-simple vuedemo01```

### Error: No PostCSS Config found in...

项目在本地运行不报错，上传到码云，clone到本地安装依赖包，执行

`npm run dev`

报错 Error: No PostCSS Config found in...

解决方案：

在项目根目录新建 postcss.config.js 文件，配置如下：

```
module.exports = {
	plugins: {
		'autoprefixer': {browsers: 'last 5 version'}
	}
}
```

重新run，就好了~

postcss.config.js 是针对webpack3.0做的特殊处理~

### 跨域问题 No 'Access-Control-Allow-Origin' header is present on the requested resource.

在vue中直接请求接口，会存在跨域问题。

解决方案：

例如请求的接口的路径为:

`http://192.168.1.167:8080/api_v1/user/list`

1. 根目录config->index.js配置

```
module.exports = {
	dev: {
		...
		proxyTable: {
			'/cms_api': {
				target: 'http://192.168.1.167:8080',
				changeOrigin: true,
				pathRewrite: {
					'^/cms_api': '/api_v1/'
				}
			}
		}
		...
	}
}
```

2. 请求接口

```
this.getToApi(
	'cms_api/user/list',
	{
		page: 1,
		limit: 20
	},
	result => {
		...
	},
	err => {
		...
	}
)
```

### 组件构成部分

props 传递数据、events 触发事件 和 slot 内容分发 构成Vue组件的3个API来源，再复杂的组件也是由这3部分构成。

### 多层路由+跨域请求接口 问题

路由路径

```
{ path: '/hospital/detail/:hospital_id', name: 'HosDetail', component: HosDetail }
```

config -> index.js跨域配置

```
proxyTable: {
      '/cms_api': {
        target: 'http://192.168.1.172:8080',
        // target: 'http://localhost:8080',
        changeOrigin: true,
        pathRewrite: {
          '^/cms_api': '/hospitalpatrol/admin/'
        }
      }
    }
```

当在 HosDetail 组件下请求接口时，请求路径会变为

`http://localhost:8080/hospital/detail/cms_api/department/hospital`

而正常请求路径应该是

`http://localhost:8080/cms_api/department/hospital`

解决方案：请求拦截

在main.js中配置axios请求拦截

```
//请求拦截
axios.interceptors.request.use((config) => {
  //请求之前重新拼装url
  config.url = process.env.API_ROOT + config.url;
  return config;
});
```

### vue2.0 别人通过ip访问自己运行的项目 被拒绝

1. 在 config -> index.js 文件中将 host 改为 0.0.0.0
2. 在 package.json 文件中修改 dev ：

`"dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js --host 0.0.0.0"`