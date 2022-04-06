#! https://zhuanlan.zhihu.com/p/494105108
# Vscode插件开发的总结

最近做了一个Vscode的插件，在这里说一下，自己踩过的坑，遇到的问题。   

## 文件的路径问题    

在vscode中运行插件，如果使用相对路径，那么它是相对于code.exe的，如果你想在一个路径执行一些操作，那么你可以使用（当然很不推荐）使用绝对路径。  

### 使用对话框   

```
vscode.window.showInputBox({ // 这个对象中所有参数都是可选参数
      password:false, // 输入内容是否是密码
      ignoreFocusOut:true, // 默认false，设置为true时鼠标点击别的地方输入框不会消失
      placeHolder:'提示信息', // 在输入框内的提示信息
      prompt:'', // 在输入框下方的提示信息
    }).then(function(msg){
        your code here
    )};
```
这样会产生一个可供用户输入的对话框，我们可以让用户直接输入目录（也不推荐），或者输入一些信息，更加这些信息。  

例如：  

我们想要获取刚刚使用`git clone`下来的仓库路径，那么我们只需要知道命令执行的路径（更加上面所述我们可以相对于code.exe建一个文件夹用来存放`clone`下的文件）+文件名，所以我们只需要要求用户输入名称即可。  
### 使用文件选择对话框   
```
  // 自定义目录名称
    let options = {
			canSelectFiles: false,		//是否可选择文件
			canSelectFolders: true,		//是否可选择目录
			canSelectMany: false,		//是否可多选
			defaultUri: vscode.Uri.file("B:/Coding"),	//默认打开的文件夹
			openLabel: '选择文件夹'
		};
		//向用户显示“文件打开”对话框，允许用户选择用于打开目的的文件。
		vscode.window.showOpenDialog(options).then(result => {
			if(result === undefined){
				vscode.window.showInformationMessage("can't open dir.");
			}
			else{
                var loadUri = result[0].path.toString();
				var loadDir = loadUri.substr(1, loadUri.length);
                console.log("open dir: " + loadDir);
                your code here
            }
        )};
```
这样是一个很好的选择直接通过文件选择器来获取路径。  
## package.json 的问题    

关于插件信息的配置
```
"name": "插件名",
"repository":"仓库地址",
"publisher": "发布者名",
"displayName": "插件页面名称",
"description":"介绍",
"keywords":["关键词"],
"icon":"图标",
"version": "版本号",
"engines": {
		"vscode": "^1.66.0"
	},
	"categories": [
		"分类"
	],
```
关于插件功能的配置  
```
"activationEvents": [
        "激活事件",
	],
	"main": "入口文件",
	"contributes": {
        "功能贡献点"
	}
```
[详细](https://www.cnblogs.com/liuxianan/p/vscode-plugin-package-json.html)   
## 最后    
做的一个插件，把任意位置的md文推送至您的Github-page站点。  
一个小插件：[Newpage-push](https://marketplace.visualstudio.com/items?itemName=Wildptr.newpage-push)   
仓库：[Github](https://github.com/SongZihui-sudo/newpage-push)   
联系我: 1751122876@qq.com  
我的博客：[我的博客](http://s-zh.space)
Vscode API : [API](https://code.visualstudio.com/api/references/vscode-api)
