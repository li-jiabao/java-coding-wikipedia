---
title: VIM-autocmd命令
author: lijiabao
e-mail: lijiabao@smail.nju.edu.cn
date: 2021-03-08-23:57:14
categories: vim使用
tags: vim使用
description: 介绍vim编辑器中autocmd的自动命令设置和常见使用方法
---


## autocmd

### 简要介绍
当你想要在你读取或者写入文件时、进入或者离开一个buffer或者窗口时、再或者离开vim时，你可以指定在执行这些操作之后自动执行的命令。

比如当你想要为匹配某一文件类型的文件打开某一特定的vim选项时：对于匹配`*.c`的C文件时，创建一个自动命令设置`cindent`选项。同样你还可以应用高级特征，如编辑压缩文件。

通常你的autocommands放在vim配置文件中`.vimrc`或者`.exrc`文件中。

<font color='red'>
	注意：尽管autocommands很有用，但可能会导致意料之外的副作用，应留心不要毁坏你的文本.
	可能的话，尽量为`File*`和`Buf*`事件提供一样的autocmd命令
</font>

### 如何定义自动命令

`:au[tocmd] [group] {event} {pat} [++once] [--nedted] {cmd}`

上述命令的解释：
> 添加`{cmd}`到自动命令中，当发生事件`{event}`时，为匹配autocmd-pattern`{pat}`的文件执行这个自动命令


> `:au[tocmd]`是用来开启绑定自动命令的，au是缩写，autocmd是全称，`{cmd}`处注释也会被当成命令的参数.此处不仅支持外部命令，也支持脚本文件中定义的函数和绑定的快捷键。


> `[group]`没有给定时，vim会使用`:augroup`定义的当前group，否则，会使用`[group]`定义的组。group必须预先定义好存在。不可以使用`:au group ...`定义组，需要使用`:augroup `，当测试自动命令时，可以开启`verbose`参数：`:set verbose=9`这个选项可以在自动命令执行时显示它们。


`:autocmd`不管是否已经存在都会将这个自动命令添加进去,因此,若是你的命令出现两次,他就会出现两次。为了避免这个情况，可以将你的autocommands定义到组中，之后也可以很方便的清理：
```vimscript
augroup vimrc
	"将所有的自动命令在后面定义
	autocmd!
	au BufNewFile,BufRead *.html so <sfile>:h/html.vim
augroup END

```
如果不想使用group，也可以做一次判断来确保命令只出现一次：
```vimscript
if !exists("autocommands_loaded")
	let autocommands_loaded = 1
	au ...
endif
```


<font color="yellow">注意：</font>
在自动命令中，特殊符号不会被扩展，这些只会在事件响应且cmd执行后，才会扩展，只有`<sfile>`会被扩展。


### 删除自动命令

去除所有和事件和pat相联系的自动命令:
`:au[tocmd]! [group] {event} {pat} [++once] [++nested] {cmd}`
去除所有和事件和pat相联系的自动命令：
`:au[tocmd]!{event} {pat}`
去除和pat相联系的所有事件的自动命令：
`:au[tocmd]! {pat}`
删除所有自动命令：
`:au[tocmd] [group]`
但是<font color="yellow">注意：</font>当你的脚本不存在group时不要使用，会破坏插件和语法高亮。当group未指定时，和前面创建命令时一样的默认操作。



### Event事件

指定事件的时候，你可以中通过英文逗号来分割事件，不可以使用空格。自动命令中的command应用于这个列表中的所有事件。

对于读取文件，有四个可选事件：
- `BufNewFile`开始编辑一个不存在的文件
- `BufReadPre`和`BufReadPost`开始编辑一个已经存在的文件
- `FileterReadPre`和`FilterReadPost`使用过滤器输出读取临时文件
- `FileReadPre`和`FileReadPost`任何其他文件的读取
当读取文件时,vim只使用这四种的其中一个,pre和post分别对应读取文件前后

<font color="yellow">注意：</font> 所有的和`*ReadPre`和Filter事件绑定的自动命令，不允许改变当前buffer。

当vim识别事件的时候，它是忽略大小写的，可以识别`bufread`或者`BUFread`

你还可以设定`eventignore`选项来忽略一些事件或者所有的事件。

#### 所有的事件列表：

**Reading**

| 事件 | 事件描述 | 
| :---: | :---: | 
|BufNewFile|		starting to edit a file that doesn't exist
|BufReadPre|		starting to edit a new buffer, before reading the file
|BufRead|		starting to edit a new buffer, after reading the file
|BufReadPost|		starting to edit a new buffer, after reading the file
|BufReadCmd|		before starting to edit a new buffer |Cmd-event|
|FileReadPre|		before reading a file with a ":read" command
|FileReadPost|		after reading a file with a ":read" command
|FileReadCmd|		before reading a file with a ":read" command |Cmd-event|
|FilterReadPre|		before reading a file from a filter command
|FilterReadPost|	after reading a file from a filter command
|StdinReadPre|		before reading from stdin into the buffer
|StdinReadPost|		After reading from the stdin into the buffer

**Writing**

| 事件 | 事件描述 | 
| :---: | :---: | 
|BufWrite|		starting to write the whole buffer to a file
|BufWritePre|		starting to write the whole buffer to a file
|BufWritePost|		after writing the whole buffer to a file
|BufWriteCmd|		before writing the whole buffer to a file |Cmd-event|
|FileWritePre|		starting to write part of a buffer to a file
|FileWritePost|		after writing part of a buffer to a file
|FileWriteCmd|		before writing part of a buffer to a file |Cmd-event|
|FileAppendPre|		starting to append to a file
|FileAppendPost|	after appending to a file
|FileAppendCmd|		before appending to a file |Cmd-event|
|FilterWritePre|	starting to write a file for a filter command or diff
|FilterWritePost|	after writing a file for a filter command or diff

**Buffers**

| 事件 | 事件描述 | 
| :---: | :---: | 
| BufAdd |		just after adding a buffer to the buffer list
|BufCreate|		just after adding a buffer to the buffer list
|BufDelete|		before deleting a buffer from the buffer list
|BufWipeout|		before completely deleting a buffer
|BufFilePre|		before changing the name of the current buffer
|BufFilePost|		after changing the name of the current buffer
|BufEnter|		after entering a buffer
|BufLeave|		before leaving to another buffer
|BufWinEnter|		after a buffer is displayed in a window
|BufWinLeave|		before a buffer is removed from a window
|BufUnload|		before unloading a buffer
|BufHidden|		just before a buffer becomes hidden
|BufNew|		just after creating a new buffer
|SwapExists|		detected an existing swap file

**Options**

| 事件 | 事件描述 | 
| :---: | :---: | 
|FileType|		when the 'filetype' option has been set
|Syntax|		when the 'syntax' option has been set
|EncodingChanged|	after the 'encoding' option has been changed
|TermChanged|		after the value of 'term' has changed
|OptionSet|		after setting any option

**Startup and exit**

| 事件 | 事件描述 | 
| :---: | :---: | 
|VimEnter|		after doing all the startup stuff
|GUIEnter|		after starting the GUI successfully
|GUIFailed|		after starting the GUI failed
|TermResponse|		after the terminal response to |t_RV| is received
|QuitPre|		when using `:quit`, before deciding whether to exit
|ExitPre|		when using a command that may make Vim exit
|VimLeavePre|		before exiting Vim, before writing the viminfo file
|VimLeave|		before exiting Vim, after writing the viminfo file
|VimSuspend|		when suspending Vim
|VimResume|		when Vim is resumed after being suspended

**Terminal**

| 事件 | 事件描述 | 
| :---: | :---: | 
|TerminalOpen|		after a terminal buffer was created
|TerminalWinOpen|	after a terminal buffer was created in a new window

**Various**

| 事件 | 事件描述 | 
| :---: | :---: | 
|FileChangedShell|	Vim notices that a file changed since editing started
|FileChangedShellPost|	After handling a file changed since editing started
|FileChangedRO|		before making the first change to a read-only file
|DiffUpdated|		after diffs have been updated
|DirChanged|		after the working directory has changed
|ShellCmdPost|		after executing a shell command
|ShellFilterPost|	after filtering with a shell command
|CmdUndefined|		a user command is used but it isn't defined
|FuncUndefined|		a user function is used but it isn't defined
|SpellFileMissing|	a spell file is used but it can't be found
|SourcePre|		before sourcing a Vim script
|SourcePost|		after sourcing a Vim script
|SourceCmd|		before sourcing a Vim script |Cmd-event|
|VimResized|		after the Vim window size changed
|FocusGained|		Vim got input focus
|FocusLost|		Vim lost input focus
|CursorHold|		the user doesn't press a key for a while
|CursorHoldI|		the user doesn't press a key for a while in Insert mode
|CursorMoved|		the cursor was moved in Normal mode
|CursorMovedI|		the cursor was moved in Insert mode
|WinNew|		after creating a new window
|TabNew|		after creating a new tab page
|TabClosed|		after closing a tab page
|WinEnter|		after entering another window
|WinLeave|		before leaving a window
|TabEnter|		after entering another tab page
|TabLeave|		before leaving a tab page
|CmdwinEnter|		after entering the command-line window
|CmdwinLeave|		before leaving the command-line window
|CmdlineChanged|	after a change was made to the command-line text
|CmdlineEnter|		after the cursor moves to the command line
|CmdlineLeave|		before the cursor leaves the command line
|InsertEnter|		starting Insert mode
|InsertChange|		when typing `<Insert>` while in Insert or Replace mode
|InsertLeave|		when leaving Insert mode
|InsertCharPre|		when a character was typed in Insert mode, before inserting it
|TextChanged|		after a change was made to the text in Normal mode
|TextChangedI|		after a change was made to the text in Insert mode when popup menu is not visible
|TextChangedP|		after a change was made to the text in Insert mode when popup menu visible
|TextYankPost|		after text has been yanked or deleted
|SafeState|		nothing pending, going to wait for the user to type a character
|SafeStateAgain|	repeated SafeState
|ColorSchemePre|	before loading a color scheme
|ColorScheme|		after loading a color scheme
|RemoteReply|		a reply from a server Vim was received
|QuickFixCmdPre|	before a quickfix command is run
|QuickFixCmdPost|	after a quickfix command is run
|SessionLoadPost|	after loading a session file
|MenuPopup|		just before showing the popup menu
|CompleteChanged|	after Insert mode completion menu changed
|CompleteDonePre|	after Insert mode completion is done, before clearing info
|CompleteDone|		after Insert mode completion is done, after clearing info
|User|			to be used in combination with ":doautocmd"
|SigUSR1|		after the SIGUSR1 signal has been detected


### Patterns匹配模式

这个匹配的参数也可以使用英文逗号隔开，这就相当于对于每个给定的模式匹配，都会执行自动命令
模式匹配在匹配文件时，如果没有`/`符号，只匹配文件名，如果有该符号，既匹配文件名，也匹配全路径名。

示例：
- `:au BufRead *.txt,*.md   set et`
- `:au BufRead /vim/src/*.c set cindent`
- `:au BufRead */tmp/*.c`
- `:au BufRead $ROOTDIR/main.$EXT`匹配的是扩展后的情况，支持扩展，同时也支持正则表达式的通配符

### Buffer-local autocmd

是被附加到指定buffer的命令
格式如下：
- `<buffer>`指当前buffer
- `<buffer=99>`指标号99的buffer
- `<buffer=abuf>`使用`<abuf>`(只有当执行自动命令时)
示例：
- `:au CursorHold <buffer> echo 'hold'`
- `:au CursorHold <buffer=33> echo hold`
- `:au BufNewFile * au CursorHold <buffer=abuf> echo 'hold'`

### Groups组

autocmd可以放在一个group中，这对于之后移除或者执行一组autocmd很方便。如所有放在`highlight`组中的语法高亮autocmd，可以通过命令：`:doautoall highlight BufRead`在gui启动的时候执行这一组autocmd。

定义组：`:aug[roup] {name}`
删除组：`:aug[roup] {name}`
没有指定组选择的时候，使用默认组，默认组没有名字，不能从默认组单独执行命令。

定义特定组的autocommands，使用下面步骤：
1. 选择设置组名，`:augroup {name}`
2. 删除任何旧的zutocommands，`:au!`
3. 定义autocommands
4. 使用`augroup END`回到默认组

示例：
```vimscript
:augroup uncompress
:	au!
:	au BufEnter *.gz   %!gunzip
:	augroup END
```


### 执行autocmd命令

vim同样可以非自动的执行自动命令，当你执行了错误的自动命令或者已经改变了一个命令的时候，这个会比较有用

注意：`evnentignore`选项对这个也是适用的，不会执行列在这个选项中的自动命令

两种执行autocmd的方法：

#### `:doautocmd`

应用匹配fname文件和evnet事件的自动命令到当前的buffer上。
`:do[autocmd] [<nomodeline>] [group] {event} [fname]`
这个命令可以简化成`do` `doau` `doaut` `doautocmd`，这个执行autocmd命令也可应用到autocmd中。如：
```vim
:au BufEnter *.cpp so ~/.vimrc_cpp
:au BufEnter *.cpp doau BufEnter x.c

" 但是这样的话需要注意不要发生无线循环才好，详情可以参考autocmd-nested"
" vim配置文件中，:可以省略"
```
- `nomodeline`：当使用autocmd时，modelines被处理以让他们的设置可以覆盖autocmd的设置。而使用了nomodeline选项，就会忽略这个操作。当没有autocmd匹配的时候，也会跳过处理modeline
- `group`：当没有指定group时，使用所有group的autocmd，当指定了group，就只会运用这个组的autocmd，如果使用了未定义的group，会报错。
- `fname`：默认时当前的文件名

#### `doautoall`

和doau类似，但是为所有加载的buffer执行autocommand，注意fname是用来选择使用的autocmd的，而不是autocmd应用的buffer。
`:doautoa[ll] [<nomodeline>] [group] {event} [fname]`

<font color="yellow">注意：</font>不要在以下情况使用这个autocmd执行：
- 删除buffer
- 改变到另外一个buffer
- 改变一个buffer的内容
*以上操作可能会造成不可预料的副作用*


### 禁用autocmd

可以使用参数`eventignore`来禁用

只禁用一个autocmd可以使用`:noautocmd`命令或者`:noa`：
`:noautocmd w fname.gz`

## autocmd示例

读取和写入压缩文件的autocmd例子：
```vim
  :augroup gzip
  :  autocmd!
  :  autocmd BufReadPre,FileReadPre	*.gz set bin
  :  autocmd BufReadPost,FileReadPost	*.gz '[,']!gunzip
  :  autocmd BufReadPost,FileReadPost	*.gz set nobin
  :  autocmd BufReadPost,FileReadPost	*.gz execute ":doautocmd BufReadPost " . expand("%:r")
  :  autocmd BufWritePost,FileWritePost	*.gz !mv <afile> <afile>:r
  :  autocmd BufWritePost,FileWritePost	*.gz !gzip <afile>:r

  :  autocmd FileAppendPre		*.gz !gunzip <afile>
  :  autocmd FileAppendPre		*.gz !mv <afile>:r <afile>
  :  autocmd FileAppendPost		*.gz !mv <afile> <afile>:r
  :  autocmd FileAppendPost		*.gz !gzip <afile>:r
  :augroup END

```

`autocmd!`用来在组中删除任何已经存在的autocommands
`<afile>:r` 是没有扩展的文件名

一些多pattern匹配的示例：
```vim
:autocmd BufRead   *		set tw=79 nocin ic infercase fo=2croq
:autocmd BufRead   .letter	set tw=72 fo=2tcrq
:autocmd BufEnter  .letter	set dict=/usr/lib/dict/words
:autocmd BufLeave  .letter	set dict=
:autocmd BufRead,BufNewFile   *.c,*.h	set tw=0 cin noic
:autocmd BufEnter  *.c,*.h	abbr FOR for (i = 0; i < 3; ++i)<CR>{<CR>}<Esc>O
:autocmd BufLeave  *.c,*.h	unabbr FOR
```

读取模板文件添加到新文件中：
```
" To read a skeleton (template) file when opening a new file: 

:autocmd BufNewFile  *.c	0r ~/vim/skeleton.c
:autocmd BufNewFile  *.h	0r ~/vim/skeleton.h
:autocmd BufNewFile  *.java	0r ~/vim/skeleton.java
```

自动插入时间到html文件中：
```vim
autocmd BufWritePre,FileWritePre *.html   ks|call LastMod()|'s
fun LastMod()
  if line("$") > 20
    let l = 20
  else
    let l = line("$")
  endif
  exe "1," . l . "g/Last modified: /s/Last modified: .*/Last modified: " .
  \ strftime("%Y %b %d")
endfun
```
解释：
- `ks`		标记当前位置为`'s`
- `call LastMod()`   调用函数来实现插入时间的工作
- `'s`		返回cursor光标到原来的位置


对于makefiles文件(makefile, Makefile, imakefile, makefile.unix, etc.): 
```vim
:autocmd BufEnter  ?akefile*	set include=^s\=include
:autocmd BufLeave  ?akefile*	set include&
```


总是在C文件的第一个函数中编辑文件：
`:autocmd BufRead   *.c,*.h	1;/^{`
没有`1;`，搜索将从进入文件时所在的位置开始而不是从文件的第一行开始

