## 设置Git用户名和邮箱

Pop_OS预装了git，不用安装，直接使用`git config`命令配置即可。参考git官方文档：
[初次运行 Git 前的配置](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%88%9D%E6%AC%A1%E8%BF%90%E8%A1%8C-Git-%E5%89%8D%E7%9A%84%E9%85%8D%E7%BD%AE)
使用命令
``` bash
$ git config --global user.name "YOUR_NAME"
$ git config --global user.email YOUR_EMAIL@EXAMPLE.com
```
详解以上命令：
### `--global`

`--global`的含义是，对当前[用户]()的所有仓库生效，在Pop_OS系统上，默认`.gitconfig`文件路径为`~/.gitconfig`文件。查看一下执行以上配置后的结果：
``` bash
$ cat ~/.gitconfig
[user]
	name = YOUR_NAME
	email = YOUR_EMAIL@EXAMPLE.com
```
也可换为其他参数来指定此配置的作用范围，详见git官方文档。

### `user.name`

你的用户名，按个人喜好制定

### `user.email`
在浏览器登陆github账户并访问[Github-Email Settings](https://github.com/settings/emails)，查看本人账户用于进行github操作的邮箱。
> 注意：如果开启了**Keep my email addresses private**选项，需要使用github生成的以`@users.noreply.github.com`结尾的邮箱。
> ![[Pasted image 20231202154000.png]]





## 使用gpg配置Github提交签名认证(commit signature authentication)

在github进行commit需要配置commit signature verification，可以使用gpg, SSH, S/SIME等方式。我们采用gpg。参考github官方文档：
[GPG commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification#gpg-commit-signature-verification)
执行以下步骤：
### 1. 生成gpg密钥

参照[Generating a GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key#generating-a-gpg-key)执行即可。同样注意如果开启了**Keep my email addresses private**选项，需要使用github生成的以`@users.noreply.github.com`结尾的邮箱。


