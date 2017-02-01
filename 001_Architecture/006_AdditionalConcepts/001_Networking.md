# Networking

Kubenetesでは、pod間で互いに通信することができる。
内部ネットワークでIPアドレスを個々のPodに割り当てることで実現している。

PodがIPアドレスを持つ = 単一のマシンのメタファー

Pod間でlinkを確立する必要はないが、
IPアドレス直で他のポッドとやり取りするのはおすすめしない、
サービスを構成し、サービス名で通信することを推奨する。

## Openshiftのネットワークにおける推奨事項

- 非推奨
    - PodのIPアドレスで通信
- 推奨
    - Podのサービス名で通信

# OpenShiftコンテナプラットフォームDNS
複数のPod構成を伴った複数のサービスで、
サービス間で通信させる場合、環境変数がユーザ名、サービスIPなどの
ために生成される。

- サービスを使ってやり取りしている限りはIPアドレスを意識する必要はない。

```
Master内部にSkyDNSを持っており、53番ポートで受け付けている。
```

- デフォルトの検索スペース
    - .<pod_namespace>.cluster.local
- Default
    - <pod_namespace>.cluster.local
- Service
    - <service>.<pod_namespace>.svc.cluster.local
- Endpoint
    - <name>.<namespace>.endpoints.cluster.local

# ネットワークプラグイン
OpenShiftではKubenetesと同様のプラグインモデルを提供している。

## OpenShiftコンテナプラットフォームSDN
OpenShiftコンテナプラットフォームSDNでは、
単一のクラスタネットワークに所属する
ホスト上に存在するすべてのPodをつなぐことができる。

