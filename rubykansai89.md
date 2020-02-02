# 他言語由来のRubyの機能

author
:   Kazuhiro NISHIYAMA

content-source
:    第89回 Ruby関西 勉強会

date
:   2020/02/08

allotted-time
:   60m

theme
:   lightning-simple

# 自己紹介

- Ruby コミッター
- Twitter, GitHub: `@znz`
- 株式会社Ruby開発

# 使ってきた言語

- BASIC でプログラミング言語を知る
- C MagazineやNIFTY-ServeなどでC言語などのいろんな言語を勉強
- Perl で CGI などを作成

# 使ってきた言語

- JPerl という日本語対応の Perl の開発が終わりそうだったので、
  ネイティブで日本語に対応している Ruby に乗り換え
- 他の言語も興味に応じて勉強

# Agenda

- グローバル変数
- シンボルと文字列
- 小ネタ

# グローバル変数

- 一部が組み込み変数・特殊変数などと呼ばれる
- `$` + 記号, `$-` で始まるものなど
- スコープはグローバルとは限らない
- Perl 由来の物が多い
- できれば使うのを避けた方が良い

# ワンライナー向け

- `$_` : `Kernel.#gets` などで使われる
  - ワンライナーなどの短いものでわかった上で使うなら良い
  - 普通のプログラムでは使わない
- `$.` : 普通のプログラムでは `ARGF.lineno` を使うと良い

# コマンドラインオプション系

コマンドラインオプションの説明を参照

- `$-a`, `$-i`, `$-l`, `$-p` など
  - 主にワンライナー向けのオプション

# ワンライナー向け (awk 由来?)

- `$/` (`$-0`) : awk の RS (input record separator)
- `$\` : 出力レコード区切り

# 非推奨になったもの

2.7 から nil 以外にすると警告、非推奨

- `$;` (`$-F`) : `String#split` のデフォルト
- `$,` : `Array#join` のデフォルト

# シェル由来 (Perl 経由)

- `$0`, `$PROGRAM_NAME` : 実行中のプログラム名 (フルパスとは限らない)
- `$?` : 子プロセスのステータス
- `$*` : ARGV

# IO 関連

- `$<` : `ARGF` (リダイレクト記号由来)
- `$>`, `$stdout` : デフォルトは `STDOUT`
- `$stderr`, `$stdin` : 別名なし

# IO 関連 補足説明

例えば子プロセスに

- 引き継ぎたい場合は `STDOUT.reopen`
- 引き継ぎたくない場合は `$stdout = io`

という使い分け

# エラー処理関連 (Perl 由来)

- `$!` : 例外
- `$@` : バックトレース

# 正規表現関連 (Perl 由来)

ローカルスコープかつスレッドローカル

- `$~` : MatchData (これは Ruby 独自?)
  - `Regexp.last_match` で代用可能
- `$1`, `$2`, `$3`, ... : n 番目のキャプチャ
  - `Regexp.last_match(1)`, `Regexp.last_match(2)`, ... で代用可能

# 正規表現関連 (Perl 由来)

- `` $` ``, `$&`, `$'` : マッチ部分より前、マッチ部分全体、マッチ部分の後ろ
- `$+` : 最後のキャプチャ (滅多に使われない)

# 別名

- `$:`, `$LOAD_PATH`, `$-I`
  - `$LOAD_PATH` 推奨、 `$:` もよく使われる
  - `$-I` は普通は使わない
- `$DEBUG`, `$-d`
  - 普通は `$DEBUG` を使う

# 警告

- `$VERBOSE`, `$-v`, `$-w`
  - `nil`, `false`, `true`
  - 普通は `$VERBOSE` を使う
- `$-W`
  - 0, 1, 2
  - 普通は `$VERBOSE` を使う

# 今は未使用

- `$KCODE`, `$-K`
- `$=`

# シンボルと文字列

- Symbol は ruby 1.6.0 から
- それまでは `:sym` は Integer
- (`?a` (文字リテラル) も 1.8.7 までは Integer)

# シンボル

- たぶん Lisp 由来
- メソッド名の内部表現が元々の用途だった?
- 元々は、プログラム作成時に決まっていて、実行中には変化しないものに向いている

# 文字リテラル

- 1.9 の m17n 対応で Integer から String に変更
- Ruby は大クラス主義なので文字クラスは導入される
  (文字とは? という問題もあった)

# ヒアドキュメント

- シェルや Perl 由来
  - `assert_equal(<<-EXPECTED, <<-ACTUAL)` のように行の途中に複数回書ける
  - PHP のように行の末尾にしかおけない言語も
- <https://blog.n-z.jp/blog/2017-06-25-indented-here-document.html>
  - `<<~` での行頭空白削除ルールは Perl と Ruby で違う

# 埋め込みドキュメント

- `=begin` ... `=end` で複数行のドキュメントを埋め込める
  - 最近はあまり使われてない
- Perl の POD (the Plain Old Documentation format) 由来?
  - `=pod` ... `=cut`

# 文字列補間

- 補完ではない, string interpolation
- シェルや Perl では `"$foo $bar"`
  - 変数をそのまま書くと展開
- Ruby は`#{}` でくくる
  - 曖昧さがないので `"${foo}_bar"` のような書き分けが不要

# 数値リテラル

- 0755, 0xFF : C 言語など昔から多くの言語で使える
- 0o755, 0d123, 0b1011 : だんだん増えていった
  - 0b に対応したのは Ruby は他の言語と比べて早かった印象

# 正規表現リテラル

- オプションは Perl 由来の物が多い (`ixom`)
- Perl の `/s` は Perl のデフォルトの `^`, `$` が `\A`, `\Z` になっているので存在しない
- Ruby の `/s` は m17n 対応前から SJIS の正規表現リテラルの意味

# まとめ

- 主に Perl 由来のグローバル変数について紹介
- 参考にしてそうなものとは違っているものもある
- `<<~` や数値リテラルのように今は他の言語と影響を与え合っている