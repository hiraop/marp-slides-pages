---
marp: true
theme: default
paginate: true
backgroundColor: #fff
header: "情報過多にサヨナラ！RSSHubで作る自分だけの情報収集システム"
footer: "社内LT 2025/04/08"
style: |
  section {
    font-family: 'Helvetica Neue', Arial, sans-serif;
  }
  h1 {
    color: #2c3e50;
  }
  h2 {
    color: #3498db;
  }
  code {
    background-color: #f8f8f8;
  }
---

# 情報過多にサヨナラ！<br>RSSHubで作る自分だけの情報収集システム

#### 社内LT 2025年4月8日

---

## 自己紹介

- [発表者名]
- [所属部署/チーム]
- [役職/専門分野]
- Twitter: [@xxxxxxx]
- GitHub: [xxxxxxx]

---

## 今日のアジェンダ

1. 情報過多時代の課題
2. RSSHubとは
3. RSSHubの主な機能
4. 実装デモ
5. RSS情報の活用方法
6. まとめと実践ヒント

---

## 1. はじめに：情報過多時代の課題

![bg right:40% 80%](https://api.placeholder.com/400/320)

- **毎日膨大な情報に触れる現代のエンジニア**
  - SNS、技術ブログ、ニュース、動画...
  - 重要な情報を見逃す危険性
  - コンテキストスイッチングによる生産性低下

- **情報収集の時間が増加している問題**
  - プラットフォームごとに別々のアプリを開く非効率さ
  - アルゴリズムに左右される情報取得

---

## 情報収集の「痛み」

- **複数のプラットフォーム間の移動**
  - GitHub, Twitter, YouTube, Qiita...
  - それぞれにログイン、UI操作の違い

- **情報の分散**
  - ブックマーク、メール通知、アプリ内通知...
  - どこで何を見たか忘れてしまう

- **アルゴリズムの罠**
  - プラットフォームの思惑による情報提示
  - 本当に必要な情報が埋もれる

---

## RSSという解決策

![bg right:40% 80%](https://api.placeholder.com/400/320)

- **RSS (Really Simple Syndication)の再評価**
  - 2000年代から存在する成熟した技術
  - ウェブサイトの更新を自動的に取得
  - 「プル型」の情報収集

- **RSSのメリット**
  - プラットフォームに依存しない
  - 一箇所に情報を集約できる
  - 自分でフィルタリング可能

---

## 2. RSSHubとは

![bg right:40% 80%](https://api.placeholder.com/400/320)

- **オープンソースのRSS生成・集約ツール**
  - GitHub: [github.com/DIYgod/RSSHub](https://github.com/DIYgod/RSSHub)
  - 2万5千以上のスター数（2025年4月現在）
  - 活発なコミュニティ、頻繁なアップデート

- **最大の特徴**
  - RSS非対応のサイトもRSS化できる
  - 多様なサービスに対応（2,000種類以上）
  - 拡張性が高い（自作ルートの追加可能）

---

## RSSHubのアーキテクチャ

```
                   ┌─────────────────────┐
                   │                     │
                   │      Websites       │
                   │                     │
                   └─────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────┐
│                                                         │
│                         RSSHub                          │
│                                                         │
└─────────────────────────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────┐ ┌──────────────────┐ ┌────────────────┐
│                     │ │                  │ │                │
│    RSS Readers      │ │ Notification     │ │ Automation     │
│    (Feedly etc.)    │ │ (Slack, Discord) │ │ (IFTTT, Zapier)│
│                     │ │                  │ │                │
└─────────────────────┘ └──────────────────┘ └────────────────┘
```

---

## 3. RSSHubの主な機能

**対応サービス（一部）**

| カテゴリ | サービス例 |
|---------|-----------|
| 社会メディア | Twitter, Instagram, Facebook, Weibo |
| 開発者 | GitHub, GitLab, NPM, Docker Hub |
| ニュース | 各種ニュースサイト、ブログプラットフォーム |
| 動画 | YouTube, Bilibili, ニコニコ動画 |
| 音楽 | Spotify, Apple Music, SoundCloud |
| 学術 | arXiv, IEEE, 各大学リポジトリ |
| ショッピング | Amazon, 楽天, 価格.com |

---

## RSSHubのルート構造

- **ルート = RSSフィードのURL**

```
https://rsshub.app/:ルートパス/:パラメータ1/:パラメータ2/...
```

**例：GitHubリポジトリのIssues**
```
https://rsshub.app/github/issue/DIYgod/RSSHub
```

**例：特定のTwitterユーザーのツイート**
```
https://rsshub.app/twitter/user/nodejs
```

---

## フィルタリングとカスタマイズ

- **クエリパラメータを使ったフィルタリング**

```
https://rsshub.app/github/issue/DIYgod/RSSHub?filter=bug
```

- **フィルタリングの例**
  - キーワードでフィルタリング
  - 日付でフィルタリング
  - タグでフィルタリング
  - 複数条件の組み合わせ

---

## カスタムルートの作成

**独自のルートを作成可能**

```javascript
module.exports = {
    fetch: async (ctx) => {
        const { user } = ctx.params;
        const response = await got(`https://api.example.com/users/${user}/posts`);
        
        ctx.state.data = {
            title: `${user}の投稿`,
            link: `https://example.com/users/${user}`,
            item: response.data.map((item) => ({
                title: item.title,
                description: item.content,
                pubDate: new Date(item.date).toUTCString(),
                link: item.url,
            })),
        };
    },
};
```

---

## 4. 実装デモ：セットアップ方法

**方法1: Dockerを使う（推奨）**

```bash
# Dockerコンテナを起動
docker run -d --name rsshub -p 1200:1200 diygod/rsshub
```

**方法2: Node.jsで直接実行**

```bash
# リポジトリをクローン
git clone https://github.com/DIYgod/RSSHub.git
cd RSSHub

# 依存関係をインストール
npm install

# 起動
npm start
```

---

## 環境変数による設定

- **`.env`ファイルの作成**

```
# 基本設定
PORT=1200
NODE_ENV=production

# キャッシュ設定
CACHE_TYPE=redis
REDIS_URL=redis://localhost:6379/

# アクセス制御
ACCESS_KEY=ILoveRSS

# 外部API設定（例：Twitter API）
TWITTER_CONSUMER_KEY=xxxxxxxxxxxx
TWITTER_CONSUMER_SECRET=xxxxxxxxxxxx
```

---

## Docker Composeでの実装例

```yaml
version: '3'
services:
  rsshub:
    image: diygod/rsshub
    restart: always
    ports:
      - '1200:1200'
    environment:
      NODE_ENV: production
      CACHE_TYPE: redis
      REDIS_URL: 'redis://redis:6379/'
      PUPPETEER_WS_ENDPOINT: 'ws://browserless:3000'
    depends_on:
      - redis
      - browserless

  redis:
    image: redis:alpine
    restart: always
    volumes:
      - redis-data:/data

  browserless:
    image: browserless/chrome
    restart: always

volumes:
  redis-data:
```

---

## デモ：実際のルート例と使い方

**Qiitaのトレンド記事**
```
http://localhost:1200/qiita/trending/monthly/機械学習
```

**特定のGitHubリポジトリのリリース**
```
http://localhost:1200/github/release/kubernetes/kubernetes
```

**YouTubeチャンネルの新着動画**
```
http://localhost:1200/youtube/channel/UCJFkMNTsEtSbUBb3mnd2x_Q
```

---

## 5. RSS情報の活用方法

![bg right:40% 80%](https://api.placeholder.com/400/320)

**主なRSSリーダー**

- **クロスプラットフォーム**
  - [Feedly](https://feedly.com/)
  - [Inoreader](https://www.inoreader.com/)
  - [Tiny Tiny RSS](https://tt-rss.org/)（セルフホスト）

- **デスクトップ/モバイル専用**
  - [Reeder](https://reederapp.com/)（Mac/iOS）
  - [NetNewsWire](https://netnewswire.com/)（Mac/iOS）
  - [Thunderbird](https://www.thunderbird.net/)（クロスプラットフォーム）

---

## Slack/Discordへの通知連携

**Slackでの設定例**

1. Slackで「RSS」アプリを追加
2. チャンネルを選択してRSSフィードURLを追加

```
/feed subscribe http://localhost:1200/github/issue/DIYgod/RSSHub
```

**Discord設定例**

1. サーバー設定 → インテグレーション → ウェブフック
2. RSSボットの追加（MEE6, YAGPDB.xyzなど）
3. RSSフィードURLを登録

---

## 自動化ワークフローの例

**IFTTT（If This Then That）**

![bg right:40% 80%](https://api.placeholder.com/400/320)

- RSSフィードの新着をトリガーに設定
- アクション例：
  - Evernoteに保存
  - Gmailで通知
  - Notionデータベースに追加
  - Twitterに自動投稿

---

## Zapierを使った連携例

```
RSSHubフィード → フィルター → Notion
```

**例：特定のキーワードを含む技術記事だけNotionに保存**

1. トリガー：RSSフィードの新規アイテム
2. フィルター：タイトルか内容に「Kubernetes」を含む
3. アクション：Notionデータベースに新規エントリ作成
   - タイトル、URL、公開日、概要を保存

---

## 6. まとめと実践ヒント

![bg right:40% 80%](https://api.placeholder.com/400/320)

- **情報過多時代の生存戦略**
  - 受動的な情報収集から能動的な情報設計へ
  - 一元管理による効率化

- **RSSHubのメリット**
  - プラットフォーム横断的な情報収集
  - カスタマイズ可能な情報フィルタリング
  - 自動化によるタスク削減

---

## 明日から実践できるステップ

1. **自分に必要な情報源を洗い出す**
   - 毎日チェックしているサイト/サービスのリスト化

2. **RSSHubのデプロイ**
   - ローカルまたはクラウドサーバーへ

3. **RSSリーダーの選定と設定**
   - 使いやすいインターフェースを優先

4. **徐々に自動化を追加**
   - まずは通知、その後ワークフロー自動化

---

## 参考資料

- [RSSHub公式ドキュメント](https://docs.rsshub.app/)
- [GitHub - DIYgod/RSSHub](https://github.com/DIYgod/RSSHub)
- [Awesome-RSSHub](https://github.com/DIYgod/RSSHub/discussions/8492)（カスタムルート集）
- [情報収集を効率化するRSSの活用法](https://example.com)
- [Marpを使ったスライド作成入門](https://example.com)

---

## Q&A

ご質問をお待ちしています！

---

## ご清聴ありがとうございました

RSSHubで情報収集を効率化し、
本当に大切なことに時間を使いましょう！

- 連絡先：[your-email@example.com]
- 本発表資料：[スライド共有URL]
