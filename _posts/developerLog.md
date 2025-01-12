+++
title = 'DeveloperLog'
date = 2024-04-04T14:58:15+08:00
draft = true
+++

#### 2024

##### 4/2

为什么 `* http.Response` 无法被当成参数传进去。我只能把 []byte 类型的 解析之后的response body 作为参数传进去进行 JSON schema 检查了。

##### 4/3

加了个captcha.go，做法是先生成随机字符串(specific length)，再根据该字符串在空白的图片上绘制文字(using third-party package)，最后返回给client。 

##### 4/4

我正在构思如何选择api的设计模式。我决定移除github上的`test/database/` 和 `.vscode/`。虽然它们本来就不应该上传上去。

`gorilla/mux` 优势：

1. 动态路由
   
   ```go
   router.HandleFunc("/users/{id}", getUser).Methods("GET")
   
   func getUser(w http.ResponseWriter, r *http.Request) {
       params := mux.Vars(r)
       userID := params["id"]
       // 使用userID从数据库或其他存储中获取用户信息...
   }
   ```

2. 为同一路径注册多个HTTP方法
   
   ```go
   router.HandleFunc("/articles/{slug}", articleHandler).
       Methods("GET", "PUT", "DELETE")
   func articleHandler(w http.ResponseWriter, r *http.Request) {
       slug := mux.Vars(r)["slug"]
   
       switch r.Method {
       case http.MethodGet:
           getArticle(w, r, slug)
       case http.MethodPut:
           updateArticle(w, r, slug)
       case http.MethodDelete:
           deleteArticle(w, r, slug)
       default:
           w.WriteHeader(http.StatusMethodNotAllowed)
       }
   }
   ```

3. 为一组共享相同前缀的路由创建“子路由器” 
   
   ```go
   adminRouter := router.PathPrefix("/admin").Subrouter()
   adminRouter.HandleFunc("/users", listAdminUsers).Methods("GET")
   adminRouter.HandleFunc("/users/{id}", getAdminUser).Methods("GET")
   adminRouter.HandleFunc("/settings", updateAdminSettings).Methods("PUT")
   
   publicRouter := router.PathPrefix("/").Subrouter()
   publicRouter.HandleFunc("/users", listPublicUsers).Methods("GET")
   ```

4. 自定义中间件
   
   ```go
   middleware := []mux.MiddlewareFunc{
       loggingMiddleware,
       authenticationMiddleware,
       responseCompressionMiddleware,
   }
   
   router.Use(middleware...)
   
   router.HandleFunc("/", homeHandler).Methods("GET")
   ```
   
   我对context 感到疑惑。

少打了个 `/` , 完整的应该是 `/api/config/{key}`，debug了好久。

##### 4/5

现在已经可以用了。代理服务仍然是在8084端口，API在8085端口。其实应该也可以布置在同一个端口的。先push了再说。

稍微了解了一下Go的**channel**，**context**.  另外，interface 好像和其他语言的用法差不多。

##### 4/10

好像不可以使用 map[string][]interface{}，用 map[string]interface{} 就可以达到相同的效果，即 `interface{}`已经包含了 `[]interface{}`

##### 4/12

[装个npm和node.js可真废劲](https://github.com/facebook/create-react-app/discussions/10173)

The error message you're seeing indicates that the `create-react-app` tool requires at least Node.js version 14, but you are currently running Node.js version 10.19.0. This version mismatch is why you're unable to proceed with creating a new React application.

To resolve this, you need to update your Node.js to a version that is 14 or higher. Here's how you can do it:

1. **Uninstall the current version of Node.js**
   
   If you installed Node.js via Ubuntu's package manager, you can uninstall it using the following command:

bash

```
   sudo apt remove nodejs npm
```

2. **Install Node.js using NodeSource**
   
   NodeSource provides more up-to-date versions of Node.js than the official Ubuntu repositories. To install a newer version, you can use the NodeSource repository.
   
   First, you can use curl to retrieve the installation script for your desired version of Node.js (replace `14.x` with `16.x` or `18.x` if you want a later version):

bash

```
   curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

Then, install Node.js:

bash

```
   sudo apt install -y nodejs
```

3. **Verify the installation**
