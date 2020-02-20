# Ansible で MYSQL Master-Slave 環境を構築する

# コマンド

デプロイサーバでこんな感じに実行する。

```
> ansible-playbook -i hosts.ini db.yml
```

## 構築

1. master 1台, slave 2台とする

1. aws t2-micro を 4台用意する。

	1. これらは同じ VPC ネットワークに設置する
	2. これのうち一つは deploy 用として用意。いらなけりゃなくていい。

1. デプロイサーバから `ssh db1.atys.local` とホスト名でログインできるように設定しておく

	1. `~/.ssh/priv.pem` に鍵を設置する
	2. `~/.ssh/config` に設定を設置する
	3. このリポジトリをデプロイサーバに設置する

1. Route53 に atys.local をプライベートDNSとして登録する

	1. master は `172.31.20.128` で `group_vars/db.yml` でベタ打ちになってるので合わせたほうが良いっぽい
	3. master のプライベートIP を `db1.atys.local` と対応させるようにする。
	3. slave のプライベートIP を `db2.atys.local` と `db3.atys.local` と対応させるようにする。

1. ansible をインストールする

	1. `yum install python3-pip`
	2. `pip install ansible-playbook`

1. 実行する
	
	1. `ansible-playbook -i hosts.ini db.yml`

### `~/.ssh/config` ファイル設定例

```
Host db1.atys.local db2.atys.local db3.atys.local
	IdentityFile ~/.ssh/priv.pem
```

# 設定の変更とか

1. mysql root パスワードは `group_vars/db.yml` に書いています。

# 対処的なこと

Security group で mysql の通信ができない時は slave が master のデータを取りにいけない。その時は security group を追加すれば問題がない。（もちろん、IP Range で範囲限定して）

# いいかもなぁ

`my.cnf` の設定とかは `roles/mysql/vars/main.yml` にベタ書きだから、なんか振りかけられるようになれればいいかもなぁ

# see

- [Ansibleで本番レベルのMySQLレプリケーションを構築して見よう。](https://qiita.com/park-jh/items/34e6434d71e685a48f07)
- [【AWS】VPC内でPrivate DNSによる名前解決](https://qiita.com/y_takeshita/items/2eb5e6abb5eb5516d1de)
- [Configuration Management 101: Writing Ansible Playbooks](https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks)
