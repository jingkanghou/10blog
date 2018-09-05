tag:go,vscode  

# Go语言开发环境配置(VSCode)

配置Go语言时遇到一些坑，特别是VSCode中Go工具**包的安装**及相关的**科学上网**知识，查了很多资料都描述不清晰，可能是我认为是难点的内容在别人那里是不需要说明的基础吧。

其实只要科学上网配置完成，VSCode中安装工具包超级简单，对，是超级简单。好像本文标题不对？应该叫“Go语言工具包安装”？好吧，我在这里是把Go的环境变量等配置当作不需要说明的基础了，那些东东网上很多，我在本文略过了:)

#### 安装go  
略  
#### 安装git  
略  
#### VS Code配置  
- 1.安装go扩展  
  在vscode扩展中搜“go”,选择microsoft官方的ms-vscode.go  
- 2.安装相关工具包  
  - 通过VSCode安装  
    操作很简单，但需要科学上网（见下面第4条）  
    - 1)Ctrl + Shift + P  
    - 2)输入`Go: install/update tools`  
    - 3)All Select,确定  
    - 4)部分工具需要科学上网。注意为VSCode设置[用户设置]http代理:  
      此处以SSR为例，请先安装并完成SSR客户端配置  
      - 4.1) windows下先启动SSR客户端，再对VSCode设置：  
      ``` json  
      "http.proxy": "127.0.0.1:1080",  
      ```  
      - 4.2) linux下需要[使用privoxy](https://www.cnblogs.com/liuxuzzz/p/5324749.html)一类的工具，安装配置好privoxy之后，再设置VSCode  
      ``` json  
      "http.proxy": "http://localhost:8118/",  
      ```  
      
      ``` javascript  
      if (a == b) {
			   var c = "21";
			}
      ```  
			
      ``` go  
      if a == b {
			   var c = "21";
			}
      ```  
      注：因为要跑本地服务，因此需要去掉Privoxy/config中`# forward           localhost/     .`注释  
      ``` shell  
      forward           localhost/     .  
      ```  
  - 手动安装  
    - 使用go get，例如：    
      ``` shell  
      go get -u github.com/gin-gonic/gin  
      ```        
      部分包需要科学上网，可参考前面科学上网说明  
    - 不想翻墙的安装方法  
      ``` shell  
      # 注意，github下golang/镜像中的包需要放到golang.org/x目录下  
      git clone https://github.com/golang/net.git $GOPATH/src/golang.org/x/net  
      git clone https://github.com/golang/sys.git $GOPATH/src/golang.org/x/sys  
      git clone https://github.com/golang/tools.git $GOPATH/src/golang.org/x/tools  
      ```  

- 3.配置  
  - 设置toolsGopath(原因参考[vscode golang详细配置](https://blog.csdn.net/ys5773477/article/details/78881841)中的【**注：坑点**】)  
在VSCode[用户设置]中添加：  
    ``` json  
    "go.toolsGopath": "F:\\SourceCode\\go",  
    ```  
  - debug配置  
    使用默认配置  
    debug配置的更多信息，参考官方Wiki： [Debugging Go code using VS Code](https://github.com/Microsoft/vscode-go/wiki/Debugging-Go-code-using-VS-Code)  
