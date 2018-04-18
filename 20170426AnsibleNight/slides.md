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

## Infrastructure as Code の再利用

* 例えば複数のサービスでnginxを使いたい場合
    * NG → サービスごとにコードを書く
    * OK → 1回書いたコードを各サービスで再利用する
* コード再利用でスノーフレークサーバーを防ぐ

---

## AnsibleではRoleを使って再利用

* Ansibleでコードを再利用する仕組みにRoleがある。
    * task, handler, 変数, template等をまとめて再利用。
* Roleの管理は `ansible-galaxy` コマンドと `requirements.yml` で可能。

```bash
$ ansible-galaxy install bennojoy.nginx
```

```yaml:requirements.yml
- src: https://github.com/bennojoy/nginx
  version: master
  name: nginx_role
```

---

## Roleを再利用

* 絵

---

## Roleをアップデートすると、、、

* 絵

---

**Roleを使い回すほど管理が大変になる**

---

## Roleの後方互換性を保つ

Roleを更新するとき、Playbook側でなるべく作業が必要ないよう配慮する。

* Roleの破壊的な変更はなるべくしない。
    * Role名や変数名は変えない。
    * 変数をdeprecatedにするなら、debug moduleを使ってその旨を実行時に出力するなど、気付かせてあげるべき。
* 変数を追加するときは必ず `defaults/` で初期値を設定する。

---

## 問題は実際の更新作業

* Roleの更新をいかに各Playbook側でキャッチするのか？
* PlaybookごとにRoleの更新→テストを全部やるのか？
* 100サービスとかあったら？

**継続的自動的にやるしかないのでは？**

---

## ソフトウェアのプラクティスに倣おう

Infrastructure as Codeの利点の1つは、ソフトウェアの開発プロセスを採用できること。

> Infrastructure as Codeは、ソフトウェア開発のプラクティスをインフラのオートメーションに活かすアプローチだ。 (p.5)

---

## Ansible Role ≒ プログラムのpackage

* Ansible RoleはRubyGemsやJSのyarnなどと似た位置付け。
* ソフトウェアでもimported packageの継続的更新は課題になっている。
    * [定期的にyarn updateするには - おもしろwebサービス開発日記](http://blog.willnet.in/entry/2018/03/18/163405)
    * [Jenkins に bundle update した上で Pull Request させる - @kyanny's blog](http://blog.kyanny.me/entry/2012/11/06/003902)

---

**Jenkins !! (or CI)**



---

## RoleもCIで継続的アップデート

こんな感じでうまくいきそう？

1. 更新確認用のブランチにcheckout。
1. installしているroleの更新を確認。
1. 更新があればupdate。
1. update後にserverspec等テストを回す。
1. テストが通ったらJenkinsからPR。
1. 結果をslackに通知。

---

## CircleCIでやってみる

```yaml
```

---

## まとめ

* Ansibleでも依存モジュールを継続的自動更新をするべき。
* そのためにもまずはCIに載せるところから。
* `ansible-galaxy update` コマンドがほしい。
