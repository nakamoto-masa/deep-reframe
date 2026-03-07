# deep-reframe

ディープテック企業の技術メッセージングを「技術中心」から「価値中心」に変換する Claude Code Skill。

## 何をするか

リポジトリ・サイト・技術メモなど素材を渡すと、対話的にガイドしながら4ステージで書き換えます。

1. **ヒアリング** — 素材を受け取り、質問しながらコンテキストを集める
2. **診断** — メッセージングの成熟度（Phase 1〜4）と問題パターンを特定
3. **変換** — Dunford / Raskin / Category Design 等のフレームワークで書き換え案を複数提示
4. **検証** — Sub-agentで読者テストを実行し、伝わるか確認

技術者は「合ってる/違う」を判断するだけ。コードレビューと同じ感覚で使えます。

## インストール

Claude Code内で以下を実行:

```
/plugin marketplace add nakamoto-masa/deep-reframe
/plugin install deep-reframe@deep-reframe
```

または、ローカルで試す場合:

```bash
git clone https://github.com/nakamoto-masa/deep-reframe.git
claude --plugin-dir ./deep-reframe
```

インストール後、「サイトの見せ方を改善したい」「技術メモから外向けコンテンツを作りたい」などと伝えると、このSkillが起動します。

## 想定ユーザー

- 技術力はあるが「見せ方」に課題を感じているディープテック企業の技術者
- ウェブサイトや資料のテキストが技術説明に偏っていて、顧客価値が伝わっていないと感じている人

## 背景にある知見

- April Dunford「Obviously Awesome」— ポジショニング5要素
- Andy Raskin — Strategic Narrative
- Category Design（Play Bigger）
- 3層メッセージング（経営層 / マネージャー / 現場エンジニア）
- TREW Marketing — エンジニア向けコンテンツマーケティング

## ライセンス

MIT
