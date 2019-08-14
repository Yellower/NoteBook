# vscode 实时同步代码到远程服务器

### 1.安装sftp插件

vscode扩展中直接搜索安装

### 2.配置当前的项目

打开需要同步的项目文件夹，`Ctrl+Shift+P`调出输入框，输入`sftp`，选择`SFTP:Config`回车，程序自动在`.vscode`目录中创建`sftp.json`配置文件，配置如下：

```json
{
    "name": "myserver",
    "host": "服务器IP",    
    "port": 22,     
    "username": "用户名", 
    "password": "密码", 
    "protocol": "sftp", 
    "passive": false,
    "interactiveAuth": false,
    "remotePath": "服务器上项目的路径，如/usr/share/nginx/mwServer/web/laravel/",   
    "uploadOnSave": true, 
    "syncMode": "update",
    "ignore": [            
        "**/.vscode/**",
        "**/.git/**",
        "**/.DS_Store"
    ]
}
```

3.上传本地代码到服务器

`Ctrl+Shift+pP调出输入框，选择`SFTP:Upload`回车即可将项目上传到服务器中。

修改本地代码后，只需要`Ctrl+S`保存即会自动上传到服务器中。在服务器上直接修改代码不会对本地的代码有影响。