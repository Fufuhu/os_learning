# 概要
ポッドのネットワークのために以下のプラグインを提供している。

1. ovs-subnet
    - フラットなpod-networkを提供する
1. ovs-multitenant
    - podおよびサービスをプロジェクトレベルで分離するためのプラグイン
    - VNIDがprojectにひも付き、トラフィックがどのプロジェクトに紐づくものなのかを明確にし、
        他のプロジェクトのトラフィックが紛れ込まないようにする事ができる。
    - VNID 0は全podと通信可能なVNIDである。

# Masterの設計
SDNに関連した情報をetcdの内部に格納する
クラスタネットワークから使用していないサブネットを割り当てる。

- デフォルト設定
    - クラスタネットワークは10.128.0.0/14
    - /23のサブネットでkリイダスト512のサブネットを実現することができる
    - 512のサブネットを切り出せる(≒クラスタあたり、500サイトくらい格納できる?)
    - MasterはNodeとしても動作するよう設定しない限りはPodとの通信は不可。

ovs-multitenantプラグインを利用しているとき、
Masterはプロジェクトの作成、削除を確認して、VXLAN VNIDの管理を行い、
トラフィックが適切に流れるよう制御を行う。


# Nodeの設計
- br0
    - OVS(Open vSwitch)ブリッジデバイス
    - Podのコンテナが接続される
    - Openshift SDNはこのブリッジに対してsubnet-specific(サブネットベース??)ではないフロールールの設定をこのデバイスに行う。
      (ovs-multitenant pluginなど)
- lbr0
    - Linuxブリッジ(Linux Bridge)デバイス
    - Dockerサービスのゲートウェイアドレスとして利用??
- tun0
    - OVSの内部ポート ??
    - クラスタサブネットのゲートウェイアドレスとして利用?
    - 外部ネットワークへのアクセスに使う
    - SDNでnetfilterとルーティングルールを設定し、クラスタサブネットから
        NATを介して外部ネットワークにアクセスする。
- vlinuxbr/vovsbr
    - 詳細はドキュメントの内容を参照
    - Openshift外で作られたDockerコンテナへのアクセスを提供
- vxlan0
    - br0のポート1 OVS VXLANデバイス

## Pod起動時の挙動

1. ホスト側のPodのvethインタフェースをlbr0からbr0へと移動する。
1. OpenFlowのルールをOVSデータベースに投入し、正しいOVSポートに送信されるようルーティング設定を追加する。
1. ovs-multitenantプラグインを利用している場合、Podからの通信はVNIDとセットで送受信するようになっており、
    他のVNIDをもつPodには送信されない。

## パケットの流れ

### コンテナ間(コンテナA->コンテナB)
#### 同一ホスト上のパケットの流れ
(コンテナA) eth0 -> vethA -> br0 -> vethB -> eth0 (コンテナB)
#### ホストをまたいだパケットの流れ
(コンテナA) eth0 -> vethA -> br0 -> vxlan0 -> network -> vxlan0 -> br0 -> vethB -> eth0 (コンテナB)

### コンテナ-外部間
最終的にはコンテナが外部ホストに繋ぐ場合はこんなふうに見える
(ホストA) eth0 -> vethA -> br0 -> tun0 -> (NAT) -> eth0(物理デバイス) -> Internet

```
ネットワーク分離やるならovs-multitenant pluginを利用しなさい。
```