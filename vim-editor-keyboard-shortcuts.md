---
layout: post
title: Vim editor keyboard shortcuts
---

#### File commands

|Keys| Description|
|:---|:---|
|`i` | insert mode before character  
|`a` | append mode after character  
|`:q` | quit file  
|`:q!` | quit file and ignore saving  
|`:w`| save file
|`:wq` | save file and quit
|`:x` | save file and quit
|`/{string}`| the first occurrence of this string in the file is highlighted
|`:set number`| shows line numbers in the file
|`:set nonumber`| to stop showing the line numbers in the file
|`v` + `select text using arrow keys`| visually select text
|`d`| cut the text selection
|`y`| copy the text selection
|`p`| paste the text selection
|`/<text>`| selects and projects the first occurrence of this text in the file
|`:%s/foo/bar/g`| Find each occurrence of 'foo' (**in all lines**), and replace it with 'bar'.
|`:s/foo/bar/g`| Find each occurrence of 'foo' (in the **current line** only), and replace it with 'bar'.
|`:%s/foo/bar/gc`| Change each 'foo' to 'bar', but **ask for confirmation** first.
|`:%s/\<foo\>/bar/gc`| Change only whole words **exactly matching** 'foo' to 'bar'; ask for confirmation.
|`:%s/foo/bar/gci`| i => case **insensitive**
|`:%s/foo/bar/gcI`| I => case **sensitive**


#### Navigation commands

|Keys| Description|
|:---|:---|
|`1G`| go to line no. 1
|`5G`| go to line no. 5
|`G`| go to the last line
|`^`| go to the beginning of the line
|`$`| go to the end of line
|`dw`| delete the word
|`dd`| delete the entire line
|`dG`| delete all contents of the file
|`x`| delete the current character
|`u`| undo the last change
|`w`| move forward word by word
|`b`| move backward word by word

