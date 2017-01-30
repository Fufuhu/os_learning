# 概要

OpenshiftコンテナプラットフォームのマスターはOAuthサーバを含んでいる。
OAuthアクセストークンを使ってアクセスすることができる。

特定の認証手段をmaster configurationファイルを通じて、OAuthに対して提供することができる。
初期状態ではあらゆるアクセスが拒否される。(Deny Allサービス・プロバイダが選択されている)

master configuration file ... /etc/origin/master/master-config.yaml ???

## 手順
Ansibleを用いて認証プロバイダを設定することができる。

1. htpasswd
1. Allow All(認証なし)
1. LDAP

などなど

```
# htpasswd auth
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/master/htpasswd'}]
# Defining htpasswd users
#openshift_master_htpasswd_users={'user1': '<pre-hashed password>', 'user2': '<pre-hashed password>'
# or
#openshift_master_htpasswd_file=<path to local pre-generated htpasswd file>

# Allow all auth
#openshift_master_identity_providers=[{'name': 'allow_all', 'login': 'true', 'challenge': 'true', 'kind': 'AllowAllPasswordIdentityProvider'}]

# LDAP auth
#openshift_master_identity_providers=[{'name': 'my_ldap_provider', 'challenge': 'true', 'login': 'true', 'kind': 'LDAPPasswordIdentityProvider', 'attributes': {'id': ['dn'], 'email': ['mail'], 'name': ['cn'], 'preferredUsername': ['uid']}, 'bindDN': '', 'bindPassword': '', 'ca': '', 'insecure': 'false', 'url': 'ldap://ldap.example.com:389/ou=users,dc=example,dc=com?uid'}]
# Configuring the ldap ca certificate
#openshift_master_ldap_ca=<ca text>
# or
#openshift_master_ldap_ca_file=<path to local ca file to use>

# Available variables for configuring certificates for other identity providers:
#openshift_master_openid_ca
#openshift_master_openid_ca_file
#openshift_master_request_header_ca
#openshift_master_request_header_ca_file
```

### 認証プロバイダの設定

|名前|内容|
|---|---|
|name|プロバイダ名がユーザ名の前置詞として付く|
|challenge|trueに設定した場合、CLIなどからの認証されていないトークンリクエストはWWW-Authenticateチャレンジヘッダを送付する|
|login|trueに設定した場合、Webクライアントからの認証されていないトークンリクエストはloginページへとリダイレクトされる|
|mappingMethod|新しい識別子がログイン時にどのようにマッピングされるかを定義*1|

*1 https://docs.openshift.com/container-platform/3.3/install_config/configuring_authentication.html#mapping-identities-to-users

### master configurationファイルの書き方

/etc/origin/master/master-config.yaml

1. Allow All
1. HTPasswd
1. Keystone
1. LDAP認証
1. Basic認証
1. RequestHeader認証
1. GitLab認証
1. OpenIDコネクト
1. Tokenオプション

> Todo以下の３つを記述する。

#### Allow All
#### HTPasswd
#### LDAP認証
