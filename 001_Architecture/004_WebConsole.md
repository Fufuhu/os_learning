# 概要

Openshiftではブラウザからウェブコンソールにアクセスすることが可能。
開発者はGUIから以下の事柄を実行できる。

- プロジェクト単位で
    1. 環境の可視化
    1. 環境のブラウジング
    1. 環境の管理

が可能

UIのカスタマイズはいくらでも可能だよ。
(画像、JS、CSSは好きにいじって良い)

--public-masterオプションの指定内容または、
master configuration fileで定義したmasterPublicURLで
アクセスすることができる。

異なるホスト名などでアクセスする場合は、--cors-allowed-originsを
openshift start時に指定するか、master configuration file paramaterでcorsAllowedOrigins
を指定する必要がある。**ここは要注意！！**

# CLIのダウンロード

CLIのダウンロードはWebコンソールから行う。

#　ブラウザの対応

Chrome, FirefoxかSafari, IE
OSはWindows 8以上......???


