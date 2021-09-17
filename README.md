# 目录

- [vault](#vault)
    - [获取库的基础路径](#获取库的基础路径)
    - [获取全局设置](#获取全局设置)
- [workspace](#workspace)
    - [获取当前编辑器](#获取当前编辑器)
- [editor](#editor)
    - [获取编辑器内容](#获取编辑器内容)
    - [设置编辑器内容](#设置编辑器内容)
    - [获取当前文件](#获取当前文件)
    - [获取光标位置](#获取光标位置)
    - [设置光标](#设置光标)
    - [获取滚动位置](#获取滚动位置)
    - [设置滚动位置](#设置滚动位置)
    - [获取自定义frontmatter](#获取自定义frontmatter)
    - [获取选择文字](#获取选择文字)
    - [发送通知](#发送通知)
- [plugin](#plugin)
    - [生命周期](#生命周期)
    - [设置快捷键](#设置快捷键)
    - [设置文件菜单](#设置文件菜单)
    - [加载设置](#加载设置)
    - [保存设置](#保存设置)
- [其他](#其他)
    - [网络请求](#网络请求)

# vault

## 获取库的基础路径

```ts
import {
  FileSystemAdapter,
} from "obsidian";

const basePath = (
          this.app.vault.adapter as FileSystemAdapter
        ).getBasePath()
```

## 获取全局设置

```ts
const config = this.app.vault.config
```

# workspace

## 获取当前编辑器

```ts
import { MarkdownView } from 'obsidian';

// 获取md编辑器
getEditor() {
    const mdView = this.app.workspace.getActiveViewOfType(MarkdownView);
    if (mdView) {
      return mdView.editor;
    } else {
      return null;
    }
}

```

# editor

## 获取编辑器内容

```ts
const editor = this.getEditor()
const content = editor.getValue()
```

## 设置编辑器内容

```ts
const editor = this.getEditor()
// string为需要设置的字符串，采用\n换行
const content = editor.setValue("string")
```

## 获取当前文件

```ts
const file = this.app.workspace.getActiveFile();
```

## 获取光标位置

```ts
const editor = this.getEditor()
const cursor = editor.getCursor()
```

## 设置光标

```ts
const editor = this.getEditor()
// line是行数，ch是距离第一个字符串的位置
editor.setCursor({line: 'number', ch: 'number'});
```

## 获取滚动位置

```ts
const editor = this.getEditor()
const { left, top } = editor.getScrollInfo();
```

## 设置滚动位置

```ts
const editor = this.getEditor()
// left,top均为number
editor.scrollTo(left, top);
```

## 获取自定义frontmatter

```ts
  getFrontmatterValue(key: string, defaultValue: any = undefined) {
    const file = this.app.workspace.getActiveFile();
    if (!file) {
      return undefined;
    }
    const path = file.path;
    const cache = this.app.metadataCache.getCache(path);

    let value = defaultValue;
    if (cache?.frontmatter && cache.frontmatter.hasOwnProperty(key)) {
      value = cache.frontmatter[key];
    }
    return value;
  }
```

## 获取选择文字

```ts
  getSelectedText(editor: any) {
    if (editor.somethingSelected()) {
      let cursorStart = editor.getCursor(true);
      let cursorEnd = editor.getCursor(false);
      let content = editor.getRange(
        { line: cursorStart.line, ch: 0 },
        { line: cursorEnd.line, ch: editor.getLine(cursorEnd.line).length }
      );
      return {
        start: { line: cursorStart.line, ch: 0 },
        end: {
          line: cursorEnd.line,
          ch: editor.getLine(cursorEnd.line).length,
        },
        content: content,
      };
    } else {
      var lineNr = editor.getCursor().line;
      var contents = editor.getDoc().getLine(lineNr);
      let cursorStart = {
        line: lineNr,
        ch: 0,
      };
      let cursorEnd = {
        line: lineNr,
        ch: contents.length,
      };
      let content = editor.getRange(cursorStart, cursorEnd);
      return { start: cursorStart, end: cursorEnd, content: content };
    }
  }
```

## 发送通知

```ts
import {
  Notice,
} from "obsidian";

// 参数分别为message和timeout
new Notice("message", 1000);
```

# plugin

## 生命周期

```ts
// 挂载时的钩子，一般用于加载设置，设置命令行等初始化操作
async onload(){}

// 卸载插件时的钩子，处理插件中存在的一些副作用，如定时器等
async onunload(){}
```

## 设置快捷键

```ts
this.addCommand({
  id: "id",
  name: "name",
  checkCallback: (checking: boolean) => {
    // do something...
  },
});
```

## 设置文件菜单

```ts
this.app.workspace.on(
  "file-menu",
  (menu: Menu, file: TFile, source: string) => {
    menu.addItem((item: MenuItem) => {
      item.setTitle("title").onClick(() => {
        console.log("dome something...")
      });
    });
  }
);
```

## 加载设置

```ts
async loadSettings() {
  // DEFAULT_SETTINGS 为默认设置
  this.settings = Object.assign(DEFAULT_SETTINGS, await this.loadData());
}
```

## 保存设置

```ts
async saveSettings() {
    await this.saveData(this.settings);
}
```

# 其他

## 网络请求

可以安装 `node-fetch` 进行请求
