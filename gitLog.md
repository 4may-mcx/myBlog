### 1. 常用 linux 指令

##### 1.1 文件操作

1. cd ：改变目录
2. cd .. ：回退到上一个目录
3. pwd ： 显示当前目录路径
4. ls ：列出档期啊目录中的所有文件
5. touch ：创建新文件
6. rm ：remove 删除文件
7. mkdir ：新建一个文件夹，即新建目录
8. rm -r ：删除一个文件夹，`rm -r src` 删除src目录
9. mv ：移动文件 `mv common.js src` common.js 是要移动的文件，src是目标文件夹



##### 1.2 终端操作

1. reset ：重置终端
2. clear ：清屏
3. history ：历史命令
4. exit ：退出

 

### 2. Git配置文件地址

C:\Git\etc\gitconfig

C:\用户\adminstor\\.gitconfig

### 3. Git基本理论

![Image](https://raw.githubusercontent.com/4may-mcx/myBlog/master/images/gitLog_1.png))

##### Git本地有三个工作区域

- 工作目录（Working Directory）
- 暂存区（Stage / Index）
- 资源库（Respository / Git Directory）

**working directory**：工作区，平时写代码的地方

**index / stage**（.git）：暂存区，只是一个文件，保存即将提交到文件列表的信息

**respository**（.git）：本地仓库，安全存放数据的位置，有提交到所有版本的数据。其中 HEAD 指向最新放入仓库的版本

**remote directory**：远程仓库，托管代码的服务器 如 github、gitee



#####  文件的四种状态

- **untracked**：在文件夹中，但不在 git 库里，不参与版本控制。通过 `git add` 使其状态变为 `staged`

- **unmodify**：

  文件已入库，未修改。若被修改，则变为 `modified` 状态。若使用 `git rm filename` 移除版本库，则成为 `untracked` 文件。

- **modified**：

  文件已修改，仅仅是修改。通过 `git add` 进入 `staged` 状态。

  可使用 `git checkout` 则丢弃修改，返回到 `unmodify` 状态。即从库中取出文件，覆盖当前修改。

- **staged**：

  暂存状态。通过 `git commit 注释内容` 将修改同步到库中，这时文件和本地文件又变为一致，文件变为 `unmodify` 状态。可执行 `git reset HEAD filename` 取消暂存，使其状态为 `modified`。



### 4.操作流程

1. ①本地仓库搭建 `git init` ，操作后目录仅出现一个 .git 文件 

   ②克隆远程仓库 `git clone https://github.com/4may-mcx/firstTest.git`，将远程服务器上的仓库完全镜像一份到本地

2. 文件操作 

```bash
#查看所有文件的状态
git status 	
#查看指定文件的状态
git statuss filename 

#添加所有文件到暂存区
git add . 			

#提交到本地仓库  -m 提交信息
git commit -m '注释内容' 	
```

![Image](https://raw.githubusercontent.com/4may-mcx/myBlog/master/images/gitLog_process.png)

##### 忽略文件

配置在 `.gitignore` 文件中

1. （!）表示例外规则，不会被忽略

2. （/）在名称的最前面，表示要忽略的文件在此目录下，而子目录中的文件不被忽略

   ​		 在名称的最后面，表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）

```bash
*.txt		#忽略所有 .txt 结尾的文件
!lib.txt	#忽略除了 lib.txt 之外的文件
/temp		#仅忽略项目根目录下的TODO文件，不包括其他目录temp 
buid/		#忽略 build/ 目录下的所有文件
doc/*.txt	#会忽略 doc/notes.txt 但不忽略 doc/serve/arch.txt (浅忽略)
```



 ### 5. Git 分支

分支相当于平行的两条线，互不干扰，即同时存在多个版本。如果需要合并，那就需要处理问题了。



```bash
#分支常用指令

#列出所有本地分支
git branch

#列出所有远程分支
git branck -r

#新建一个分支，但依旧停留在当前分支
git branch [branchName]

#切换分支
git checkout [branchName]

#新建一个分支，并切换到该分支
git checkout -b [branchName]

#合并指定分支到当前分支
git merge [branchName]

#删除分支
git branch -d [barnchNeme]

#删除远程分支
git push origin --delete [branchName]
git branch -dr [remote/branch]
```

