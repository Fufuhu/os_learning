# 概要
Openshiftコンテナプラットフォーム内部では、Kubenetesがコンテナおよびホストのセットをまたがった
コンテナ化アプリケーションの管理を行う。

- デプロイ
- メンテナンス
- アプリケーションの拡張

などを担う。

> Openshift 3.3では Kubenetes 1.3 と Docker 1.10を使う

# Kubenetesクラスタの構成要素
1. 1台以上のMaster
1. 1台以上のNode

## Master
|コンポーネント名|役割|
|---|---|
|API Server|ポッド、サービス、レプリケーションコントローラのためのデータのバリデーションと設定を行う。|
|etcd|マスターの一貫した状態を保管する。他のコンポーネントは"意図した状態"へと自身を変化させるためにetcdをウォッチする|
|Controller Manager Server|クラスタ中に1台がリーダとして存在、etcdの内容をウォッチしている|
|HAProxy|(オプション) nativeメソッドを用いてHA master構成を組んだ場合に利用する|

HA構成を組んだ場合の可用性についての情報は明示的に記述されているので、
それを参照すること(Active-stanbyなどの情報および確保できる冗長性などが確認できる)
https://docs.openshift.com/container-platform/3.3/architecture/infrastructure_components/kubernetes_infrastructure.html

## Node
ノードはコンテナの実行環境を提供する。
いずれのノードもマスターによって管理されなければならない。
ノードはPodを実行するために必要な各種サービス(Docker, Kubelet, serviceプロキシ)などを持っている。

kubenetesはnodeオブジェクトと相互のやり取りを行う。
Masterはnodeオブジェクトの情報をバリデーションし、ヘルスチェックを行う。
ノードの管理の詳細についてはKubenetesのドキュメントに詳しい
https://github.com/kubernetes/kubernetes/blob/master/docs/admin/node.md#node-management
https://kubernetes.io/docs/admin/node/
