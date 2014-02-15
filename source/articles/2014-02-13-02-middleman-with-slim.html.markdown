---
title: ブログのレイアウトファイルを ERB から Slim へ変更した
tags: middleman, slim
---

簡単だった。Gemfile に `gem "slim", "~> 2.0.0"` と書いて `bundle install` し、erb ファイルを slim へ変換するだけ。
出力される HTML ファイルを pretty にするため、次の1行を `config.rb` に記述した。

```ruby
set :slim, { :pretty => true, :sort_attrs => false, :format => :html5 }
```

しかし問題がある。development モードでの反応が ERB だったときよりも遅いのだ。

Chrome を再起動したら遅い問題が解決したような気がする。
