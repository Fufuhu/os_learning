# 概要
OpenshiftコンテナプラットフォームではDocker registry APIに準拠したあらゆるイメージを利用することができる。


# 統合されたOpenshiftコンテナプラットフォームのレジストリ
新しいイメージを即座に加えることのできる組み込みコンテナレジストリ?がOpenshiftには存在する。

新しいイメージが統合レジストリにプッシュされるたびに、
自動で新規のビルドを行い、デプロイを行うことができる。

# サード・パーティのレジストリ

Openshiftコンテナプラットフォームでは3rd partyレジストリを用いてコンテナを構築することができる。
が、自動でビルド->デプロイのフローまでを含めることは難しい(イメージがプッシュされたことを検知する仕組みが無いため)

この場合、Openshiftではイメージストリームの作成に際して、リモートレジストリのタグをフェッチする。
フェッチされたタグを更新するには
```bash
$ oc import-image <stream>
```

を実行すれば、新規イメージがある場合には事前にで意義されたビルドフローとデプロイが行われる。

> ビルドとデプロイの手順を強制的にコード化させることは可能そう......

## 認証について

OpenShiftコンテナプラットフォームではプライベートイメージリポジトリにアクセスするために
ユーザによって提供された認証情報を利用することができる。

詳細は、

https://docs.openshift.com/container-platform/3.3/architecture/additional_concepts/authentication.html#architecture-additional-concepts-authentication

を参照すること