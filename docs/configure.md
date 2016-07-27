#1.svc 安装
```bash
sudo npm install svc -g
```
### 远端服务器配置
当我们开发项目后，需要发布到测试机或生产环境，可以通过使用 HTTP 上传代码。

#### 首先，需要在测试机部署上传接收脚本（或者服务）：

* php脚本:  **https://github.com/fex-team/fis-command-release/blob/master/tools/receiver.php**
* node实现： **https://github.com/fex-team/receiver**

#### 其次，在fis3配置中加入


```
	//如配置测试环境(qa)下的远程发布
	//这里一定要注意远端目录是example1(如果远端没有会新建example1目录), 用来接收所有代码
	//这个时候，svc release 命令中加参数 -d [输出目录] 无效
	fis.media('qa').match('*', {
    	deploy: fis.plugin('http-push', {
        	receiver: 'http://10.2.132.102:8999/receiver',
        	//远端目录
        	to: 'D:\\upload\\example1'
    	})
	});
```
### 将代码部署到指定git

#### 1. 在用户的主目录(linux: ~,windows: %USERPROFILE%)新建配置文件_svc_git_config.json,内容为json格式的git部署配置信息

```
   {
     "to": "/upload-test-1", //上传到的git目录,如果是'/'则从根目录开始覆盖
     "remote": "git@git.d.sohu.com:h5-release/static.git", //上传git地址
     "branch": "master", //git分支
     "clean": false, //上传过程中是否执行git rm ./* -r
     "add-tag": true, //是否打tag
     "tag-prefix": "h5_" //tag前缀, 最终tag是[前缀+v+时间戳]
   }
```

#### 2. 在config/default.js中对应的media中添加deploy(可以为数组, 即多个部署方案),如下, 最终代码既上传到远程服务器,又会添加到指定git

```
    .match('*', {
       deploy: [fis.plugin('http-push', {
           receiver: 'http://10.16.48.40:8999/receiver',
           //远端目录
           to: deploy_dir
       }), svcPlugin.gitMod]
   });
```