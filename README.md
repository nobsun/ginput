# ginput

## 動機

- `ghci`を使ったときのセッションの記録したい。
- セッション中にどのようなエラーがあったのかを後から分析したい。
- コードのどこでエラーが起きたかも記録しておきたい（`catn`を呼んでいるのはそのときのソースコードを記録にのこすため）。
- `:reload`の発行の時間間隔などを記録しておきたい。

## `ginput`： Ghciへの入力を加工するフィルタ機能

- `:load`命令を発行する前に、`:edit`命令を発行する。
    - `:edit`命令は単に`catn`を起動するように`.ghci`に設定する
- `:reload`命令は`:edit`命令に置き換える。

## `catn`

- シェルコマンド `cat -n` に相当する。単にテキストファイルを行番号付きで標準出力に出すだけの単純なコマンド。

## `.ghci`

```
import Data.Time
_prompt _ n = (utcToLocalTime <$> getCurrentTimeZone <*> getCurrentTime) >>= print >> return ">>> "
:set prompt-function _prompt
:set -interactive-print=Text.Show.Unicode.uprint
:set editor catn
```

## `lghci` スクリプト

`ghci`起動スクリプト。このスクリプトをPATHのとおっているとこに置く。

```bash
#!/bin/bash
ginput | tee -i -a ghci.log | stack exec -- ghci 2>&1 | tee -i -a ghci.log
```

