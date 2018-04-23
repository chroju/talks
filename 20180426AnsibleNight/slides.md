Ansible Roleの継続的自動Update
====

**Ansible Night 2018.04**

chroju

---

## 自己紹介

![](https://en.gravatar.com/userimage/112557146/b0bb9918ea567d117d4745f14fb3822a.jpg?size=200)

**chroju**

* [GitHub](https://github.com/chroju) / [Qiita](http://qiita.com/chroju) / [Twitter](https://twitter.com/chroju)
* インフラの面倒を見たり運用の改善したりする仕事
* Ansible / Terraform / influxDB / Python あたり

---

## Infrastructure as Code におけるコードの再利用

* 例えば複数のサービスでnginxを使いたい場合
    * 🙅NG → サービスごとにコードを書く
    * 🙆OK → 1回書いたコードを各サービスで再利用する
* コード再利用でスノーフレークサーバーを防ぐ

---

## Ansible Roleでの再利用

* Ansibleでコードを再利用する仕組みはAnsible Role
    * task, handler, 変数, template等をまとめてモジュール化
* Roleの管理は `ansible-galaxy` コマンドと `requirements.yml` で可能

```
$ ansible-galaxy install bennojoy.nginx
```

```yaml:requirements.yml
- src: https://github.com/bennojoy/nginx
  version: master
  name: nginx_role
```

---

## Roleを再利用

![](image/install_role.png)

---

## 再利用 ...

![](image/install_role2.png)

---

## Roleを更新すると？

![](image/install_role3.png)

---

**Roleを使い回すほど管理が大変になる**

---

## Roleの更新をどう反映する？

* Roleの更新をいかに各Playbook側でキャッチするのか？
* PlaybookごとにRoleの更新→テストを全部やるのか？
* 1000 Playbookあったら？

**継続的自動的にやるしかないのでは？**

---

## ソフトウェア開発に倣う

> Infrastructure as Codeは、ソフトウェア開発のプラクティスをインフラのオートメーションに活かすアプローチだ。 (Kief Morris『Infrastructure as Code』p.5)

![](https://www.oreilly.co.jp/books/images/picture978-4-87311-796-6.gif)

---

## Role ≒ プログラムのpackage

* Ansible RoleはRubyGemsやJSのyarnなど、プログラムで言うpackageやmoduleと似た位置付け
* ソフトウェアでもimported packageの継続的更新は課題になっている
    * [定期的にyarn updateするには - おもしろwebサービス開発日記](http://blog.willnet.in/entry/2018/03/18/163405)
    * [Jenkins に bundle update した上で Pull Request させる - @kyanny's blog](http://blog.kyanny.me/entry/2012/11/06/003902)

---

## Jenkins !! (or CI)

![](image/jenkins.png)

---

## RoleもCIで継続的update

こんな感じでうまくいきそう？

1. 更新確認用のブランチにcheckout
1. installしているroleの更新を確認
1. 更新があればupdate
1. update後にserverspec等テストを回す
1. テストが通ったらJenkinsからPR
1. 結果をslackに通知

---

## ansible-galaxyにupdateはない

* 残念ながら、現時点で `ansible-galaxy update` は無い
* roleのバージョン管理機能実装はproposalが出ている
    * [role versioning · Issue #23 · ansible/proposals](https://github.com/ansible/proposals/issues/23)
* 現時点では `git submodule` とかの方がupdateはしやすい

---

## `git submodule` でのサンプル

```
$ git checkout check-update

$ before=$(git submodule status)
 00e6459bc0c3c2cba1b2d84c55d9c03831529617 mysql (remotes/origin/HEAD)

$ git submodule update
$ after=$(git submodule status)

$ if [[ "$before" != "$after" ]]; then ...

# この後でtestしてpushしてPRして通知
```

---

## 余談: Roleの後方互換性

Roleを更新するとき、Playbook側でなるべく作業が必要ないよう配慮する

* Roleの破壊的な変更はなるべくしない
    * Role名や変数名は変えない
    * 変数をdeprecatedにするなら、debug moduleでその旨を実行時に出力するなど、気付かせてあげるべき
* 変数追加のときは必ず `defaults/` で初期値を設定する

---

## まとめ

* Ansibleでも依存モジュールを継続的自動更新をするべき
* そのためにもまずはCIに載せるところから
* `ansible-galaxy update` コマンドがほしい
* 何かいい方法があったら教えてほしい
