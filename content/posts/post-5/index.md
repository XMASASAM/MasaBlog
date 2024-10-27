+++
title = 'Hugoの使い方メモ'
date = 2024-10-28T01:41:51+09:00
draft = false
tags=['hugo']
+++

Hugoの使い方をメモしていく。
<!--more-->

# 注意点
Windowsの場合は
PowerShellを使う必要がある。
Windows PowerShell では正常に動作しない場合がある。(PowerShellとWindows PowerShellは別物)

# ポストを追加
```
hugo new content content/posts/post-X/index.md
```

# 実行
```
hugo server
```

# css追加(anankeの場合)
``` {name="$HugoProject$/hugo.toml"}
...

[params]
    custom_css = ["custom.css"]
```

``` {name="$HugoProject$/assets/ananke/css/custom.css"}
cssを書く
```

custom.cssの箇所はどんな名前でもいい。
ファイルを追加したい場合は
``` {name="$HugoProject$/hugo.toml"}
...

[params]
    custom_css = ["custom.css","custom2.css"]
```
$HugoProject$/assets/ananke/css/custom2.css　で追加でOK。


