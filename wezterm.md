如果需要输入大写，`Shift-x` 来输入大写的 X
`Shift-PageUp` and `Shift-PageDown` 上下翻页
`Ctrl-Shift-F` 进行搜索模式，上下方向键进行选择，`Esc` 退出该模式，`Ctrl-u` 可以清空输入的内容, `Ctrl-r` 可以切换搜索模式（不忽略大小写，忽略大小写，正则式模式）
`Ctrl-Shift-C`，`Ctrl-Shift-V` 进行复制粘贴，`Shift-Insert` 也是粘贴
`Ctrl-Shift-N` 新建窗口（另有 `Super-N`，但没有成功）
`Ctrl-Shift-T` 和 `Super-T(t)` 新建标签，`Super-Shift-[` and `Super-Shift-]` 下一个和上一个标签，`Super-[1-9]` 跳转到标签（与系统按键冲突）
`Ctrl-Shift-Alt-%` and `Ctrl-Shift-Alt-"` 水平和垂直分割窗口，`Ctrl-Shift-ArrowKey` 在窗口间移动

## Scrollback

```
-- How many lines of scrollback you want to retain per tab
config.scrollback_lines = 3500
```
`Ctrl-Shift-K` 清空 scrollback，默认情况下，当前屏幕内容不会变，但无法向前滚动了，相当于只保留了当前一页。将其改为清空全部，见：[ClearScrollback - Wez's Terminal Emulator](https://wezfurlong.org/wezterm/config/lua/keyassignment/ClearScrollback.html)

开启滚动条
```
-- Enable the scrollbar.
-- It will occupy the right window padding space.
-- If right padding is set to 0 then it will be increased
-- to a single cell width
config.enable_scroll_bar = true
```

## # Quick Select Mode
`CTRL-SHIFT-SPACE` 开启快速选择模式，将匹配特定的内容，并以输入前缀的方式来快速选取一个匹配内容，并将其复制到粘贴板。
匹配的内容可以自行配置。