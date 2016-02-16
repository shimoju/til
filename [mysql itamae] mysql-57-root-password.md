# MySQL5.7の初期rootパスワード変更をItamaeで自動化してみる

MySQL5.7では、初期rootパスワードが「無し」から一時的なパスワードを起動時に生成する方針となった。
一時パスはログファイル（標準では`/var/log/mysqld.log`）に出力されている。

問題は自動化するときだが、[AnsibleでRPMのMySQL5.7のインストール・初期設定を自動化してみた · DQNEO起業日記](http://dqn.sakusakutto.jp/2016/01/ansible-mysql57-setup.html)を参考にしてItamaeで以下のように書いてみた。

```ruby
template '/root/.my.cnf'

execute 'set root password' do
  command <<-EOL
    tmp_pass=$(grep 'temporary password' /var/log/mysqld.log | awk -F'root@localhost: ' '{print $2}') &&
    mysql -u root -p"$tmp_pass" --connect-expired-password -e "ALTER USER 'root'@'localhost' IDENTIFIED BY '#{node.mysql.root_password}';"
  EOL
  not_if "mysql -u root --defaults-file=/root/.my.cnf -e 'show databases;'"
end
```

.my.cnf

```
[client]
user=root
password='<%= node.mysql.root_password %>'
```

[AnsibleでRPMのMySQL5.7のインストール・初期設定を自動化してみた · DQNEO起業日記](http://dqn.sakusakutto.jp/2016/01/ansible-mysql57-setup.html)では一時パスからの変更時にも`.my.cnf`を使っているが、一時パスは重要なものではないので`-p`オプションでパスワードを含める形にした。

ここ（初回変更）で完全を求めない理由はもう一つあって、

[[Ansible] dev.mysql.comのrpmでMySQL 5.6をインストール - Qiita](http://qiita.com/hnakamur/items/f3243235793d57a341df)
> mysqlのドキュメントには--passwordオプションでパスワードを指定するのは、psで他のユーザにパスワードを見られてしまうので安全ではないと書かれています。

`.my.cnf`を使えば`-p`オプションのパスワードは入らないけど、`-e`オプションのSQLには新しいパスワードが含まれてしまっているので片手落ちになる。完全にやるには手で（インタラクティブモードで）やるしかない。自動化してコマンドからやる以上逃れられないので、初回は仕方ない、許容できるリスクだと思ってこの方法にした。
