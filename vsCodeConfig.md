vs code config

### 方便开发的插件：

- Sublime Text Keymap and Settings Importer
- Go
- JSON tools
- GitLens

### keybindings.json

```js
// Place your key bindings in this file to overwrite the defaults
[
    {
        "key": "ctrl+t",
        "command": "-workbench.action.showAllSymbols"
    },
    {
        "key": "ctrl+t",
        "command": "editor.action.transpose"
    },
    {
        "key": "alt+p",
        "command": "workbench.action.openWorkspace"
    },
    {
        "key": "ctrl+shift+alt+k",
        "command": "workbench.action.editor.nextChange", // next change with git
        "when": "editorTextFocus"
    },
    {
        "key": "alt+f5",
        "command": "-workbench.action.editor.nextChange",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+alt+j",
        "command": "workbench.action.editor.previousChange",
        "when": "editorTextFocus"
    },
    {
        "key": "shift+alt+f5",
        "command": "-workbench.action.editor.previousChange",
        "when": "editorTextFocus"
    }
]

```

### UserSetting.go

```js
{
    // 基本配置
    // 更改多光标的方式为 alt+click， 这样可以让 Ctrl+Click 为 Go To Defination
    "editor.multiCursorModifier": "alt",
    "editor.snippetSuggestions": "top",
    "editor.formatOnPaste": true,
    "files.trimFinalNewlines": true,
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true
    // 使用 sublime 的快捷键
    "sublimeTextKeymap.promptV3Features": true,
    "go.lintTool": "gometalinter"
    "go.lintFlags": ["-min_confidence=.8"]
}
```

### 创建 Snippet

- File > Preferences > User Snippets, select the language then create a snippet.

```golang
{
	"fmt.Println": {
		"prefix": "fmt",
		"body": [
            "fmt.Println(\"----------------- ${1:flag}\"$2)"
		],
		"description": "fmt.Println"
	}
}
```
