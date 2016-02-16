# CentOS標準のRubyでjsonがないよって言われたとき

itamaeを`bundle exec itamae`で動作させようとしたらjsonがrequireできないとエラーが出た。

原因を探したところ、CentOSでは一部の標準ライブラリがgem形式で入って、標準ライブラリがあるディレクトリにシンボリックリンクすることで標準ライブラリとして動作する形になっている（bigdecimalやpsych）。

```shell
ls -l /usr/share/ruby
lrwxrwxrwx. 1 root root    42  2月 15 14:14 psych -> /usr/share/gems/gems/psych-2.0.0/lib/psych
lrwxrwxrwx. 1 root root    45  2月 15 14:14 psych.rb -> /usr/share/gems/gems/psych-2.0.0/lib/psych.rb
```

だが、jsonはなぜかリンクされていない。
そのため、`bundle exec`などbundlerでgemのロードパスを絞った状態下だと、jsonは標準ライブラリではなくgemの扱い=Gemfileに入っていないのでロードできなくなってしまう。

```
[vagrant@localhost ~]$ irb
irb(main):001:0> require 'json'
=> true
irb(main):002:0>
[vagrant@localhost ~]$ bundle exec irb
irb(main):001:0> require 'json'
LoadError: cannot load such file -- json
	from (irb):1:in `require'
	from (irb):1
	from /usr/bin/irb:12:in `<main>'
```

解決するにはpsychなどと同様、標準ライブラリのある`/usr/share/ruby`にシンボリックリンクすればよい。
（`/usr/lib64/ruby`はC拡張のライブラリ`.so`が入るらしい）

```shell
sudo ln -snv /usr/share/gems/gems/json-1.7.7/lib/json.rb /usr/share/ruby
sudo ln -snv /usr/share/gems/gems/json-1.7.7/lib/json /usr/share/ruby
sudo ln -snv /usr/lib64/gems/ruby/json-1.7.7/lib/json /usr/lib64/ruby
```

Gemfileに`json`を含めても解決するが、特定のOSの問題をアプリケーション側で解決するのはいけてない感じがする（この解決策もイケてるとは言いがたいが、それを含めても）。
