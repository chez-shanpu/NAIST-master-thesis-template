# NAIST修士論文用TeXテンプレート
## 特徴
- Dockerコンテナを使うことでLaTeXのセットアップ作業を必要としません．
  - ホスト環境でのビルドも可能です
- 以下のツールを用いて自動ビルドすることが可能です
  - GitHub Actions
  - BitBucket Pipelines

## 環境
- macOS 10.14 or later
- Ubuntu 18.04 LTS or later
- Windows 10

Docker
- Docker Desktop for Mac 2.1 or later
- Docker 18.06 or later
- Docker Desktop for Windows

Dockerコンテナは内部でTeX Live 2020 on Ubuntu 20.04 LTSを使用します. 詳細は[こちら](https://github.com/pddg/latex-docker)．

ホスト環境でのビルドはVSCodeの拡張機能を利用しているため，VSCodeの使用を推奨します（好きなエディタと`Makefile`を利用しての作業も可能です）.

![demo](figures/screenshot.png)

## 機能
### Makefile
PDFの生成に関連した操作は全て，デフォルトでDockerコンテナを利用します．
これは`USE_DOCKER`という環境変数によって制御されています．
デフォルトでは`USE_DOCKER=yes`です．
Dockerは不要でネイティブで動かしたい場合，`USE_DOCKER=no`とすることでこれを無効化できます．

また，Windowsで利用する場合は以下のようにすることでDockerの有効無効を切り替えることが出来ます．

```
# 無効
# PowerShell
$ env:USE_DOCKER="no"
# cmd.exe
$ set USE_DOCKER=no

# 有効
# PowerShell
$ env:USE_DOCKER="yes"
# cmd.exe
$ set USE_DOCKER=yes
```

### make pdf
main.texをコンパイルし，完全なPDFを生成するコマンドです．
このコマンドでは`\subfile`によって分割したファイルを個別にコンパイルすることはサポートされていません．

このツールは全体を通してコンパイルにはlatexmkを使用します．デフォルトで使用されている.latexmkrcをカスタマイズするには，`make latexmkrc`の項を参照してください．

### make watch
latexmkによる変更検知時の自動ビルドを実行します．
Dockerを利用しない場合，viewerの指定があればビルドしたPDFを開きます．
Dockerを利用する場合，Dockerコンテナ内からホスト上のPDFビューワを開くことができないため，`-view=none`として開かないようにしています．
自動リロードに対応したPDFビューワで開くと，ビルドの度に自動で変更が反映されます．

このコマンドでは分割したファイルの個別コンパイルをサポートしています．例えば以下の様に実行することで，`sections/usage.tex`だけを`usage.pdf`として閲覧できます．
ターゲットの指定が無い場合，`main.tex`が対象となります．

```sh
$ make watch target=sections/usage.tex
```

### make all
TeXソースのコンパイルを行う前に，前回のコンパイルによる生成物を全て破棄してコンパイルを実行します．

### make clean
コンパイルによる成果物を全て破棄します．
最終成果物のPDFも含めて破棄されることに注意してください．

### make draft
現在のブランチから新しいブランチを切り，空のcommitを行ってリモートへpushします．
その後，hubコマンドを利用してPull Request作成のページを標準のブラウザで表示します．
実行時にブランチ名を指定することが出来ます．
指定しない場合，デフォルト値のWIPが使用されます．

### make latexmkrc
コンテナ内で使用されている.latexmkrcをこのディレクトリ直下にコピーします．
これを編集することで，latexmkの動作をカスタマイズできます．

### make lint
GitHub Actions上で実行されるlintをローカルでも実行します．
事前にNodejsをインストールし`npm install`を実行しておく必要があります．

### make fix
lintに違反したもののうち，自動的に適用が可能なものについて訂正します．
これは例えばprhなどによる表記揺れ訂正が対応しています．
事前にNodejsのインストールと`npm install`の実行が必要です．

## VSCode
VSCode使用時は，LaTeX Workshopプラグインを用いて自動的に動作するよう`.vscode/settings.json`に記述されています。

### VSCodeでの設定
`.vscode/settings.json`を変更することで反映されます．


## GitHub Actions
また，GitHub Actionsを用いて下記の操作をサポートします．
- masterブランチへのpush時に自動ビルド・生成PDFをアップロード
- Pull Request作成時にルールベースでの校正，および，レビューでの指摘
- タグをpushするたびにGitHub Releasesへ自動でPDFをアップロード．


## 作業手順
1. ブランチを切り空のプルリクエストを発行する．<br>
    `make draft branch=ブランチ名`で新しいブランチが切られ，プルリクエスト発行の画面がブラウザで開くので，タイトル等を入力する．
1. 通常通りtexソースを編集してコミットおよびプッシュする．
1. textlintによる校正結果がreviewdogによりプルリクエストにレビューとして投稿されるので，それを参考に修正する．
1. ある程度書けたら共著者や教員・先輩など，内容をレビューする人をレビュアーとしてアサインする．
1. レビュアーの指摘を元に修正．
1. 全ての指摘を修正し，加筆の必要がなくなった時点でmasterへマージする．
1. ローカルのリポジトリでmasterにチェックアウトしてgit pullする．
1. 1へ戻る．

![workflow](figures/textlint_workflow.pdf)

## 元ドキュメント
https://github.com/pddg/latex-template-ja/releases/download/v1.0.1/main.pdf

## License
CC0

## 謝辞
このテンプレートはShoma Kokuryoさんの[pddg/latex-template-ja](https://github.com/pddg/latex-template-ja)を元に作成されました．<br>
素晴らしいテンプレートの作成および提供に感謝いたします．
