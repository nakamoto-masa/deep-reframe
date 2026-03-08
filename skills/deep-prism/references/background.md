# Deep Prism — 背景知識

チェック項目の背景にある市場動向・技術メカニズム・定量データをまとめた参照資料。SKILL.mdの実行手順には含めない情報を、ユーザーへの説明や質問対応に使う。

## なぜディープテックに特に重要か

- エンタープライズ技術バイヤーの47%がAIアシスタントでベンダー調査を開始（Google検索の43%を超えた）
- B2B回答者の60%がChatGPT/Geminiで初期ベンダーリストを作成
- AI経由のコンバージョン率はGoogle検索の4〜9倍（ChatGPT 15.9%, Perplexity 10.5% vs Google 1.76%）
- 技術者の92%がAIツールを業務で使用 → ディープテックは顧客がAIで探す確率が高い
- ニッチな技術領域ほどキーワード検索より自然言語での探索が適している

Sources:
- AI Discovery is Reshaping the B2B Buying Journey https://blogs.workfx.ai/2026/01/19/ai-discovery-is-reshaping-the-b2b-buying-journey-the-missing-piece-of-tech-companies-gtm/
- Generative AI begins to eclipse traditional search in B2B vendor discovery https://www.digitalcommerce360.com/2025/10/15/generative-ai-traditional-search-b2b-vendor-discovery/
- ChatGPT vs. Perplexity vs. Google AI Mode: B2B SaaS Citation Benchmarks 2026 https://www.averi.ai/how-to/chatgpt-vs.-perplexity-vs.-google-ai-mode-the-b2b-saas-citation-benchmarks-report-(2026)
- Stack Overflow 2025 Developer Survey - AI https://survey.stackoverflow.co/2025/ai

## AI検索エンジンの引用メカニズム

### 各プラットフォームのデータソースとJS対応

| プラットフォーム | データソース | JSレンダリング |
|---|---|---|
| Perplexity | 独自インデックス（200億URL）+ リアルタイム取得 | しない |
| ChatGPT Search | Bingインデックス + OpenAI独自インデックス | しない |
| Google AI Overviews | Googleインデックス | する（Googlebot経由） |

### 引用されやすいコンテンツの特徴

- 構造化されたコンテンツ（見出し・箇条書き・表）は引用率が28〜40%高い
- トピックの深さとオリジナルデータがある
- 高トラフィックサイトはAI引用が3倍（SE Ranking、230万ページ調査）

### ニッチ技術コンテンツにとっての重要な知見

- 従来のGoogle検索順位とAI引用はほぼ無関係 — AI引用されたURLの80%はGoogle上位100位にすら入っていない
- Perplexityがニッチ技術に最も有利 — ニッチソースが引用の24%（全プラットフォーム中最高）
- ChatGPTはニッチに弱い — Wikipedia・大手メディアに偏重（Wikipediaが引用の7.8%）
- ChatGPTとPerplexityの引用ドメインの重複は11%のみ — プラットフォームごとに引用基準が大きく異なる

Sources:
- AI Platform Citation Patterns - Profound https://www.tryprofound.com/blog/ai-platform-citation-patterns
- B2B SaaS Citation Benchmarks Report 2026 - Averi https://www.averi.ai/how-to/chatgpt-vs.-perplexity-vs.-google-ai-mode-the-b2b-saas-citation-benchmarks-report-(2026)
- Google AI Overview Citations Drop from Top-10 - Ahrefs https://ahrefs.com/blog/ai-overview-citations-top-10/
- Perplexity Crawlers Documentation https://docs.perplexity.ai/guides/bots
- How OpenAI Crawls and Indexes Your Website - Daydream https://www.withdaydream.com/library/how-openai-crawls-and-indexes-your-website

## 各チェック項目の「なぜ気づかない」

チェック結果がNGだったとき、ユーザーに「なぜ今まで気づかなかったか」を説明するための情報。

### 1. SPA（Single Page Application）で中身が空
GooglebotはJSを実行するのでGoogle検索では問題に見えない。サイトは普通に表示されるし、Googleにも出ている。

### 2. robots.txt がAI検索ボットをブロック
従来のSEOツールはAIクローラーのブロックをチェックしない。Google検索には影響しないので、ブロックしていても気づく手段がない。

### 3. Schema.org 構造化データがない
構造化データなしでも従来のSEO順位には影響しない（Google公式に確認済み）。「SEOのおまじない」程度の認識で、効果を実感する機会がない。

### 4. メタタグ・OGPが雑または未設定
サイトは普通に表示される。SNSシェアも「なんとなく動いている」ので、メタ情報の不備に気づく機会がない。

### 5. コンテンツがAI抽出しにくい構造
人間は文脈を補って読めるので問題に見えない。デザインがきれいなら「ちゃんとしたサイト」に見える。

### 6. CDN/WAF がAIボットをデフォルトブロック
セキュリティ設定を変えた覚えがない。サイトは普通に動いている。Google検索にも出る。CDNのデフォルト設定が変わったことを知る手段がない。

### 7. Bing Webmaster Toolsに未登録
Google Search Consoleすら設定していない技術者チームが多い。Bingは存在すら知らない。「Googleに出てるから大丈夫」と思っている。

### 8. XMLサイトマップが未提出
サイトが少数ページしかなければ「全部リンクで辿れるから不要」と思っている。実際にはクローラーの発見効率に大きく影響する。

## 各チェック項目の定量データ

### 1. SPA
- seoClarity調査：CSRのみのSPAは50〜80%のコンテンツがAIに見えない
- Vercel計測：GPTBot, ClaudeBot, AppleBot, PerplexityBot合計で月13億リクエスト（Googlebotの28%相当）
- GPTBotはJSファイルを11.5%のリクエストで取得するが実行はしない

Sources:
- The Rise of the AI Crawler - Vercel https://vercel.com/blog/the-rise-of-the-ai-crawler
- How JavaScript-Heavy Sites Perform in LLM Retrieval - SingleGrain https://www.singlegrain.com/artificial-intelligence/how-javascript-heavy-sites-perform-in-llm-retrieval/
- AI Crawlers & JavaScript Rendering - SearchViu https://www.searchviu.com/en/ai-crawlers-javascript-rendering/
- seoClarity Bot Optimizer https://www.seoclarity.net/blog/bot-optimizer-ai-visibility-webinar

### 2. robots.txt
Sources:
- Robots.txt Strategy 2026: Managing AI & Traditional Crawlers https://witscode.com/blogs/robots-txt-strategy-2026-managing-ai-crawlers/
- AI Bots and Robots.txt - Paul Calvano https://paulcalvano.com/2025-08-21-ai-bots-and-robots-txt/
- Anthropic clarifies how Claude bots crawl sites https://searchengineland.com/anthropic-claude-bots-470171

### 3. Schema.org 構造化データ
- GPT-4の回答精度が構造化データありで16% → 54%に向上（Data World調査）

Sources:
- How Structured Data Schema Transforms Your AI Search Visibility in 2026 https://medium.com/@vicki-larson/how-structured-data-schema-transforms-your-ai-search-visibility-in-2026-9e968313b2d7
- Schema and AI Overviews - Search Engine Land https://searchengineland.com/schema-ai-overviews-structured-data-visibility-462353

### 4. メタタグ・OGP
- Perplexityは回答の97%でソースを引用。メタ情報がないと引用対象から外れやすい

Sources:
- Meta Tags That AI Actually Reads https://linknabber.com/guides/meta-tags
- AI Platform Citation Patterns https://www.tryprofound.com/blog/ai-platform-citation-patterns

### 5. コンテンツ構造
- Q&A形式のコンテンツはAI引用率が40%向上

Sources:
- LLM Content Optimization Best Practices 2026 https://fibr.ai/geo/llm-content-optimization-best-practices-2026
- AI Search Optimization: How to Get Cited https://sapt.ai/insights/ai-search-optimization-complete-guide-chatgpt-perplexity-citations

### 6. CDN/WAF
Sources:
- Cloudflare Blocks AI Crawlers by Default https://llmrefs.com/blog/cloudflare-blocks-ai-crawlers
- How to ensure Cloudflare is not blocking AI bots https://www.indexlab.ai/blog/how-to-ensure-cloudflare-not-blocking-ai-bots-october-2025-demo
- Why Isn't My Site Being Crawled by AI? - Goodie https://higoodie.com/blog/why-isnt-my-site-being-crawled-indexed-cloudflare-blockage

### 7. Bing Webmaster Tools
- ChatGPT SearchのGoogleアラインメントは12%→33%に上昇しているが、依然としてBingが主要ソース
- Bing Webmaster Toolsには「AI Performance Report」があり、Copilotでの引用状況を確認できる

Sources:
- Why Bing Webmaster Tools Is Your Secret Weapon for AI Search Visibility https://www.blogseo.io/blog/bing-webmaster-tools-ai-search-visibility-2025
- Bing Webmaster Tools AI Performance Report Guide https://almcorp.com/blog/bing-webmaster-tools-ai-performance-report-guide/

### 8. XMLサイトマップ
- IndexNowプロトコル（Bing対応）でコンテンツ更新を即時通知 → AI検索パイプラインに反映される

Sources:
- How to Rank on Perplexity 2026 https://aiclicks.io/blog/how-to-rank-on-perplexity
- Optimizing Content for AI Search Answers - Microsoft https://about.ads.microsoft.com/en/blog/post/october-2025/optimizing-your-content-for-inclusion-in-ai-search-answers

## 訓練データ収集ボットについて

GPTBot, ClaudeBot, Google-Extended等の訓練データ収集用ボットもある。訓練データに入ればモデルが自社を「知る」可能性はあるが、ニッチなディープテック企業はWeb上の情報量自体が少ないため、訓練データに入っても影響は限定的。AI検索はリアルタイムのページ取得が主なので、検索ボットの許可の方がはるかに重要。
