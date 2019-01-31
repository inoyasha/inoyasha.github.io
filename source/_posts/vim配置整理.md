title: vim配置整理
tags:
  - vim
categories:
  - 编辑器
author: 郭小黑
date: 2019-01-24 09:41:00
---

#### 比较完整的vim配置整理

window的gvim和linux的vim插件均可以使用。在配置linux vim的时候，注意注释，如果配置不生效，删除配置即可。

<!-- more -->

##### 配置中文字符
```vim
"the chinese encoding
set encoding=utf-8
set fileencodings=utf-8,chinese,latin-1
if has("win32")
    set fileencoding=chinese
else
    set fileencoding=utf-8
endif
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim
language messages zh_CN.utf-8
set termencoding=gbk
```

#### 配置行号，配色方案等

```vim
set nu!            "设置行号
colorscheme desert     "设置配色方案
syntax on         "语法高亮
syntax enable        set nobackup        "不生成备份文件
set showmatch        "设置匹配模式
set smartindent        "设置只能对齐
set ai!            "设置自动缩进
set fileencodings=utf-8,gbk
set ambiwidth=double    "设置中文支持
set guifont=consolas:h12 "设置字体及大小
set mouse=a
```

#### 设置缩进等配置 

```vim
"F4 to toggle highlighting on/off
noremap <F4> :set hlsearch! hlsearch?<CR>
"F3 to toggle caseinsensetive
noremap <F3> :set ic! ic?<CR>

"设置缩进"
set tabstop=4 
set softtabstop=4 
set shiftwidth=4 
set noexpandtab 
set nu  
set autoindent 
set cindent

"设置粘贴模式"
set paste

"显示空格和tab键"
set listchars=tab:>-,trail:-

"总是显示状态栏"
set laststatus=2
"显示光标当前位置"
set ruler

set nocompatible               "去除VIM一致性，必须"
```

#### vundel插件管理配置

```vim
"设置包括vundle和初始化相关的运行时路径"
set rtp+=E:\Vim-Vundle\Vundle.vim
call vundle#begin()

"启用vundle管理插件，必须"
Plugin 'VundleVim/Vundle.vim'

"在此增加其他插件，安装的插件需要放在vundle#begin和vundle#end之间"
"安装github上的插件格式为 Plugin '用户名/插件仓库名'"

call vundle#end()              
filetype plugin indent on      "加载vim自带和插件相应的语法和文件类型相关脚本，必须"
```

