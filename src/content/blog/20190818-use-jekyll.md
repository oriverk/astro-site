---
create: '2019-08-18'
update: '2019-12-01'
title: 'GithubPages で Jekyll を使ってみよう'
tags: [githubpages, jekyll]
published: true
---

from [Qiita: GithubPagesでjekyllを使ってみよう](https://qiita.com/OriverK/items/ce48102c66c9fa97b33e) より

GithubPagesJekyll を利用し、静的ページを作成した。

## major update history

- 20190825：GithubPages with jekyll 作成
  - 目的：自分の情報などを纏めるサイト作成の為
  - remote theme：[fongandrew / hydeout](https://github.com/fongandrew/hydeout)
- 20191206：デザイン等変更
- 目的：アクセシビリティ改善、デバイスによる見た目差を小さく

[twitter](https://twitter.com/not_you_die/status/1204474648223576064)

- [デザインを大幅修正した件](https://qiita.com/OriverK/items/ce48102c66c9fa97b33e#%E3%83%87%E3%82%B6%E3%82%A4%E3%83%B3%E3%82%92%E5%A4%A7%E5%B9%85%E4%BF%AE%E6%AD%A3%E3%81%97%E3%81%9F%E4%BB%B6)

## Environment

- Windows
- vm OS: Linux Ubuntu Bento/Bionic
  - Ruby ruby 2.5.1p57

## Refferrence

- [GithubPages：https://pages.github.com/](https://pages.github.com/)
- [Jekyll：https://jekyllrb.com/](https://jekyllrb.com/)
- [Jekyll Themes :hydeout](http://jekyllthemes.org/themes/hydeout/)
- [幣GithubPagesリポジトリ：https://github.com/oriverk/oriverk.github.io](https://github.com/oriverk/oriverk.github.io)

## デフォルトのGithubPages作成

### jekyllの準備、作成

```sh
gem install bundler jekyll
jekyll new oriverk.github.io
```

### Gitリモートリポジトリ作成、git push

**リポジトリ名を `username.github.io` にすること**

>> Github Pages HP
>>If the first part of the repository doesn’t exactly match your username, it won’t work, so make sure to get it right.

### デフォルト状態完成

上記に従ったデフォルト状態では、[テーマminimaが適用され、こんなページになる。](https://jekyll.github.io/minima/)

## jekyllテーマを変更

- 参照：[jekyll-theme-hydeout 4.0.2](https://rubygems.org/gems/jekyll-theme-hydeout)

### テーマをhydeoutに変更してみる

上記の `jekyll new username.github.io` で作成したディレクトリに手を加えていく。

まず、Gemfile を編集し、 `gem "github-pages"` をアンコメント。また、今回使用するテーマ hydeout の gem を書き加える。

```rb title=Gemfile
# uncomment
gem "github-pages", group: :jekyll_plugins
# add
gem "jekyll-theme-hydeout"
```

次に `_config.yml` を編集する。今回はリモートテーマを使用するので、 theme を `remote_theme` に変更する。さらにプラグインも追加しておく。

```yml title=_config.yml
# theme: mininma
remote_theme: fongandrew/hydeout

plugins:
  - jekyll-feed # pre writen
  - jekyll-remote-theme　# added
  - github-pages # added
```

```sh
bundle install
bundle exec jekyll server
```

## エラー

- 参照：[GitHub Pages ビルドのトラブルシューティング](https://help.github.com/ja/articles/troubleshooting-github-pages-builds)

### Layoutが見つからないエラー

記事投稿中にターミナルログを消してしまって、エラー文を覚えていないが、該当の.md ファイル中の `Layout` を `Layout:page` に変えたら、エラーが解消された。

### Invalid theme folder: _sass

参照

- [`Invalid theme folder: _sass` when using Github Pages with remote_theme #7630](https://github.com/jekyll/jekyll/issues/7630)
- [Page build failed: Invalid Sass or SCSS](https://help.github.com/en/articles/page-build-failed-invalid-sass-or-scss)

参照先での議論を見る限り、問題ない…・？

## カスタマイズ

※使用テーマやテーマの追加方法によって、ディレクトリ構造が違うので、他テーマは使うべきではない言葉なので修正してください

ディレクトリに `_include` フォルダを作成。

### headタグ内の情報を書き込む

`_include` フォルダ内に、 `head.html` ファイルを作成する

```html title=head.html
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta http-equiv="content-type" content="text/html; charset=utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1" />
  <title>
    {% if page.title == "Home" %}
    {{ site.title }}{% if site.tagline %} &middot; {{ site.tagline }}{% endif %}
    {% else %}
    {{ page.title }} &middot; {{ site.title }}
    {% endif %}
  </title>
  <link rel="stylesheet" href="{{ "/assets/css/main.css" | relative_url }}" />
</head>
```

### ページネーション

```yml title=_config.yml
# _config.yml
plugins:
  - jekyll-paginate

paginate: 5
paginate_path: '/blog/page:num'
sidebar_blog_link: '/blog'
```

```sh
bundle install
bundle exec jekyll server
```

### Googleアナリティクス

- 参照: [Google Analytics for Jekyll](https://desiredpersona.com/google-analytics-jekyll/)

```html title=google-analytics.html
<!-- google-analytics.html -->
{% if jekyll.environment == 'production' and site.google_analytics %}
<script>
  (function (i, s, o, g, r, a, m) {
  i['GoogleAnalyticsObject'] = r; i[r] = i[r] || function () {
    (i[r].q = i[r].q || []).push(arguments)
  }, i[r].l = 1 * new Date(); a = s.createElement(o),
    m = s.getElementsByTagName(o)[0]; a.async = 1; a.src = g; m.parentNode.insertBefore(a, m)
  })(window, document, 'script', 'https://www.google-analytics.com/analytics.js', 'ga');
  ga('create', '{{ site.google_analytics }}', 'auto');
  ga('send', 'pageview');
</script>
{% endif %}
```

`_include/head.html` に書き加える。

```html title=_include/head.html.erb
<head>
  {% include google-analytics.html %}
</head>
```

`_config.yml` にトラッキング ID を書き加える。トラッキング ID は、UA-　から始まる ID。

```yml title=_config.yml
google_analytics: UA-〇〇〇〇〇
```

github に上げて完了。

### Twitterカード追加

- 参照
  - [サルワカ：【2019年版】Twitterカードとは？使い方と設定方法まとめ](https://saruwakakun.com/html-css/reference/twitter-card)
  - [Supporting Twitter Cards with Jekyll](http://davidensinger.com/2013/04/supporting-twitter-cards-with-jekyll/)
  - [Twitter Cards on Jekyll](https://www.brianbunke.com/blog/2017/09/06/twitter-cards-on-jekyll/)

`_include/twitter-card.html` を作成

```html title=_include/twitter-card.html.erb
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@not_you_die"/>
<!-- <meta name="twitter:creator" content="@{{ page.author }}"/> -->
<meta name="twitter:title" content="{{ page.title }}"/>

{% if page.summary %}
  <meta name="twitter:description" content="{{ page.summary }}"/>
{% else %}
  <meta name="twitter:description" content="{{ site.description }}"/>
{% endif %}

{% if page.image %}
  <meta name="twitter:card" content="summary_large_image"/>
  <meta name="twitter:image" content="{{ site.url }}{{ page.image }}"/>
{% else %}
  <meta name="twitter:card" content="summary"/>
  <meta name="twitter:image" content="{{ site.title_image }}"/>
{% endif %}
```

`_include/head.html` 内に書き加える

```html title=_include/head.html.erb
<head>
  {% include twitter-card.html %}
</haed>
```

Git に push し、[Twitter Card Validator](https://cards-dev.twitter.com/validator) で確認

### sidebar-nav-link

![Image from Gyazo](https://i.gyazo.com/aa38f41cc183d8e25de273d3020d4257.png)

こんな感じで、サイドバーに nav-link 付け足したい。

## デザインを大幅修正した件

- 改修方針
  - モバイルファースト
  - ページ数を少なく
  - サイズ指定に px を極力使わず、rem や％を使う

### 実作業

```sh
git branch changeDesign
git checkout changeDesign
```

#### `Gemfile` と `_config.yml` から不要なものを削除

```rb title=Gemfile
source 'https://rubygems.org'

gem 'github-pages', group: :jekyll_plugins
group :jekyll_plugins do
  gem 'jekyll-admin'
  gem 'jekyll-feed', '~> 0.6'
end
install_if -> { RUBY_PLATFORM =~ /mingw|mswin|java/ } do
  gem 'tzinfo', '~> 1.2'
  gem 'tzinfo-data'
end
gem 'wdm', '~> 0.1.0', install_if: Gem.win_platform?
gem 'jekyll-coffeescript'
```

#### 最低限必要なディレクトリ構造を考える

自分でデザインを構成するには、jekyll と liquid でできることを理解する必要があった。

- `_include` 配下のファイルは `{% include footer.html %}` の形で変数展開の様に扱う
  - ただし、画像はこの方法では利用できない。svg は ok
- _site 中身は `bundle exec jekyll serve` で自動で生成されるので中身は触らない

自分の結果

```plaintext
# ...
# .
# ├── _config.yml
# ├── _includes
# |   ├── svgファイル類
# |   └── `head`内パーツ類（head.html, twitter-card, google-analytics, bootstrap ...
# |   └── `body`内のhtmlパーツ類
# ├── _layouts ─ default.html
# ├── _posts   ─ 2007-10-29-why-every-programmer-should-play-nethack.md
# ├── assets   ─ jpg / png 画像類
# └── index.html # can also be an 'index.md' with valid front matter
```

#### スクロール関連の変更

```scss title=_layout.scss
body{
  background-image:url("../taiwan.jpg");
  background-size:cover;
  background-position: right;
  background-attachment:fixed;

  -ms-overflow-style: none; // IE用
  overflow: -moz-scrollbars-none; // Firefox用
  &::-webkit-scrollbar { // Chrome用
    display: none;
  }
}
```
