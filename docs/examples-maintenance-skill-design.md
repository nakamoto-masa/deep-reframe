# examples.mdメンテナンススキルの設計

Issue #4

## 背景と目的

examples.mdはdeep-reframeのStage 3（生成・変換）で変換の方向性・粒度の基準として参照される。現状の問題:

- **ドメイン偏り**: CAE/シミュレーション領域の1案件から派生した架空の例のみ
- **演繹的**: フレームワークの理論から生成しており、実際に読者に刺さったかの検証がない
- **品質の天井**: 例の質が「この程度でOK」の暗黙の基準として機能する

これを継続的に品質管理・拡充できるスキルを作る。

## 責務

| | deep-reframe | examples.mdメンテナンス |
|---|---|---|
| 役割 | ユーザーの素材からテキストを仕上げる | 良い例を調査・検証・追加する |
| トリガー | 「見せ方を変えたい」 | 「リファレンスを更新したい」「例を増やしたい」 |
| 対話相手 | テキストの依頼者 | スキルの開発者（自分自身） |
| 使うツール | AskUserQuestion, 生成 | WebSearch, WebFetch, Read, Write, Edit |

## ワークフロー

3つのモードを持つ。ユーザーが指定するか、状況から判断する。

### モード1: 品質監査（Audit）

現行examples.mdの全例をeval基準でチェックし、問題のある例を特定・改善する。

```
1. examples.mdを読み込む
2. references.mdのeval基準（理解度・アクション誘発・Phase判定・3層カバレッジ・構造的品質）を読み込む
3. セクション1〜8の架空例: After側のテキストを基準でチェック
   - 変換のポイントが明確か
   - After側がPhase 3的な語り方になっているか
   - 顧客主語になっているか
   - バズワード・自己宣言が混入していないか
4. セクション9の実在事例: 事実の正確性をWeb検索で検証
5. 結果をレポートとして提示:
   - PASSした例
   - FAILした例（理由と改善案）
   - カバレッジの偏り（ドメイン・変換パターン）
6. ユーザー確認後、examples.mdを更新
```

### モード2: 実例調査（Research）

メッセージング改善に成功したディープテック企業の実例を調査し、examples.mdに追加する。

```
1. 調査対象ドメインを決定（ユーザー指定 or カバレッジの薄い領域を提案）
2. 候補企業を特定（後述の検索戦略を使用）
3. 各企業のBefore/Afterを収集
4. 調査結果をユーザーに提示:
   - 企業名・領域
   - Before/After（ソース付き）
   - 変換パターン（セクション1〜8のどれに該当するか、または新パターンか）
5. ユーザー確認後、examples.mdのセクション9に追加
```

**調査手順（検証済み）:**

バイオ/創薬・素材/MI・ロボティクスの3ドメインで実際に調査を実行し、18社のBefore/Afterを収集できた手順。

候補企業の特定:
- `"[ドメイン] startup series B"` — シリーズB以降の企業はメッセージングが成熟している傾向
- 業界メディア（TechCrunch, MIT Tech Review, The Robot Report, BioCentury等）の企業紹介記事

初期のメッセージング（Before）の取得:
- `"[企業名] series A announcement"` / `"[企業名] launch"` でプレスリリース（BusinessWire等）や初期報道を検索
- SEC filing（S-1）に創業時の自己記述が残っている場合がある
- VCの投資発表記事にも初期のポジショニングが引用されている

現在のメッセージング（After）の取得:
- 公式サイトのタグライン・ミッションページをWebFetchで取得

**有効でなかったアプローチ:**
- Wayback Machineは使わなくても初期報道・プレスリリースで十分取れた
- Crunchbaseは企業の基本情報確認には使えるが、メッセージング自体は取れない

### モード3: ドメイン拡充（Expand） — TBD

特定ドメインの架空例を追加するモード。モード2で実例が見つからない場合の補完として想定。

```
1. 拡充するドメインとセクション（変換パターン）を決定
2. references.mdのフレームワークに基づいてBefore/After例を生成
3. eval基準でセルフチェック
4. ユーザーに提示して確認
5. examples.mdに追加
```

**このモードを残すかはモード2の実例調査の結果で判断する。** 実例が十分見つかれば不要。見つからないドメインがある場合も、架空例で埋めるべきかどうか自体が判断対象（架空例はフレームワークからの演繹でしかなく、実際に読者に刺さったかの検証がない。例がないよりましとは限らず、質の低い例は品質の天井になりうる）。

## トリガー

```
description: Audit, research, and expand the examples in deep-reframe's references/examples.md.
Trigger when user mentions updating examples, auditing reference quality, adding new domains
to examples, researching real company messaging changes, or maintaining deep-reframe references.
```

## 共通参照するファイル

| ファイル | 用途 |
|---|---|
| `deep-reframe/references/references.md` | eval基準（診断基準・フレームワーク）の参照元 |
| `deep-reframe/references/examples.md` | 読み書きの対象 |
| `deep-reframe/evals/evals.json` | ルーブリック評価基準の参照（After側テキストの品質チェック用） |

## ファイル配置

```
skills/examples-maintenance/
├── SKILL.md          # ワークフロー（3モード）
```

references/やevals/は持たない。deep-reframeのものを共通参照する。

## 設計判断

### なぜsub-agentではなくスキルか

- deep-reframeのsub-agent（Researcher, Writer, Reviewer）はテキスト生成ワークフローの一部
- examples.mdのメンテナンスは独立したタスク。トリガーも対話相手も異なる
- スキルとして独立させることで、SKILL.mdの責務が混ざらない

### eval基準の流用方法

evals.jsonのルーブリックはテキスト生成全体を評価する基準（タグライン+会社説明のセット等）。examples.mdの個々のBefore/After行に直接適用するには粒度が合わない。

代わりに、references.mdの診断基準（Phase判定シグナルワード・問題パターン）を使う:

- After側にPhase 1-2シグナルワードが主要訴求軸として残っていないか
- After側に問題パターン（技術が主語、自己宣言型等）が残っていないか
- 変換のポイントが明確で、生成時の方向性ガイドとして機能するか
