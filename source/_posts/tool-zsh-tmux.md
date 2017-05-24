---
title: 工具流：使用zsh+oh-my-zsh+tmux构建你的命令行环境
date: 2017-01-06 22:31:26
tags: [Linux,shell]
categories: Linux没那么难
---
>注：我使用的环境是 Ubuntu16.04 + gnome-terminal

`zsh`与`bash`一样是`shell`环境，但是`zsh`比起`bash`拥有拼写纠正以及更多的扩展功能，
搭配`oh-my-zsh`可以使终端效率加倍
<!--more-->
## 安装`zsh`
```
sudo apt-get install zsh
```
## 安装`oh-my-zsh`
```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
## 设置Gnome-terminal使用zsh为默认的shell
打开`Gnome-terminal`，Edit -> Proile Prefrences -> Command -> Custom command: zsh

## tmux
`tmux` 是一个终端的窗口管理器，允许你在一个Linux终端窗口实例切分为多个窗口面板并管理
```
sudo apt-get install tmux
```
配置默认开启`tmux`的方式与上面配置zsh的方法一致，将命令改为`tmux`即可

## tmux 快捷键

- C-b ? 显示快捷键帮助
- C-b “ 横向分隔窗口
- C-b % 纵向分隔窗口
- C-b 0~9 选择几号窗口
- C-b o 跳到下一个分隔窗口
- C-b 上下键 上一个及下一个分隔窗口
- C-b C-o 调换窗口位置，类似与vim 里的C-w
- C-b 空格键 采用下一个内置布局
- C-b ! 把当前窗口变为新窗口
- C-b q 显示分隔窗口的编号
- C-b C-方向键 调整分隔窗口大小
- C-b c 创建新窗口
- C-b n 选择下一个窗口
- C-b p 选择前一个窗口
- C-b l 切换到最后使用的窗口
- C-b ; 切换到最后一个使用的面板
- C-b x 关闭面板
- C-b & 关闭窗口
- C-b d 退出tumx，并保存当前会话，这时，tmux仍在后台运行，可以通过tmux attach进入到指定的会话

## 番外：使用powerline-status
`powerline-status` 是一个插件，可以终端上显示一条导航栏
对于vim，导航栏上可以显示文件的信息和编辑器所处的模式等
对于tmux可以显示当前是否处于编辑模式，时间等（我对tmux还不是很熟悉）

### powerline-status 需要通过 pip 命令安装，先安装 python-pip 
```
sudo apt-get install python-pip
```

### 安装powerline-status
```
su -c 'pip install git+git://github.com/Lokaltog/powerline'
```

### powerline中自定义了一些符号，所以需要安装他的字体
```
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
sudo mv PowerlineSymbols.otf /usr/share/fonts/
sudo fc-cache -vf
sudo mv 10-powerline-symbols.conf /etc/fonts/conf.d/
```

### vim 配置
在`/etc/vim/vimrc`中添加如下配置
```
set rtp+=/usr/local/lib/python2.7/dist-packages/powerline/bindings/vim/

" Always show statusline
set laststatus=2

" Use 256 colours (Use this setting only if your terminal supports 256 colours)
set t_Co=256
```

### 配置zsh
在`/etc/zsh/zshrc`中添加如下配置
```
if [[ -r /usr/local/lib/python2.7/dist-packages/powerline/bindings/zsh/powerline.zsh ]]; then
    source /usr/local/lib/python2.7/dist-packages/powerline/bindings/zsh/powerline.zsh
fi
export TERM=xterm-256color
```

### 配置tmux
在`~/.tmux.conf`中添加如下配置
```
source /usr/local/lib/python2.7/dist-packages/powerline/bindings/tmux/powerline.conf
set-option -g default-terminal "screen-256color"
set-option -g default-shell /bin/zsh
```

## 番外的番外
写了一个[自动化脚本](https://gist.github.com/pcyan/acf05b05e210df3f56c63044fb8e0e52)，还没测试过，懒人但是**懂linux脚本**可以试一下

## 参考
[how-can-i-install-and-use-powerline-plugin](http://askubuntu.com/a/283909)
[Github * oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
