---
title: overleaf的本地同步配置
date: 2024-04-20 14:04:33
tags:
  - 教程
categories: 编辑器配置
---
因为有多人同步写论文的需求，所以还是用overleaf方便一些，但是我又不习惯在线编辑，更习惯本地vscode写论文，在[overleaf的网站上](https://www.overleaf.com/learn/how-to/Git_integration)看到说可以用git同步，于是研究一下，以下内容基本上是对这个页面的翻译。

bts，网上的很多资料是关于overleaf+GitHub的教程，但是overleaf绑定GitHub得付费升级，而git是免费的。

### 什么是 Overleaf Git 集成？  

Overleaf 的 Git 整合功能允许你从 Overleaf 项目中获取 Git URL，并将其视为远程 Git 仓库（repo）。您可以将项目克隆到本地 Git 仓库，并在本地处理项目，从本地机器向项目推送更改，并将 Overleaf 在线编辑器中的更新拉到本地仓库。  

这样，当在线工作不可行时，您就可以在本地处理项目；在处理 Overleaf 项目时，您可以选择使用不同的本地编辑器；还可以轻松地将其他本地软件的输出添加到 Overleaf 项目中。  

你可以从项目 Overleaf 菜单的 Git 选项中获取项目的 Git URL 和克隆命令。

### 使用 Git 集成时如何进行身份验证  

当你执行克隆、拉取或推送到远程 Overleaf 项目等 Git 操作时，系统会要求你进行身份验证。Overleaf 的 Git 整合有两种身份验证方法。  

1. 基于 Overleaf 令牌的身份验证（首选方法）。具体步骤请参见 基于[令牌](https://www.overleaf.com/learn/how-to/Git_integration_authentication_tokens)的身份验证。  
    
2. Overleaf 基于密码的身份验证（已弃用）。分步说明请参阅 [基于密码的身份验证](https://www.overleaf.com/learn/how-to/Git_integration_password-based_authentication)  
    

请注意，令牌认证是 Overleaf Cloud 用户的首选登录方式，也是 Overleaf Server Pro 用户的唯一登录方式。基于密码的 Git 认证已经过时，今后将不再作为选项。我们鼓励所有用户在访问 Overleaf 项目时使用基于令牌的认证。

### 将项目克隆为本地仓库  

在项目的[**菜单**](https://www.overleaf.com/learn/how-to/Using_the_Overleaf_project_menu)中，你可以从同步选项中选择 Git。这会弹出一个对话框，显示创建本地仓库时要使用的`git clone`命令

或者，你也可以从项目 URL（进入项目时浏览器地址栏中的 URL）中找到 Git 网址。  
#### Overleaf 云  

如果您使用的是 Overleaf Cloud，且 Overleaf 项目 URL 如下：`https://www.overleaf.com/project/1234567`  

那么克隆项目的 Git 网址就是 `https://git.overleaf.com/1234567`

克隆该项目到本地 Git 仓库的命令是 `git clone https://git.overleaf.com/1234567`

**注意**：系统可能会提示输入用户名和密码。请参阅[Git 整合密码验证](https://www.overleaf.com/learn/how-to/Git_integration_password-based_authentication)  

#### Overleaf Server Pro（4.0 或更高版本）  

**注意**：Server Pro 默认未启用 Git 桥集成。请参阅我们的[Git](https://github.com/overleaf/overleaf/wiki/Git-Integration)整合指南，了解如何启用。  

如果你正在使用 Overleaf Server Pro，而你的项目 URL 看起来像这样：  `https://<SHARELATEX_SITE_URL>/project/1234567`    

那么克隆项目时使用的 Git URL 将是 `https://git@<SHARELATEX_SITE_URL>/git/1234567`   

使用 Git 命令行将项目克隆到本地 Git 仓库的命令是  `git clone git@<SHARELATEX_SITE_URL>/git/1234567`

**注意**：系统可能会提示您输入密码。请参阅 [Git 整合身份验证令牌](https://www.overleaf.com/learn/how-to/Git_integration_authentication_tokens)

### 从现有 Git 仓库创建 Overleaf 项目  

如果你已有一个 Git 仓库，就可以添加 Overleaf 项目作为远程仓库。下面有几条命令，你需要运行它们才能开始工作。请注意，Overleaf 只支持每个项目一个线性历史记录。Git 集成通过将分支数量限制为一个来实现这一限制。这一个分支目前被硬编码为`master`。  

1. 首先，我们假设您的电脑上有一个名为`my-paper` 的文件夹中的 Git 仓库。(它也可能托管在 GitHub、GitLab 或 Bitbucket 等网站上，但也需要克隆到本地）。      
2. 在 Overleaf 上创建[一个新项目](https://www.overleaf.com/learn/how-to/Creating_a_document_in_Overleaf)。由于项目中的任何内容都会被覆盖，因此在创建项目时请使用 "空白 "项目选项，并删除 Overleaf 中的`main.tex`文件。   
3. 找到项目的 Git URL（见[上文](https://www.overleaf.com/learn/how-to/Git_integration%23Cloning_your_project_as_a_local_repository)）。
4. 将项目的 Git URL 作为远程链接添加到本地项目中。
	`$ cd my-paper` 
	`$ git remote add overleaf <GIT-URL>`（请务必使用你自己的链接，而不是示例链接。）
5. 从 Overleaf 项目中提取最新内容，并将其合并到你的主分支中。
	`$ git checkout master`
	`$ git pull overleaf master --allow-unrelated-histories --rebase=false`
6. 将项目推送到 Overleaf $ git push overleaf master --set-upstream 如果想在本地使用不同的分支名称，比如`我的`分支，可以使用别名推送修改：
	`$ git push overleaf my-branch:master`
7. 访问 Overleaf 上的项目。你的修改应该就在那里。(你可能需要打开 Overleaf 项目菜单，查找哪个文件被设置为主文档文件，并在可能的情况下更改初始选择）。

### 全流程总结
1. 本地论文的文件夹里打开`git bash`并输入`git init`初始化；
2. 接着配置本地的用户名和邮箱：`git config --global user.email "kunli@sdu.edu.cn"` 和 ` git config --global user.name "likun"`
3. 然后添加git仓库链接：`git remote add overleaf https://git.overleaf.com/66235d8cacf3f13eca27714b`
4. 接着	`$ git checkout master`和`$ git pull overleaf master --allow-unrelated-histories --rebase=false`
5. 最后就可以`git push overleaf my-branch:master`了
6. 但是到第4步时，我就发现提示认证失败，经过一番尝试搜索，发现即便不用GitHub，只是git，也得付费一个月21美元，因此不继续搞了，还是老老实实写完上传吧，这篇记录就先放着，没准以后能用到。