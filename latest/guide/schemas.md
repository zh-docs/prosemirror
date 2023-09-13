# 模式

每个 ProseMirror 文档都有一个与之相关的模式。模式描述了文档中可能出现的节点类型，以及它们的嵌套方式。例如，顶层节点可以包含一个或多个区块，段落节点可以包含任意数量的内联节点，并可对其进行任意标记。

有一个包含[基本模式]()的软件包可供使用，但 ProseMirror 的好处在于它允许你定义自己的模式。

## 节点类型

文档中的每个节点都有一个类型，该类型代表其语义和属性，例如在编辑器中的呈现方式。

定义模式时，要枚举模式中可能出现的节点类型，并用[规格对象(spec object)]()描述每种类型：

```
const trivialSchema = new Schema({
  nodes: {
    doc: {content: "paragraph+"},
    paragraph: {content: "text*"},
    text: {inline: true},
    /* ... and so on */
  }
})
```

如此便定义了一种模式，其中文档可以包含一个或多个段落，每个段落可以包含任意数量的文本。

每个模式都必须至少定义一个顶级节点类型（默认名称为 `doc`，但可以进行配置），以及一个用于文本内容的`text`类型。

内联的节点必须使用`inline`属性声明这一点（不过对于定义为内联的`text`类型，可以省略这一点）。

## 内容表达式

上述示例模式中`content`字段的字符串称为内容表达式。它们控制着该节点类型有效的子节点序列。

例如，你可以用`paragraph`表示一个段落，或者用`paragraph+`表示一个或多个段落。同样，`paragraph*`指零个或多个段落。`caption?`表示零或一个标题节点。你还可以使用类似正则表达式来限定范围，比如节点名称后的`{2}` (两个)，`{1, 5}`(一到五个)或`{2,}`(两个或更多)。

这些表达式可以组合起来创建一个序列，例如`heading paragraph+`表示首先是一个标题，然后是一个或多个段落。您还可以使用管道 `|` 操作符在两个表达式之间进行选择，如`(paragraph | blockquote)+`。

有些元素类型组在模式中会出现多次--例如，你可能有一个`块(block)`节点的概念，它可能出现在顶层，但也嵌套在`块引号(blockquotes)`中。你可以通过为`节点规格(node specs)`赋予一个`group`属性来创建一个节点组，然后在表达式中使用这个组的名称来引用。

```
const groupSchema = new Schema({
  nodes: {
    doc: {content: "block+"},
    paragraph: {group: "block", content: "text*"},
    blockquote: {group: "block", content: "block+"},
    text: {}
  }
})
```

这里的`block+`等同于`(paragraph | blockquote)+`。

建议在具有块内容的节点（如上例中的`doc`和`blockquote`）中始终要求至少有一个子节点，因为当节点为空时，浏览器会将其完全折叠，使其难以编辑。

节点在 or 表达式中出现的顺序非常重要。在为非选项节点创建默认实例时，例如，为了确保文档在替换步骤后仍符合模式，将使用表达式中的第一个类型。如果是一个组，则使用组中的第一个类型（由组中成员在节点图中出现的顺序决定）。如果我在示例模式中调换 "段落 "和 "blockquote "的位置，只要编辑器尝试创建一个块节点，就会出现堆栈溢出--它会创建一个 "blockquote "节点，其内容至少需要一个块，因此它会尝试创建另一个 "blockquote "作为内容，如此循环。

## 标记

```
const markSchema = new Schema({
  nodes: {
    doc: {content: "block+"},
    paragraph: {group: "block", content: "text*", marks: "_"},
    heading: {group: "block", content: "text*", marks: ""},
    text: {inline: true}
  },
  marks: {
    strong: {},
    em: {}
  }
})
```

## 属性

## 序列化和解析

## 扩展一个模式
