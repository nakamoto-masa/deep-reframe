---
name: examples-maintenance
description: Audit, research, and expand the examples in deep-reframe's references/examples.md. Trigger when user mentions updating examples, auditing reference quality, adding new domains to examples, researching real company messaging changes, or maintaining deep-reframe references.
---

# Examples Maintenance — examples.mdの品質管理と拡充

deep-reframeスキルが参照するexamples.md（変換パターン例）を継続的に品質管理・拡充するスキル。

## このワークフローを提案するタイミング

**トリガー条件:**
- 「リファレンスを更新したい」「例を増やしたい」
- 「examples.mdの品質をチェックしたい」
- 「新しいドメインの例を追加したい」
- 「実在企業のメッセージング変遷を調べたい」

## モード選択

3つのモードがある。ユーザーが指定するか、リクエストの内容から判断する。

1. **品質監査（Audit）** — 現行の例をeval基準でチェック
2. **実例調査（Research）** — 実在企業のメッセージング変遷を調査して追加
3. **ドメイン拡充（Expand）** — 特定ドメインの架空例を追加（モード2で実例が見つからない場合の補完）

---

## モード1: 品質監査（Audit）

現行examples.mdの全例を診断基準でチェックし、問題のある例を特定・改善する。

### 手順

1. `skills/deep-reframe/references/examples.md` を読み込む
2. `skills/deep-reframe/references/references.md` の診断基準を読み込む
3. 各例をチェック:

**架空例のチェック基準:**
- **Phase判定**: After側にPhase 1-2シグナルワード（「高速」「削減」「効率化」「短縮」等）が主要訴求軸として残っていないか
- **問題パターン**: After側に問題パターン（技術が主語、自己宣言型、形容詞依存等）が残っていないか
- **変換のポイント**: 明確で、生成時の方向性ガイドとして機能するか
- **顧客主語**: 顧客の成果・体験・能力が主語になっているか

4. 結果をレポートとして提示:
   - PASSした例
   - FAILした例（理由と、改善案 or 削除の推薦）
   - カバレッジの偏り（ドメイン・変換パターン）
5. ユーザー確認後、examples.mdを更新（改善・削除・構造変更を含む）

---

## モード2: 実例調査（Research）

メッセージング改善に成功したディープテック企業の実例を調査し、examples.mdに追加する。

### 手順

1. `examples.md` を読んで現状の構造とカバレッジを把握する
2. 調査対象ドメインを決定（ユーザー指定 or カバレッジの薄い領域を提案）
3. 候補企業を特定
4. 各企業のBefore（初期）/ After（現在）のメッセージングを収集
5. 調査結果をユーザーに提示:
   - 企業名・領域
   - Before/After（ソース付き）
   - 既存の変換パターンのどれに該当するか、または新パターンか
6. ユーザー確認後、examples.mdの既存の構造に合わせて追加

### 調査手順

**候補企業の特定:**
- `"[ドメイン] startup series B"` で検索 — シリーズB以降の企業はメッセージングが成熟している傾向
- 業界メディア（TechCrunch, MIT Tech Review, The Robot Report, BioCentury等）の企業紹介記事

**初期のメッセージング（Before）の取得:**
- `"[企業名] series A announcement"` / `"[企業名] launch"` でプレスリリース（BusinessWire等）や初期報道を検索
- SEC filing（S-1）に創業時の自己記述が残っている場合がある
- VCの投資発表記事にも初期のポジショニングが引用されている

**現在のメッセージング（After）の取得:**
- 公式サイトのタグライン・ミッションページをWebFetchで取得

---

## モード3: ドメイン拡充（Expand）

特定ドメインの架空例を追加する。モード2で実例が見つからない場合の補完。

### 手順

1. `examples.md` を読んで現状の構造とカバレッジを把握する
2. 拡充するドメインと変換パターンを決定
3. `skills/deep-reframe/references/references.md` のフレームワークに基づいてBefore/After例を生成
4. 診断基準でセルフチェック（モード1のチェック基準を適用）
5. ユーザーに提示して確認
6. examples.mdの既存の構造に合わせて追加

### 注意

架空例はフレームワークからの演繹であり、実際に読者に刺さったかの検証がない。モード2（実例）を優先すること。

---

## 参照ファイル

このスキルは自前のreferences/を持たない。deep-reframeのファイルを共通参照する:

| ファイル | 用途 |
|---|---|
| `skills/deep-reframe/references/references.md` | 診断基準（Phase判定シグナルワード・問題パターン）の参照元 |
| `skills/deep-reframe/references/examples.md` | 読み書きの対象 |
| `skills/deep-reframe/evals/evals.json` | ルーブリック評価基準の参照 |
| `docs/examples-research-data.md` | 調査済みの実例データ（18社） |
