## 1 系统环境

​	不推荐计算机系的同学在Windows下安装Latex，实测编译速度比Linux环境慢很多。(如果一定要装在Windows里，直接在阿里云官方下载texlive镜像，傻瓜式安装即可)。以下所有配置方法皆以Ubuntu为例。只使用Windows又想体验Linux编译速度的同学可以安装WSL(Windows Subsystem for Linux)并在里面安装Latex，然后在使用时在Vscode里切换内核至WSL。(不建议C盘焦虑的同学安装，预估要占据10g存储空间)



## 2 texlive安装

```shell
$ sudo apt install texlive-full
```

建议科学上网，挂代理下载速度还是比较快的，或者更换apt源至国内镜像源(配置/etc/apt/sourses.list)，也可以直接硬下，但是可能会出现丢包和连接失败的情况，且用时较长。



## 3 配置Vscode

为了节省同学们的时间，将settings.json中需要增加的内容贴在这儿：

```json
"latex-workshop.latex.tools": [
        {
          "name": "latexmk",
          "command": "latexmk",
          "args": [
          "-synctex=1",
          "-interaction=nonstopmode",
          "-file-line-error",
          "-pdf",
          "-shell-escape",
          "%DOC%"
          ]
        },
        {
          "name": "xelatex",
          "command": "xelatex",
          "args": [
          "-synctex=1",
          "-interaction=nonstopmode",
          "-file-line-error",
          "-shell-escape",
          "%DOC%"
            ]
        },          
        {
          "name": "pdflatex",
          "command": "pdflatex",
          "args": [
          "-synctex=1",
          "-interaction=nonstopmode",
          "-file-line-error",
          "%DOC%"
          ]
        },
        {
          "name": "bibtex",
          "command": "bibtex",
          "args": [
            "-shell-escape",
          "%DOCFILE%"
          
          ]
        }
      ],
  "latex-workshop.latex.recipes": [
        {
          "name": "xelatex",
          "tools": [
          "xelatex"
                      ]
                },
        {
          "name": "latexmk",
          "tools": [
          "latexmk"
                      ]
        },

        {
          "name": "pdflatex -> bibtex -> pdflatex*2",
          "tools": [
          "pdflatex",
          "bibtex",
          "pdflatex",
          "pdflatex"
                      ]
        }
      ],
  "latex-workshop.view.pdf.viewer": "tab",  
  "latex-workshop.latex.clean.fileTypes": [
      "*.aux",
      "*.bbl",
      "*.blg",
      "*.idx",
      "*.ind",
      "*.lof",
      "*.lot",
      "*.out",
      "*.toc",
      "*.acn",
      "*.acr",
      "*.alg",
      "*.glg",
      "*.glo",
      "*.gls",
      "*.ist",
      "*.fls",
      "*.log",
      "*.fdb_latexmk"
    ],
    "latex-workshop.message.error.show": false,
    "latex-workshop.message.warning.show": false,
    "latex.linter.enabled": false,
    "editor.formatOnSave": true,
```

将默认配置Xelatex为编译器。

## 4 依赖和字体

"操作系统实验报告模板"中用到了minted包进行代码排版展示，其需要的包为pygments，在确保自己主环境python和pip一致的情况下(有同学曾添加环境变量，将pip的默认值改到了conda的python中......)：

```shell
$ pip install pygments
```

安装依赖后就不会报错提示minted缺失了

至于字体问题，一开始没有发现，在某贇杰同学的提示下发现其名字中的贇无法显示，这是由于字体包中本身缺少一些生僻字，解决方法是给系统安装字体：

```shell
#找到fonts.zip，解压：
$ unzip fonts.zip
#进入解压后的文件夹，有三个ttf字体文件
$ cd fonts
#在系统字体文件夹下新建文件夹chn,将字体文件复制进去
$ sudo mkdir /usr/share/fonts/chn
$ cp * /usr/share/fonts/chn
#在该文件夹下配置，建立字体索引信息，更新字体缓存
$ cd /usr/share/fonts/chn

$ mkfontscale

$ mkfontdir

$ fc-cache
#至此，字体安装完毕！可以用以下命令检查
$ fc-list:lang=zh-cn
```

然后在模板中就可以设置

```latex
\usepackage{xeCJK}
\setCJKmainfont[BoldFont={FZHei-B01},ItalicFont={FZKai-Z03}]{FZShuSong-Z01}
```

就可以使用字体，生僻字被补全。

注：

怕麻烦的同学可以不装字体包，不使用以上两个包，而改用：

```latex
\usepackage[UTF8]{ctex}
```

但是属实不建议怕麻烦的同学用Latex，中文环境确实比较恶劣