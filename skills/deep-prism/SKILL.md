---
name: deep-prism
description: Check if a website is actually discoverable by search engines and AI. Takes a URL and runs 8 diagnostic checks to find invisible problems — things that cost you visibility but are easy to fix once you know about them. Trigger when user mentions checking site visibility, AI readability, SEO audit, "is my site visible to AI", "can ChatGPT find my site", or after completing deep-reframe messaging work.
---

# Deep Prism — 見えているつもりで、見えていない

表現を磨いても、検索エンジンやAIから見えていなければ意味がない。「知らないだけで損している」技術的な問題を8項目でチェックし、具体的な修正手順を提示する。

## このワークフローを提案するタイミング

**トリガー条件:**
- ユーザーがサイトの検索エンジン/AI検索での可視性を確認したい
- deep-reframeでメッセージングを改善した後（「メッセージングは整った。次はちゃんと見えているか確認しましょう」）
- 「ChatGPTに出てこない」「Perplexityで引用されない」「AI検索で見つからない」
- サイトのSEO/AI対応状況を知りたい

**最初の提案:**
「サイトのURLを教えてください。JSを実行せずにHTMLを取得し、robots.txt、構造化データ、メタ情報など8項目を自動チェックします。数分で終わります。」

---

## チェックの実行

### 入力

URLを1つ受け取る（トップページ推奨。サービスページ等でもOK）。

### 実行手順

URLを受け取ったら、以下のデータを収集する。可能な限り並列で実行する。

1. **HTML取得**: WebFetchでURLのHTMLを取得（JSを実行しないので、クローラーと同じ条件で見える）
2. **robots.txt取得**: `{origin}/robots.txt` を取得
3. **サイトマップ取得**: `{origin}/sitemap.xml` を取得
4. **CDN/WAFチェック**: Bashで以下を実行し、通常アクセスとAIボットアクセスのレスポンスを比較

```bash
# 通常アクセス
curl -s -o /dev/null -w "%{http_code}" -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36" "{url}"

# AIボットとしてアクセス
curl -s -o /dev/null -w "%{http_code}" -A "PerplexityBot" "{url}"
curl -s -o /dev/null -w "%{http_code}" -A "OAI-SearchBot" "{url}"
```

5. **Bingインデックス確認**: WebSearchで `site:{domain}` をBingで検索

データが揃ったら、8項目を順にチェックする。

---

## 8つのチェック項目

### 1. SPA（Single Page Application）で中身が空

AI検索エンジン（Perplexity, ChatGPT Search等）はJavaScriptを実行しない。SPAだと `<div id="root"></div>` しか返らず、サイトが存在しないのと同じ。

**チェック方法**: WebFetchで取得したHTMLのbody内テキストコンテンツの量を確認。

**判定**:
- OK: テキストコンテンツが十分に含まれている（見出し、段落、リスト等がHTMLに直接記述されている）
- NG: body内がほぼ空、または `<div id="root"></div>`, `<div id="app"></div>` のみ
- 参考: WordPress、Webflow、Hugo、Jekyll等の静的サイトジェネレータはSSR/SSGなので通常この問題は起きない。React/Vue/AngularのCSR（Client-Side Rendering）が対象

**NGの場合の修正手順**:
- SSR/SSGへの切り替え。Next.js/Nuxtなら設定変更レベル
- 「GooglebotはJSを実行するのでGoogle検索では問題に見えませんが、AI検索エンジンはJSを実行しません。CSRのみのSPAは50〜80%のコンテンツがAIに見えていません」

### 2. robots.txt がAI検索ボットをブロック

訓練用ボット（GPTBot, ClaudeBot等）のブロックは問題ないが、検索用ボット（OAI-SearchBot, PerplexityBot等）までブロックするとAI検索に出なくなる。

**チェック方法**: robots.txtの内容を解析し、以下のAI検索ボットに対するDisallowルールを確認。

| User-Agent | 所有者 | 用途 |
|---|---|---|
| OAI-SearchBot | OpenAI | AI検索 |
| ChatGPT-User | OpenAI | ChatGPTブラウジング |
| Claude-SearchBot | Anthropic | AI検索 |
| PerplexityBot | Perplexity | AI検索 |

**判定**:
- OK: robots.txtが存在しない、または上記の検索用ボットがブロックされていない
- NG: 上記ボットに対する `Disallow: /` がある、または `User-agent: *` で全体ブロック
- 注意: GPTBot, ClaudeBot, Google-Extended等の訓練用ボットのブロックは問題ない。検索用と訓練用を区別して報告する

**NGの場合の修正手順**:
- robots.txtに検索用ボットの許可ルールを追加（数行、5分）
- 具体的な追記内容を提示する

### 3. Schema.org 構造化データがない

Organization, Product, FAQ, Article等のJSON-LDマークアップがないと、AIがサイトの内容を正しく理解・引用しにくい。

**チェック方法**: HTML内の `<script type="application/ld+json">` を検索。

**判定**:
- OK: JSON-LDが存在し、Organization・WebSite等の基本的な型が含まれている
- 部分的: JSON-LDはあるが型が不足（例: Organizationはあるが製品情報がない）
- NG: JSON-LDが存在しない

**NGの場合の修正手順**:
- HTMLのheadにJSON-LDのscriptタグを追加。HTML構造の変更不要
- サイトに合った最低限のJSON-LDコードを具体的に生成して提示する
- 「GPT-4の回答精度が構造化データありで16%→54%に向上したデータがあります」

### 4. メタタグ・OGPが雑または未設定

title, description, og:title, og:description等が空・自動生成のまま。AIがコンテンツの要約・引用の根拠に使う情報が不足する。

**チェック方法**: HTMLのhead要素から以下を抽出。

- `<title>`
- `<meta name="description">`
- `<meta property="og:title">`
- `<meta property="og:description">`
- `<meta name="author">`
- `<meta property="article:published_time">`

**判定**:
- OK: 各ページに固有のtitleとdescriptionが設定されている
- NG: 空、サイト名だけ、自動生成のまま（例: "Home | Company Name" のみでdescriptionなし）

**NGの場合の修正手順**:
- 各ページのメタ情報を手動設定、またはCMS/フレームワークのテンプレートで自動生成ルールを設定
- サイトの内容に基づいた具体的なtitle/descriptionの案を提示する

### 5. コンテンツがAI抽出しにくい構造

AIは40〜60語の自己完結したパッセージを抽出して引用する。見出しのない長文の連続だと、引用される「塊」が作れない。

**チェック方法**: HTMLの見出し階層（H1〜H3）の有無・構造、テキストブロックの長さを分析。

**判定**:
- OK: H2/H3で論理的にセクション分けされ、各セクションが自己完結している
- NG: H1のみ、または見出しなしで長文が続く。1つのセクションに複数トピックが混在

**NGの場合の修正手順**:
- H2/H3の階層構造、箇条書き、FAQ形式の導入を提案
- 具体的にどこで区切るとよいか、サイトの内容を見て提案する
- 「Q&A形式のコンテンツはAI引用率が40%向上するデータがあります」

### 6. CDN/WAF がAIボットをデフォルトブロック

Cloudflareが2025年7月に全新規ドメインでAIクローラーをデフォルトブロックに変更。サイト管理者が何もしていないのに、AIから見えなくなっている可能性がある。

**チェック方法**: 事前に実行したcurlの結果を比較。

**判定**:
- OK: 通常アクセスとAIボットアクセスで同じレスポンス（200 OK）
- NG: AIボットだけ403・503・CAPTCHAが返る
- 参考: レスポンスヘッダに `cf-ray` があればCloudflare経由。Cloudflareを使っていない場合はこの問題は起きにくい

**NGの場合の修正手順**:
- Cloudflareダッシュボード → Security → Bots でAI検索ボットのブロックを解除（トグル1つ）

### 7. Bing Webmaster Toolsに未登録

ChatGPT SearchはBingインデックスを基盤にしている。Bingに載っていなければChatGPTの回答に出てこない。

**チェック方法**: WebSearchで `site:{domain}` をBingで検索した結果を確認。

**判定**:
- OK: Bingの検索結果にサイトのページが表示される
- NG: 結果がゼロ、または極端に少ない
- 注意: Googleにインデックスされていても、Bingには載っていないケースがある

**NGの場合の修正手順**:
- Bing Webmaster Toolsに登録してサイトを認証（15分）
- 「Bing Webmaster ToolsにはAI Performance Reportがあり、Copilotでの引用状況も確認できます」

### 8. XMLサイトマップが未提出

サイトマップがなければ、特にリンクの少ない小規模サイトはクローラーがページを発見できない。

**チェック方法**: `/sitemap.xml` のレスポンスと、robots.txt内の `Sitemap:` 行を確認。

**判定**:
- OK: サイトマップが存在し、robots.txtで参照されている
- 部分的: サイトマップは存在するが、robots.txtで参照されていない（または逆）
- NG: サイトマップが存在しない

**NGの場合の修正手順**:
- サイトマップを生成してrobots.txtで参照を追加し、Google Search Console・Bing Webmaster Toolsに提出（30分）
- 使用しているCMS/フレームワークに応じた具体的な生成方法を案内する

---

## レポートの出力

全8項目のチェック結果をまとめてレポートとして提示する。

### レポート形式

```
## Deep Prism チェック結果: {domain}

### サマリー

| # | 項目 | 結果 | 修正の手間 |
|---|---|---|---|
| 1 | SPA空コンテンツ | OK / NG | - / 中 |
| 2 | robots.txt | OK / NG | - / 5分 |
| ... | ... | ... | ... |

{OK数}/8 項目クリア。{NG数}件の改善ポイントがあります。

### 改善ポイントの詳細

（NGの項目のみ、上記の「NGの場合の修正手順」に従って詳細を記述）
```

### レポートのトーン

- 「問題がある」ではなく「ここを直すとこう良くなる」
- 修正の手間を明示する（5分、30分、数時間 等）。手間が小さいものから着手を勧める
- 全項目OKの場合は「技術的な可視性は問題ありません。メッセージングの内容に集中できます」

### 追加の提案

レポート出力後、以下を提案する:

- NGが多い場合: 「修正の手間が小さいものから順に直すのがおすすめです。robots.txt（5分）→ サイトマップ（30分）→ ...」
- 全項目OK + deep-reframeを使っていない場合: 「技術的な可視性は問題ありません。次はメッセージングの内容を確認しませんか？」とdeep-reframeを提案
- deep-reframeから来た場合: 「メッセージングと可視性の両方が整いました」

---

## 運用上の注意

**チェックの限界を伝える:**
- 項目6（CDN/WAF）はcurlのUser-Agent変更で確認するが、高度なボット検出は再現できない場合がある
- 項目7（Bingインデックス）はWeb検索結果からの推定であり、正確な状態はBing Webmaster Toolsでしか確認できない
- チェック結果は現時点のスナップショットであり、CDN設定の変更等で状態が変わる可能性がある

**やりすぎない:**
- 8項目以外の最適化提案はしない（ページ速度、モバイル対応等は対象外）
- 修正の実装自体は手伝えるが、ユーザーが求めない限り勝手に始めない
- 「全部直さなきゃ」というプレッシャーを与えない。効果の大きいものから1つずつ

**データの引用:**
- チェック項目の根拠となるデータを説明に含める場合は、調査レポートの数字を使う
- ただしレポート全体が冗長にならないよう、NGの項目でのみ引用する

**背景知識の参照:**
- ユーザーから「なぜAI可視性が重要か」「AI検索はどう動いているか」と聞かれたら、`references/background.md` を読んで回答する
- NGの項目について「なぜ今まで気づかなかったか」を説明する際も `references/background.md` の「なぜ気づかない」セクションを参照する
- 定量データやソースの詳細も `references/background.md` にまとめてある
