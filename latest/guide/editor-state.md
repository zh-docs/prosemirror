# 编辑器状态

是什么构成了编辑的状态？当然是文档，还有当前的选区。例如，当你禁用或启用某个标记，但尚未开始输入该标记时，需要有一种方法来存储当前标记集已更改的事实。

```
import {schema} from "prosemirror-schema-basic"
import {EditorState} from "prosemirror-state"

let state = EditorState.create({schema})
console.log(state.doc.toString()) // An empty paragraph
console.log(state.selection.from) // 1, the start of the paragraph
```
