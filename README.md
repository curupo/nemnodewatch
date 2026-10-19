# NIS1 Node Watch

NEM(NIS1)ネットワークのノードを選んで、状態をまとめて監視できる軽量なブラウザ用ダッシュボードです。
`index.html` 1ファイルだけで動作し、ビルドやサーバーサイドの処理は不要です。

## 特徴

- 最大 **50 ノード**を選択して一覧表示(PC はテーブル、スマホはカード表示)
- **60 秒ごとに自動更新**、「Refresh」ボタンで手動更新も可能
- ノードを選ぶ画面で、ホスト名・ノード名によるフィルタが可能
- 選択したノードは **Cookie に保存**(有効期限 1 年)され、次回アクセス時に復元
- ライト / ダークテーマに自動対応(OS の設定に追従)

## 表示項目

| 項目 | 内容 |
| --- | --- |
| Hostname | ノードのホスト名と、nodewatch に登録されているノード名 |
| HTTPS | HTTPS(TLS)で接続できるかを示す鍵アイコン(緑 = 利用可、灰 = 不可 / 未確認) |
| Block height | ブロック高。監視中の最大値より 4 以上遅れているノードは赤字で強調 |
| Balance | ノードのアカウント残高 |
| Version | NIS のバージョン |
| Java | Java のメジャーバージョン |
| OS | ノードの OS 名 |
| Supernode | NEM Supernode Program に active で登録されていれば `○`、そうでなければ `×` |
| Delegation slots (max / free) | 委任ハーベスト(unlock)枠の最大数と空き数 |
| Uptime | ノードの稼働時間(`1y 2mo 3d 4h 5m 6s` 形式。年 = 365 日、月 = 30 日で概算) |

## 使い方

1. リポジトリを取得します。

   ```bash
   git clone https://github.com/curupo/nemnodewatch.git
   cd nemnodewatch
   ```

2. `index.html` をブラウザで開きます。ローカルサーバーを使う場合の例:

   ```bash
   python3 -m http.server 8000
   # http://localhost:8000/ を開く
   ```

   GitHub Pages や任意の静的ホスティングにそのまま置いても動作します。

3. 画面右上の **Select Nodes** から監視したいノードにチェックを入れ、**Save** を押します。

> Tailwind CSS と Alpine.js を CDN から読み込むため、インターネット接続が必要です。

## データ取得元

| 用途 | 取得先 |
| --- | --- |
| ノード一覧(ブラウザの選択候補、高さ、残高、バージョン) | `https://nodewatch.symbol.tools/api/nem/nodes` |
| Supernode 登録状況 | `https://nem.io/supernode/api/nodes?status=active`(100 件ずつページング) |
| 最新のブロック高 | 各ノードの `GET /chain/height` |
| Java / OS / 稼働時間 | 各ノードの `GET /node/extended-info` |
| 委任枠 | 各ノードの `POST /account/unlocked/info` |
| HTTPS 確認 | `https://<host>:7891/chain/height` へ TLS 接続できるかを確認 |

各ノードへのリクエストにはタイムアウト(5〜8 秒)を設定しています。
ノード自身から高さを取得できない場合は、nodewatch の値で代用します。

## 注意事項

- 各ノードへのアクセスはブラウザから直接行うため、**CORS を許可していないノードでは** Block height 以外(Java / OS / Uptime / 委任枠など)が `-` と表示されることがあります。
- HTTPS 確認は `no-cors` で TLS 接続の成否のみを見ています。クライアント証明書を要求する `super-nem.love` は、証明書選択ダイアログが出てしまうため確認を省略しています(アイコンは「未確認」表示)。
- 選択内容は Cookie(`nis1_watch_nodes`)に保存されます。ブラウザの Cookie を削除すると選択も消えます。

## 使用技術

- HTML / JavaScript(単一ファイル)
- [Alpine.js](https://alpinejs.dev/) 3.14.8
- [Tailwind CSS](https://tailwindcss.com/) 3.4.16(CDN 版)

## ファイル構成

```
.
├── index.html   # アプリ本体(UI とロジックのすべて)
└── README.md
```
