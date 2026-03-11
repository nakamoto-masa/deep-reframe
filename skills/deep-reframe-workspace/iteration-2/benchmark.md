# Benchmark: iteration-2 (Writer sub-agent) vs iteration-1 (single-agent)

## Summary

| Configuration | Mean Score | 5点 | 4点 | 3点 | Total |
|---|---|---|---|---|---|
| **iteration-2** (Writer sub-agent) | **4.19** | 6 | 18 | 1 | 25 |
| iteration-1 (single-agent) | 4.08 | 2 | 23 | 0 | 25 |
| **Delta** | **+0.11** | **+4** | **-5** | **+1** | |

## Per-Eval Comparison

| Eval | iteration-2 | iteration-1 | Delta |
|---|---|---|---|
| biotech-tagline | **4.44** (40/45) | 4.0 (36/45) | **+0.44** |
| materials-casestudy | 3.875 (31/40) | **4.1** (33/40) | **-0.225** |
| robotics-landing | **4.25** (34/40) | 4.1 (33/40) | **+0.15** |

## 5点到達項目 (iteration-2)

- biotech-tagline: 事業内容の伝達力, Phase 3の表現力, 方向性の質的差異, バズワード排除
- robotics-landing: 3層メッセージングの統合度, フレームワーク活用度

## 回帰分析

### materials-casestudy: 3点「技術手法名・テンプレート表現の排除」

方向性の見出しに「Category Design」「Raskin的パラダイムシフト」「Dunford的価値訴求」とフレームワーク名がそのまま露出。Writer sub-agentがWriting Brief中のフレームワーク名を成果物に反映してしまった。

**修正案**: writer.mdに「フレームワーク名・理論名は成果物の見出しや本文に露出させない」指示を追加。

## Timing (iteration-2, Writer sub-agent only)

| Eval | Tokens | Duration |
|---|---|---|
| biotech-tagline | 19,142 | 63s |
| materials-casestudy | 18,995 | 57s |
| robotics-landing | 18,910 | 74s |
| **Mean** | **19,016** | **65s** |

## Assessment

- 全体: 微増（+0.11）。5点項目が2→6と増加し、上位品質は改善
- 改善: biotech-taglineで顕著（+0.44）。「探す→設計する」の対比構造がPhase 3として高評価
- 回帰: materials-casestudyで-0.225。フレームワーク名漏出が原因で対処可能
- 注意: n=1のため、アーキテクチャ効果と偶然の分散を区別できない
