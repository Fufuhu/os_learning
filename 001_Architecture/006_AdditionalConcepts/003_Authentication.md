# 概要
ユーザごとに特定のリクエストによる操作が許可されているか否かを判断できる。

# UserとGroup
OpenshiftのユーザはOpenShiftにたいしてリクエストを行い、
各種操作を行うことができる。
ユーザ個別に権限を与えることもできるが、
グループを定義して複数ユーザに同じ権限をまとめて付与することもできる。

## デフォルトの仮想グループ
すべてのユーザに予め与えられる権限がある。

1. system:authenticated
    - すべての認証済みユーザに割当てられるグループ
1. system:authenticated:oauth
    - OAuthアクセストークンを用いて認証したユーザ全てに割り当てられるグループ
1. system:unauthenticated
    - 認証されていない全ユーザに割り当てられるグループ

# APIの認証

以下の方法をつかって認証できる。

1. OAuth Access Tokens
1. X.509 Client Certificates

認証に失敗したら401エラーが返ってきます。

アクセストークンなどが提供されない場合はすべてのアクセスは
system:anonymousユーザによるものと判断され、
system:unauthenticatedグループに突っ込まれる。

# Impersonation

--as=usernameをつけることでsu的な挙動を実現できる。

# OAuth
OpenShiftにはbuild-inのOAuthサーバがあり、これを利用できる。
