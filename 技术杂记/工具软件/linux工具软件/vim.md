## vim快捷键

### 正常模式

- yy 拷贝当前行
  - xyy 拷贝当前向下数共x行
- dd 删除当前行
  - xdd 删除当前向下数的x行
- p(paste) 粘贴
- u(undo) 撤销
- /要查找的内容 查找文本
  - n(next) 跳转到下一条查找结果
- G 跳转到末尾
- gg 跳转到开头

### 命令行模式

- set nu 添加行号
- set nonu 去除行号

### 可视模式



## vim tutor

version 1.7

### normal mode

#### elemental motion

- h,j,k,l  move the cursor
- q! quit the editor and discard any changes you have made
- q quit and not save
- wq means write and quit ,save your changes and then quit
- w: a word, w leads you to the starting of the next word 
- e: ending ,e leads you to the ending of a word 
- $: from cursor to the end of the line
- 0: move to the start of the line

#### text editing

- x :press x to delete the character under the cursor

- i : to insert text

- a : to append text

- d\<n>w: means delete n words begins at cursor

- d\<n>e: delete n words begins at cursor to the last character of final word

- dd: delete the whole ling where cursor stays

- d$: delete from cursor to end of the line

  ==it‘s neccesarry to stress that you can use delete to finish the cut motion in vim, cause when you delete some text, it was saved in the register of vim ,then you can use p to put the text in any where easily.==

  > Roses are red,
  >
  > Violets are blue,
  >
  > Sugar is sweet,
  >
  > And so are you.

- u: lowercase u, undo previous actions, it's possible to undo many steps

- U: capital U, undo all the changes on a line.

- ==CTRL + R==: undo the undo's

- p: put previously deleted text after the cursor.

- rx: replace the character at the cursor with x.

- ce: to change until the end of a word

  motice that ce deletes the word and places you in Insert mode

- cw,c$: both work in the same way as delete

#### cursor locating and file status

- CTRL + G : show your location in the file(the line number of the cursor's location),and the file status(including route of the file, if is modified, how many lines in total etc)
- G: move you to the bottom of the file 
- gg: move you to the start of the file 
- n + G: move you to the line which line number is n
- % ： place the cursor on any parenthese, then type %,it will find a matching parenthese,==this is very useful in debugging a program with unmatched parenthese==.
- 

### command line mode

==command line mode can totally think as normal mode however it only have to input a : first, and when command has done, it will turn to normal mode immediately.==

- type / followed by a phrase to search for the phrase forward

- type ? followed by a phrase to search for the phrase backward

- n : jump to next search result forward 

- N : jump to last search result backward

- substitute
  - s/old/new: substitute 'new' for 'old', but it only change the first occurrence of old ==in a line==
  - s/old/new/g : substitute 'new' for 'old', g means global, it will change suvstitute all 'old' ==in a line==
  - 11,121s/old/new/g : substitute all 'old' between line 11 and line 121
  - %s/old/new/g : substitute all 'old' in the whole file 
  - %s/old/new/gc : to find every occurence in the file and ask if you want to substitute it.
  
- execute an external command in vim

  - type `:!` followed by an external command to execute that command. such as `:！ls`，press enter and ls command will execute, press enter again and you will back to vim, there's promot too.

- ==save as==

  - :w filename: type this command will save the whole file to the 'filename' , ==it's useful when you want to save your changes as another file rather than the editing one==

    if you want to save a part of it rather than the whole file , enter the visual mode and select the text you want to save. enter `:` and verify that you see `:'<,'>` in the command line and then type the command `w filename`, the selected part will save in the 'filename'

- fetch and merging files

  - `r filename`: place the cursor where you want to insert text. then type then command. it will copy the 'filename' and paste at where cursor is. 

    ==you can also read the output of an external command, for example, `:r !ls` reads the output of the ls command and puts it below the cursor.


### visual mode

type `v` to enter the visual mode.move cursor to select the part you want to deal with , then just type the motion you want, such as `c,d,x,`

