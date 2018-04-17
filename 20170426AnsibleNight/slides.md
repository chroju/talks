Ansible Roleを継続的に自動アップデートする
====

**Ansible Night 2018.04**

chroju

---

## 自己紹介

![chroju](https://en.gravatar.com/userimage/112557146/b0bb9918ea567d117d4745f14fb3822a.jpg?size=200)

**chroju**

* [GitHub](https://github.com/chroju) / [Qiita](http://qiita.com/chroju) / [Twitter](https://twitter.com/chroju)
* インフラの面倒を見たり運用の改善したりする仕事
* Ansible / Terraform / influxDB / Python あたりが武器

---

## Infrastructure as Code のコードを使い回す

* 書籍『Infrastructure as Code』より

---

## AnsibleではRoleとgalaxy

* AnsibleではコードをRoleとして分割、再利用できる。
* Roleの管理は `ansible-galaxy` コマンドと `requirements.yml` で。

```yaml:requirements.yml
- src: https://github.com/bennojoy/nginx
  version: master
  name: nginx_role
```

---

## Roleを使い回す

* 絵

---

## Roleをアップデートすると、、、

* 絵

---

## Roleを使い回すほど管理が大変になる

Roleを使い回せば使い回すほど、更新時の影響は大きくなる。

* 後方互換性は保たれているか？
* Roleを更新したことを、installしているリポジトリにどう伝達するか。
* installしているリポジトリの更新をどうこなすか。

---

## まずは後方互換性を保つこと

* Roleの破壊的な変更はなるべくしない。
    * 更新前の方針（Role名とか変数名とか）は崩さない。
    * 変数をdeprecatedにするなら、debug moduleを使ってその旨を実行時に出力するなど、気付かせてあげるべき。
* 変数を追加するときは必ず `defaults/` で初期値を設定する。

---

## Roleのinstall元をどう更新していくか

* Roleの更新をいかに各Playbookへ通知するか
* PlaybookごとにRoleの更新→テストを全部やるのか？？

---

## ソフトウェアのプラクティスに倣おう

Infrastructure as Codeの利点の1つは、ソフトウェアの開発プロセスを採用できること。

---

## Ansible Role ≒ プログラムのpackage

* Rubygems, pip, yarnなどなどと同等。
* それらの更新プラクティスを参考にする。
    * [定期的にyarn updateするには - おもしろwebサービス開発日記](http://blog.willnet.in/entry/2018/03/18/163405)
    * [Jenkins に bundle update した上で Pull Request させる - @kyanny's blog](http://blog.kyanny.me/entry/2012/11/06/003902)

---

**Jenkins !! (or CI)**



---

## RoleもCIしてみる

1. 更新確認用のブランチにcheckout
1. installしているroleの更新を確認
1. 更新があればupdate
1. update後にserverspec等テストがあれば回す
1. テストが通ったらJenkinsがPR
1. 結果をslackに通知

---

## CircleCIでやってみる

```yaml
```

---

## まとめ

* Ansibleでも依存モジュールを継続的自動更新をするべき。
* そのためにまずはCIに載せるところから。
* `ansible-galaxy update` コマンドがほしい。
