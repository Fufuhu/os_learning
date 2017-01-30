# コンテナ
いわゆるDockerコンテナ
同一カーネルを共有しつつプロセス、ファイルシステム、ネットワークなどの分離を提供する。

## Initコンテナ
Podではアプリケーションコンテナとは別に、Initコンテナを持つことができる。
Initコンテナを用いることで、スクリプトのセットアップやコードのバインディングを行うことができるようになる。
Initコンテナが通常のコンテナと異なるのは、常にCompletionな状態にまで
到達しなければならないということである。

具体的な使い方はKubenetesのドキュメンテーションを参照すること。
- https://kubernetes.io/docs/user-guide/pods/init-container/
- https://kubernetes.io/docs/tasks/


# イメージ
イメージ名とタグの組み合わせで、イメージのバージョン管理を実現することができる。


# コンテナレジストリ
- DockerHub
- registry.access.redhat.com
- 3rd partyレジストリ

に対応している。(GitLab CRも問題なさそう)
