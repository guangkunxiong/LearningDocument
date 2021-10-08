## markdown 语法学习

### 段落

要创建段落，请使用空白行将一行或多行文本进行分隔。

### 换行

在一行的末尾添加两个或多个空格，然后按回车键,即可创建一个换行

### 强调

通过将文本设置为粗体或斜体来强调其重要性。

    	#### 粗体

要加粗文本，请在单词或短语的前后各添加两个星号（asterisks）或下划线（underscores）。如需加粗一个单词或短语的中间部分用以表示强调的话，请在要加粗部分的两侧各添加两个星号（asterisks）。

例如：`**Bold**` **Bold**

#### 斜体

要用斜体显示文本，请在单词或短语前后添加一个星号（asterisk）或下划线（underscore）。要斜体突出单词的中间部分，请在字母前后各添加一个星号，中间不要带空格。

例如：`*meow*` _italic_

#### 粗体（Bold）和斜体（Italic）

三个星号或下划线

例如：`***meow***` **_meow_**

### 引用语法

要创建块引用，请在段落前添加一个 `>` 符号。

#### 多个段落的快的引用

块引用可以包含多个段落。为段落之间的空白行添加一个 `>` 符号。

例如

```
> 段落1
>
>> 段落2
```

> 段落 1
>
> > 段落 2

#### 带有其它元素的块引用

```
##### 段落标题

- test

- test

  **嘿嘿嘿**
```

> ##### 段落标题
>
> - test
>
> - test
>
>   **嘿嘿嘿**

### 列表语法

```
*   This is the first list item.
*   Here's the second list item.

    > A blockquote would look great below the second list item.

*   And here's the third list item.
```

- This is the first list item.

- Here's the second list item.

  > A blockquote would look great below the second list item.

- And here's the third list item.

#### 代码块

代码块通常采用四个空格或一个制表符缩进。当它们被放在列表中时，请将它们缩进八个空格或两个制表符。

````text
1. Open the file.

2. Find the following code block on line 21：

   ```html
   <table>
   	<tr>
           <td>1</td>
       </tr>
   </table>
````

3. Update the title to match the name of your website.

````

1. Open the file.

2.  Find the following code block on line 21：
	```html
	<table>
		<tr>
	        <td>1</td>
	    </tr>
	</table>
````

3. Update the title to match the name of your website.

```
1. Open the file containing the Linux mascot.

2. Marvel at its beauty.

   ![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png)

3. Close the file.


```

1.  Open the file containing the Linux mascot.
2.  Marvel at its beauty.

    ![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png)

3.  Close the file.

### 代码块语法

要将单词或短语表示为代码，请将其包裹在反引号 (``) 中。

#### 语法高亮

使用此功能，您可以为编写代码的任何语言添加颜色突出显示。要添加语法突出显示，请在受防护的代码块之前的反引号旁边指定一种语言。

````
​```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
​```
````

```json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

#### 转义反引号

```
``Use `code` in your Markdown file.``
```

`` Use `code` in your Markdown file. ``

### 分隔线

要创建分隔线，请在单独一行上使用三个或多个星号 (`***`)、破折号 (`---`) 或下划线 (`___`) ，并且不能包含其他内容。

### 链接语法

```text
这是一个链接 [Markdown语法](https://markdown.com.cn)。
```

这是一个链接 [Markdown 语法](https://markdown.com.cn)。

#### 网址和 Email 地址

```text
<https://markdown.com.cn>
<fake@example.com>
```

<https://markdown.com.cn>
<fake@example.com>

#### 带格式化的链接

强调链接, 在链接语法前后增加星号。 要将链接表示为代码，请在方括号中添加反引号。

```text
I love supporting the **[EFF](https://eff.org)**.
This is the *[Markdown Guide](https://www.markdownguide.org)*.
See the section on [`code`](#code).
```

I love supporting the **[EFF](https://eff.org)**.
This is the _[Markdown Guide](https://www.markdownguide.org)_.
See the section on [`code`](#code).

#### 引用类型链接

引用样式链接是一种特殊的链接，它使 URL 在 Markdown 中更易于显示和阅读。参考样式链接分为两部分：与文本保持内联的部分以及存储在文件中其他位置的部分，以使文本易于阅读。

```
- `[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle`
```

- `[1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle`

### 图片语法

![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png)

```
[![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png "test")](https://www.baidu.com)

[![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png "test")](https://www.baidu.com)

```

[![百度图片](//www.baidu.com/img/540x258_2179d1243e6c5320a8dcbecd834a025d.png "test")](https://www.baidu.com)

### 转义字符串语法

要显示原本用于格式化 Markdown 文档的字符，请在字符前面添加反斜杠字符 () 。

\*

#### 特殊字符自动转义

在 HTML 文件中，有两个字符需要特殊处理： 和 。 符号用于起始标签， 符号则用于标记 HTML 实体，如果你只是想要使用这些符号，你必须要使用实体的形式，像是 和 。`<` `&` `<` `&` `<` `&`

&copy;

4 < 5

### 内嵌 HTML 标签

#### 行级內联标签

HTML 的行级內联标签如 `<span>`、`<cite>`、`<del>` 不受限制，可以在 Markdown 的段落、列表或是标题里任意使用。依照个人习惯，甚至可以不用 Markdown 格式，而采用 HTML 标签来格式化。例如：如果比较喜欢 HTML 的 `<a>` 或 `<img>` 标签，可以直接使用这些标签，而不用 Markdown 提供的链接或是图片语法。当你需要更改元素的属性时（例如为文本指定颜色或更改图像的宽度），使用 HTML 标签更方便些。

例如：

```text
This **word** is bold. This <em>word</em> is italic.
```

This **word** is bold. This <em>word</em> is italic.

#### 区块标签

区块元素 ── 比如 `<div>`、`<table>`、`<pre>`、`<p>` 等标签，必须在前后加上空行，以便于内容区分。而且这些元素的开始与结尾标签，不可以用 tab 或是空白来缩进。Markdown 会自动识别这区块元素，避免在区块标签前后加上没有必要的 `<p>` 标签。

<table>
	<tr>
   		<td>test1</td>
        <td>test2</td>
        <td>test3</td>
	</tr>    
</table>

#### HTML 用法最佳实践

出于安全原因，并非所有 Markdown 应用程序都支持在 Markdown 文档中添加 HTML。如有疑问，请查看相应 Markdown 应用程序的手册。某些应用程序只支持 HTML 标签的子集。

对于 HTML 的块级元素 `<div>`、`<table>`、`<pre>` 和 `<p>`，请在其前后使用空行（blank lines）与其它内容进行分隔。尽量不要使用制表符（tabs）或空格（spaces）对 HTML 标签做缩进，否则将影响格式。

在 HTML 块级标签内不能使用 Markdown 语法。例如 `<p>italic and **bold**</p>` 将不起作用

### 扩展语法

#### 表格

要添加表，请使用三个或多个连字符（`---`）创建每列的标题,可以控制表格的宽度，并使用管道（`|`）分隔每列。您可以选择在表的任一端添加管道。

```
输入后 会自动补全
| test | test |
```

| test | test |
| ---- | ---- |
|      |      |

##### 对齐

您可以通过在标题行中的连字符的左侧，右侧或两侧添加冒号（`:`），将列中的文本对齐到左侧，右侧或中心。

```
| test | test | Test |
| :--- | :--: | ---: |
```

| test | test | Test |
| :--- | :--: | ---: |

##### 格式化表格中的文字

您可以在表格中设置文本格式。例如，您可以添加链接，代码（仅反引号（```）中的单词或短语，而不是代码块）和强调。

您不能添加标题，块引用，列表，水平规则，图像或 HTML 标签

#### 删除线

您可以通过在单词中心放置一条水平线来删除单词。结果看起来像这样。此功能使您可以指示某些单词是一个错误，并不表示要包含在文档中。若要删除单词，请`~~`在单词前后使用两个波浪号。

```text
~~世界是平坦的。~~ 我们现在知道世界是圆的。
```

~~世界是平坦的。~~ 我们现在知道世界是圆的。

#### 任务列表语法

任务列表使您可以创建带有复选框的项目列表。在支持任务列表的 Markdown 应用程序中，复选框将显示在内容旁边。要创建任务列表，请在任务列表项之前添加破折号（`-`）和方括号，并`[ ]`在其前面加上空格。要选择一个复选框，请 x 在方括号（`[x]`）之间添加 in

```text
- [x] Write the press release
- [ ] Update the website
- [ ] Contact the media
```

- [ ] Write the press release
- [ ] Update the website
- [ ] Contact the media

#### 使用 Emoji 表情

在大多数情况下，您可以简单地从[Emojipedia](https://emojipedia.org/) 等来源复制表情符号并将其粘贴到文档中。许多 Markdown 应用程序会自动以 Markdown 格式的文本显示表情符号。从 Markdown 应用程序导出的 HTML 和 PDF 文件应显示表情符号。

**Tip:** 如果您使用的是静态网站生成器，请确保将 HTML 页面编码为 UTF-8。

```text
去露营了！ :tent: 很快回来。

真好笑！ :joy:
```

去露营了！ :tent: 很快回来。

真好笑！ :joy:
