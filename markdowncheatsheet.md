---
layout: post
title: Markdown Cheatsheet
---

This is a list of frequently used markdown commands.  
I'll be updating this section on an ongoing basis.  
I use the [GitHub flavored markdown](https://help.github.com/articles/markdown-basics/) and the [StackOverflow markdown](http://stackoverflow.com/editing-help) for reference.


#### Headers

\# H1  
\#\# H2  
\#\#\# H3  
\#\#\#\# H4  
\#\#\#\#\# H5  
\#\#\#\#\#\# H6  

# H1  
  
## H2  
  
### H3  
  
#### H4  
  
##### H5  
  
###### H6  
  

***

###### Emphasis/Italics
`*asterisks* or _underscore_`  
 *asterisks* or _underscore_

###### Strong Emphasis/Bold
 `**asterisks** or __underscore__`  
 **asterisks** or __underscore__

###### Combined Emphasis
 `**asterisks and _underscores_**`  
 **asterisks and _underscores_**

###### Strikethrough
`~~Scratch this.~~`  
 <strike>Scratch this.</strike>

***

###### Line Breaks
1 newline => new line in the same paragraph  
2 newlines => new paragraph altogether

***

###### Horizontal Rule
Three or more   
- Hyphens  
   `---`  
- Asterisks  
   `***`  
- Underscores  
   `___`  

 ***  

###### BlockQuote
`> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote.`  
> This is a very long line that will still be quoted properly when it wraps. Oh boy let's keep writing to make sure this is long enough to actually wrap for everyone. Oh, you can *put* **Markdown** into a blockquote.   

***

###### Tables
Colons can be used to align columns.  

<pre> 
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 | 
</pre>
There must be at least 3 dashes separating each header cell.  

![Table1](http://sundeepkamath.in/wp-content/uploads/2015/08/Table1.png)
 
You can also use inline Markdown.  
The outer pipes (|) are optional, and you don't need to make the raw Markdown line up prettily. 
<pre>
Markdown | Less | Pretty
--- | --- | ---
*Still* | `renders` | **nicely**
1 | 2 | 3
</pre>

![Table2](http://sundeepkamath.in/wp-content/uploads/2015/08/Table2.png)

***

###### Code and Syntax Highlighting
**Inline Code blocks**  
<pre>
Inline `code` has `back-ticks around` it.
</pre>
Inline `code` has `back-ticks around` it.  

**Fenced code blocks**
<pre>
```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
 
```python
s = "Python syntax highlighting"
print s
```
 
```
No language indicated, so no syntax highlighting. 
But let's throw in a <b>tag</b>.
```
</pre>

![fenced code syntax highlighting](http://sundeepkamath.in/wp-content/uploads/2015/08/syntaxhighlighting.png)

***

###### Images
These can be shown in two ways  
**Inline style**
<pre>
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")
</pre>
![InlineLogo](http://sundeepkamath.in/wp-content/uploads/2015/08/icon48.png)

**Reference style**
<pre>
![alt text][logo]

[logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 2"
</pre>
![alt text][logo]

[logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 2"

***

###### Links
**Inline link without title**
<pre>
[I'm an inline-style link](https://www.google.com)
</pre>
[I'm an inline-style link](https://www.google.com)

**Inline link with title**
<pre>
[I'm an inline-style link with title](https://www.google.com "Google's Homepage")
</pre>
[I'm an inline-style link with title](https://www.google.com "Google's Homepage")

**Reference style link type 1**
<pre>
[I'm a reference-style link][Arbitrary case-insensitive reference text]

[arbitrary case-insensitive reference text]: https://www.mozilla.org
</pre>
[I'm a reference-style link][Arbitrary case-insensitive reference text]

**Reference style link type 2**
<pre>
[You can use numbers for reference-style link definitions][1]

[1]: http://slashdot.org
</pre>
[You can use numbers for reference-style link definitions][1]

**Reference style link type 3**  
<pre>
Or leave it empty and use the [link text itself].

[link text itself]: http://www.reddit.com
</pre>
Or leave it empty and use the [link text itself].

[arbitrary case-insensitive reference text]: https://www.mozilla.org  
[1]: http://slashdot.org   
[link text itself]: http://www.reddit.com

***

###### Lists
**Unordered lists**
<pre>
* Unordered list can use asterisks
- Or minuses
+ Or pluses
</pre>
* Unordered list can use asterisks
- Or minuses
+ Or pluses

**Indentation**
<pre>
1. And another item.

⋅⋅⋅You can have properly indented paragraphs within list items.  
⋅⋅⋅Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).  
⋅⋅⋅To have a line break without a paragraph, you will need to use two trailing spaces.⋅⋅
⋅⋅⋅Note that this line is separate, but within the same paragraph.⋅⋅
⋅⋅⋅(This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)
</pre>  

4. And another item.  
   You can have properly indented paragraphs within list items.  
   Notice the blank line above, and the leading spaces (at least one, but we'll use three here to also align the raw Markdown).  
   To have a line break without a paragraph, you will need to use two trailing spaces.  
   Note that this line is separate, but within the same paragraph.  
   (This is contrary to the typical GFM line break behaviour, where trailing spaces are not required.)  

**Ordered List**
<pre>
1. One
2. Two
3. Three
</pre>
1. One
2. Two
3. Three

**Ordered list within unordered list**
<pre>
* Title
  1. One
  2. Two
</pre>
* Title
  1. One
  2. Two

**Unordered list within Ordered list**
<pre>
1 Title
  * One
  * Two
</pre>
1. Title
  * One
  * Two

**Sub-ordered list within ordered list**  
<pre>
1. Title  
  1. one
  2. two
</pre>
1. Title  
  1. one
  2. two
