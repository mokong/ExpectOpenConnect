
## 背景

公司文档和接口需要使用VPN访问，而这个VPN软件每次打开都要输入密码，而且还需要动态码，太麻烦了，所以就想找一个简便的方法，最好是不需要设置，一开始想的是使用Surge配置，但是失败了。所以最后的成果就是命令行输入一个命令一键连接。


<!--more-->

这个命令中，包含了密码、以及获取动态码和发起链接的过程，所以命令行输入后，什么也不需要操作了，下面来看看具体实现。


## 实现

### 首先安装expect

通过下面的命令安装expect，最终的命令是.expect的。Ps: Expect 本身不是一种文件格式，而是一种用于自动化交互的脚本语言和工具。它是一个Tcl (Tool Command Language) 脚本语言的扩展，主要用于自动化那些需要用户进行手动输入的交互式程序，例如SSH、FTP、passwd 等。Expect 脚本通过模拟人工操作，让程序在后台执行并自动处理响应，从而实现自动化。——来自Google，哈哈哈。

`brew install expect`


### 然后安装openconnect

因为公司的VPN连接用的是OpenConnect，所以通过命令行安装openConnect，命令如下：

`brew install openconnect`

安装完成后，就可以试一下通过命令行连接了，输入：

```
sudo openconnect '--protocol=anyconnect' YourVPNServer '--user=YourUserName'
```

如下图所示：

![image.png](https://raw.githubusercontent.com/mokong/BlogImages/main/img/20250928143329.png)

需要注意的是：第一个Password是sudo，即电脑的管理员密码；第二个Password是openconnect连接的密码，也就是在VPN客户端输入的密码，比如对于我来说就是固定密码+动态码。

出现上面的字段就是连接成功了，上面的--servercert那里需要着重关注下，在expect脚本中需要用到。

### 再然后就是自动获取动态码

对于我来说，动态码是一个二维码，每次是从二维码中看到最新的6位数字，但是通过命令行需要通过TOTP_SECRET获取到TOTP_CODE，所以首先需要获取到TOTP_SECRET，步骤如下：

Chrome安装`Authenticator Extension`，[Authenticator - Chrome Web Store](https://chromewebstore.google.com/detail/authenticator/bhghoamapcdpbohphigoooaddinpkbai)，添加二维码之后，点击左上角的设置，再选择Backup，然后选择Download Backup File，然后就会发现本地下载了一份authenticator.txt，打开其中secret=的部分就是TOTP_SECRET。

![image.png](https://raw.githubusercontent.com/mokong/BlogImages/main/img/20250928151931.png)

![image.png](https://raw.githubusercontent.com/mokong/BlogImages/main/img/20250928151959.png)

### 最后，编辑脚本

脚本内容如下，地址在：[GitHub - mokong/ExpectOpenConnect: openConnect 脚本](https://github.com/mokong/ExpectOpenConnect)

![image.png](https://raw.githubusercontent.com/mokong/BlogImages/main/img/20250928152442.png)

编辑完成后，直接在命令行输入类似于下面的命令，即可一键连接VPN。电脑上的open Connect客户端就可以删除了。

`expect openConnectVPN.expect`

