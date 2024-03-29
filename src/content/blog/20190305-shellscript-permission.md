---
create: '2019-03-05'
update: '2019-03-18'
title: 'Day 1: Linux と Shellscript と Permission'
tags: [cebu, linux, shellscript]
published: true
---

- Qiita: [1日目 LinuxとShellscriptとPermission](https://qiita.com/OriverK/items/23509ae58fc0b4cbf462)

## Linux

### メイン課題

1. ディレクトリ 1 を作成
2. ディレクトリ 1 の中にファイル 1 を作成
3. ディレクトリ 2 を作成
4. ディレクトリ 2 の中にディレクトリ 3 を作成
5. ディレクトリ 1 をディレクトリ 3 の中に移動

```sh
mkdir dir1
touch dir1/file1
mkdir -p dir2/dir3
mv dir1 dir2/dir3
```

### コマンドのオプションがわからなくなったとき

```sh
man コマンド名
```

### 今回使用したオプション付きコマンド

```sh
mkdir -p
 # -p, --parents　no error if existing, make parent directories as needed

mv O1 O2
 # O2が同ディレクトリ内に存在しないとき：リネーム
 # O2が同ディレクトリ内に存在するとき：移動

rm -r
 # -r, -R, --recursive
 # remove directories and their contents recursively
 # recursive：再帰的：ディレクトリ内のものに一つ一つに対し処理

ls -la
 # manコマンドで調べると-la自体は無いが、-lと-aオプションが存在する。
 # -a, --all do not ignore entries starting with
 # すべてのファイルやディレクトリを表示する。
 #この時、隠しフォルダや。から始まるディレクトリも表示される。

 # -l  use a long listing format
 # ファイルの詳細を表示する。
```

## シェルスクリプト

OS のシェルまたはコマンドラインインタプリタ向けに書かれたスクリプト言語。拡張子は `.sh`

```sh
#シェルスクリプト作成
touch test.sh

#シェルスクリプト内編集(先の課題のコードを記述してみる。
#!/bin/sh

mkdir dir1
touch dir1/file1
mkdir -p dir2/dir3
mv dir1 dir2/dir3
```

1 行目は『シバン』と呼ばれ、UNIX のスクリプトの `#!` から始まる 1 行目を指す。起動してスクリプトを読み込むインタプリタを指定する。インタプリタ（interpreter）とは、プログラミング言語で書かれたソースコードないし中間表現を逐次解釈しながら実行（英語版）するプログラムのこと。この test.sh を活用することで、先の課題を自動的に行なうことができる。

```sh
#起動方法
./test.sh
```

## Permission

```sh
ls -la test.sh
#表示結果
-rw-rw-r-- 1 vagrant vagrant 0 Mar  4 23:48 test.sh
#左のチャンクから、user:所有者、usergroup:vagrant、others:誰でも、を意味する。
#また、r＝read権限、w=write権限、ここでは見えないがx=execute権限(実行許可）を指す。
```

### chmod=change mode

権限編集モード

```sh
# すべてのユーザーに実行権限を与える／禁止する。
chmod +x test.sh / chmod -x test.sh
#グループに書き込み権限をその他のユーザーには実行権限を禁止する。
chmod g+w,o-x test.sh
```

### 別の表示方法(数字）

```sh
# r、w、xにそれぞれ4，2，1を割り当て、表すことができる。
rw-r--r--    =>  644
r-xr--r--    =>  544
#数字表示での権限編集

#すべてのユーザーに書き込み権限と読込権限を与える。
chmod 666 test.sh
```

### PermissionとPermitの違い

プログラムは Permission 。

どちらも名詞で『許可』を意味するが、ニュアンスが違う。日本語で説明するとズレるので、英語辞典で確認したい。あと、Permission のスペル注意。
