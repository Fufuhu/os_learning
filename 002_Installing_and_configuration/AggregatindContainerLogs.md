https://docs.openshift.com/container-platform/3.3/install_config/aggregate_logging.html

# 概要
OpenShift Contaienerプラットフォームの管理
EFKスタックをデプロイしてログを集約することができる。

クラスタ内にEFKスタックをデプロイするとすべてのログがElasticsearchに集約され、
KibanaUIの中にすべてのログを集約できるよう保存される。

# デプロイ前の準備

1. クラスタにルータを導入済みであることを確認する
1. Elasticsearchに必要なストレージを確保できていることを確認する。
1. Ansibleベースのインストールでは、logging-deployerテンプレが作成されるはず。できていないときは別途手動で作成する
   ```bash
   $ oc apply -n openshift -f \
    /usr/share/openshift/examples/infrastructure-templates/enterprise/logging-deployer.yaml
   ```
1. 新規プロジェクトを作成する
    1. 単一のプロジェクトに実装されたらEFKスタックはクラスタ内に所属するすべてのログを集約する
    ```bash
    $ oadm new-project logging --node-selector=""
    $ oc project logging
    ```
    (上) loggingプロジェクトを例として利用している。
1. Loggingサービスのアカウントとカスタムロールを作成する
    ```bash
    $ oc new-app logging-deployer-account-template
    ```
1. KibanaがOAuthクライアントを利用できるように、Deployerサービスアカウントを有効化する
    ```bash
    $ oadm policy add-cluster-role-to-user oauth-editor \
       system:serviceaccount:logging:logging-deployer
    ```
    前に作ったプロジェクトを指定する(今回はlogging)
1. 特権(priviledged)セキュリティコンテキストをにFluentdサービスアカウント加えることで、システムのログをマウントし読み込めるようにする。平行して、cluster-readerロールを与えることでPodのメタデータを読み取れるようにする
    ```bash
    $ oadm policy add-scc-to-user privileged  \
    system:serviceaccount:logging:aggregated-logging-fluentd 
    $ oadm policy add-cluster-role-to-user cluster-reader \
    system:serviceaccount:logging:aggregated-logging-fluentd
    ```
# デプロイヤのパラメータの指定
ConfigMap、SecretまたはテンプレートのパラメータのいずれかでEFKのデプロイ設定は指定される。
loggin-deployer ConfigMap -> logging-deployer secret -> 環境変数の順で参照。

ConfigMapないで指定しているパラメータについてはoc new-appでは表示されないので要注意。

## ConfigMapの作成手順
1. ConfigMapを作る
    ```bash
    $ oc create configmap logging-deployer \
   --from-literal kibana-hostname=kibana.example.com \
   --from-literal public-master-url=https://master.example.com:8443 \
   --from-literal es-cluster-size=3 \
   --from-literal es-instance-ram=8G
    ```

1. ConfigMapのyamlファイルを作成後に修正する
    ```bash
    $ oc edit configmap logging-deployer
    ```
    詳細なパラメータは
    - https://docs.openshift.com/container-platform/3.3/install_config/aggregate_logging.html
    - https://docs.openshift.com/container-platform/3.3/install_config/aggregate_logging.html#aggregated-elasticsearch
    
    を参照すること
1. セキュリティ関連のファイルをデプロイヤに提供するためにsecretを作成
    (ここで作成しない場合はランダムに作られたものが提供される)
    ```bash
    $ oc create secret generic logging-deployer \
   --from-file kibana.crt=/path/to/cert \
   --from-file kibana.key=/path/to/key
    ```
    詳細なパラメータは
    - https://docs.openshift.com/container-platform/3.3/install_config/aggregate_logging.html

    を参照すること

# EFKスタックのデプロイ

デプロイパラメータを読み取り、デプロイを管理するためのデプロイヤーPodを作成するためのテンプレートを使って
EFKスタックをデプロイする。

- テンプレートパラメータなしの場合
```
$ oc new-app logging-deployer-template
```

- テンプレートパラメータありの場合
```
$ oc new-app logging-deployer-template \
             --param IMAGE_VERSION=3.3.0 \
             --param MODE=install
```

|パラメータ|内容|
|---|---|
|IMAGE_PREFIX|The prefix for logging component images. For example, setting the prefix to registry.access.redhat.com/openshift3/ creates registry.access.redhat.com/openshift3/logging-deployer:latest.|
|IMAGE_VERSION|The version for logging component images. For example, setting the version to v3.3 creates registry.access.redhat.com/openshift3/logging-deployer:v3.3.|
|MODE(default:install)|Mode to run the deployer in; one of install, uninstall, reinstall, upgrade, migrate, start, stop.|

デプロイヤを実行することで、デプロイヤポッドが作成され、その名前が表示される。
ポッドが起動するまで待つ。実行状況は以下のコマンドで確認可能
```
$ oc get pod/<pod_name> -w
```

まれにRunning statusからComplete statusになる。
もし、長くかかり過ぎている場合は下記コマンドでさらなる詳細を確認できる。
```
$ oc describe pod/<pod_name>
```

デプロイに失敗した場合はポッドのログを確認する
```
$ oc logs -f <pod_name>
```