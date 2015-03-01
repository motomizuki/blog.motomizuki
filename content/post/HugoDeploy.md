+++
categories = ["hugo", "wercker", "memo"]
date = "2015-02-28T11:46:32+09:00"
description = "Hugo,  Wercker, 自動デプロイ"
keywords = ["hugo", "wercker", "CI" ]
title = "HugoDeploy"
jtitle = "Hugoのインストールから自動デプロイまで"

+++
このブログを作るにあたって行ったことのメモ

## ブログ構成
* エンジン : Hugo
* ホスティングサービス : GitHub Page
* CIサービス : Wercker

## 流れ
基本的には[Hugo](http://gohugo.io/overview/introduction/)のドキュメント通り.
自動デプロイに関しても[Automated deployments](http://gohugo.io/tutorials/automated-deployments/)通り.

### Hugoのインストール
GitHubの[リポジトリ](https://github.com/spf13/hugo)から直接入手
```zsh
$ go get -v github.com/spf13/hugo
```
Macの場合はbrewでインストール可能
```zsh
$ brew install hugo
```

### ブログを作成
#### 本体の作成
```zsh
$ hugo new site blog.motomizuki
```
以下は`blog.motomizuki`内での作業
#### コンテンツの作成
```zsh
$ hugo new about.md
```

#### 記事の作成
```zsh
$ hugo new post/title.md
```
Markdownの基本テンプレートは`archetypes`内の`default.md`が適用される

#### ブログにテーマ(hyde-x)を適用
```zsh
$ mkdir themes
$ cd themes
$ git clone https://github.com/zyro/hyde-x
$ hugo -t hyde-x
```

#### ブログのpreview
```zsh
$ hugo server --theme=ThemaName --buildDraft
2 pages created
0 tags created
0 categories created
in 5 ms
Serving pages from exampleHugoSite/public
Web Server is available at http://localhost:1313
Press Ctrl+C to stop
```
ビルドで使用するテーマをThemaNameで設定

ブログを編集しながら確認したい場合は`--watch`を付ける
```zsh
$ hugo server --theme=ThemaName --buildDrafts --watch
2 pages created
0 tags created
0 categories created
in 5 ms
Watching for changes in exampleHugoSite/content
Serving pages from exampleHugoSite/public
Web Server is available at http://localhost:1313
Press Ctrl+C to stop
```

### WerckerでGitHub Pagesへ自動デプロイ
Werckerを使い，ブログソースを`git push`したら
GitHub Pages(https://username.github.io)に自動デプロイ
するようにする．
#### gitの設定
```zsh
$ git init
```
自動デプロイの際に**public**ディレクトリが自動生成されるので,
既存の**public**はgitで管理しない.
```zsh
$ echo "/public" >> .gitignore
```
テーマのstaticディレクトリ内にファイルが存在しないので
Werckerに無視してもらうよう設定する．
```zsh
echo "User-agent: *\nDisallow:" > static/robots.txt
```

#### GitHubのプロジェクトに追加
```zsh
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:motomizuki/blog.motomizuki.git
git push -u origin master
```

#### GitHub Pages用リポジトリを用意
username.github.ioというリポジトリを作成.

#### Werckerの設定
[Wercker](http://wercker.com/)のアカウントをREGISTER USING GITHUBから作成.
APPを作成し必要な項目を埋ていく，**Configure access**ではwithout using an SSHを選択する.
APPのビルド，デプロイ設定を__Wercker.yml__で記述し, リポジトリ(blog.motomizuki)に追加する.
```Wercker.yml
box: wercker/default
build:
    steps:
        - arjen/hugo-build@1.0.2:
            theme: hyde-x
            flags: --buildDrafts=true
deploy:
    steps:
        - lukevivier/gh-pages@0.2.1:
            token: $GIT_TOKEN
            basedir: public
            repo: motomizuki/motomizuki.github.io
```
***インデントは半角スペース4つで！***
repoでデプロイ先のリポジトリを設定しないと上手く動かなかった.
ビルド, デプロイも成功となるが，実際にリポジトリは更新されないという状況.

#### APPの設定
SettingのDEPLOY TARGETSをcustomに．
Add new variableでnameをGIT_TOKEN, valueはGitHubのPersonal access tokensでgenerateできる文字列にして登録．

### 終わり
ここまで設定すると記事を書いてソースをpushすると自動でデプロイされる．
