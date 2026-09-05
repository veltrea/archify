<p align="center">
  <a href="./README.md">English</a> · <a href="./README_ZH.md">简体中文</a> · <strong>日本語</strong>
</p>

![Archify の製品紹介画像](docs/assets/archify-readme-hero.png)

# Archify（日本語対応版）

**コードや、しくみの説明を、そのまま操作できるきれいな図に変えます。チャットの中で完結します。**

Archify は Cursor・Claude Code・Codex CLI・opencode 向けの、図の生成と検査を行う Node.js のしくみです。エージェントが型のある JSON（中間表現）を作り、Archify がそれを決まった手順で HTML と SVG に組み上げます。

> **このリポジトリについて**
> これは [tt-a1i/archify](https://github.com/tt-a1i/archify) のフォークです。**日本語の表示に対応させた版**です。本家との違いは次の 3 つだけで、それ以外の機能は本家と同じです。
> 1. `meta.locale` に **`"ja"`** を指定できます。閲覧画面の文言（ボタン・凡例・状態の表示・読み上げ用の説明）が日本語になります。
> 2. **漢字が中国語の字形にならないようにしました。** 文書の言語に応じて、日本語のフォントを先に選びます。
> 3. `archify guide` が日本語の問いかけを受け付け、日本語で答えます。
>
> 変更の詳しい内容は [日本語対応について](#日本語対応について) を読んでください。

- **開いてそのまま見せられます** — 図は 5 種類、見た目は 4 種類、明るい配色と暗い配色、主要サービスのマーク付き、動きは有限で終わります
- **取り込む前に構成の変更を確かめられます** — 検査を通った 2 つの断面を、変更前 / 差分 / 変更後として比べ、追加・削除・変更・移動・経路の付け替えを事実として並べます
- **どの操作も、書かれた内容から離れません** — 節点を探す、出どころのコードを開く（任意）、上流と下流の到達範囲や正確な経路をたどる、役割どうしを比べる、案内付きの解説を再生する。存在しないつながりを作りません
- **1 つのファイルで、そのまま渡せます** — 型のある JSON と決まった検査から、単体で開ける HTML と、PNG・SVG・WebM・1200×630 の共有画像ができます

![ライセンス](https://img.shields.io/badge/license-MIT-22c55e?style=flat-square)
![Agent Skill](https://img.shields.io/badge/Agent-Skill-7C3AED?style=flat-square)
![開発版](https://img.shields.io/badge/version-2.17.0--dev.1-0891b2?style=flat-square)

**今の開発版:** `v2.17.0-dev.1`。[変更の履歴](CHANGELOG.md#unreleased)を見てください。

```bash
npx skills add veltrea/archify -g
```

**リポジトリは要りません。** どのエージェントのチャットでも、しくみを言葉で説明するだけで図になります。

## 日本語対応について

### 1. `meta.locale` に `"ja"` を指定できます

```json
{
  "meta": {
    "locale": "ja"
  }
}
```

指定すると、次のものが日本語になります。

- 画面の操作部分（配色の切り替え・見た目・発表・書き出し・経路・全体図・役割）
- 凡例の見出しと、既定の分類名（画面側・処理側・データベース・クラウド・セキュリティ・メッセージ基盤・外部）
- 状態の表示、エラーの文、読み上げソフト向けの説明
- `<html lang>` と `<svg lang>`

**図の中に書いた文字は翻訳しません。** 部品の名前・説明・注記は、あなたが書いたとおりに出ます。

### 2. 漢字が中国語の字形になる問題を直しました

本家のフォント指定には、日本語のフォントが 1 つも入っていませんでした。並びの末尾は `Noto Sans Mono CJK SC` → `PingFang SC` → `Hiragino Sans GB` → `Microsoft YaHei` で、**すべて中国語（簡体字）用**です。`Hiragino Sans GB` は名前が似ていますが、ヒラギノの中国語版であり、日本語版の `Hiragino Sans` とは別のフォントです。

先頭の欧文フォントには漢字が入っていないため、日本語の漢字は中国語のフォントで描かれていました。「骨」「直」「海」「令」などは、日本語と中国語で字の形が違います。

この版では、文書の言語に応じてフォントの並びを切り替えます。

| 文書の `lang` | 先に使うフォント |
|---|---|
| `ja`（および中国語以外すべて） | Hiragino Sans → Hiragino Kaku Gothic ProN → Noto Sans Mono CJK JP → Noto Sans JP → Yu Gothic → Meiryo |
| `zh-CN` | Noto Sans Mono CJK SC → PingFang SC → Hiragino Sans GB → Microsoft YaHei |

明朝体（`editorial` の見た目）も同じ考え方で切り替えます。画像の書き出しでも同じ並びを使うため、PNG や SVG にした後も字形は変わりません。

### 3. `archify guide` が日本語で答えます

```bash
node archify/bin/archify.mjs guide --lang ja
node archify/bin/archify.mjs guide "リリースの流れを図にしたい"
```

言語を指定しない場合は、問いかけの文字から判別します。**ひらがなとカタカナがあれば日本語**、漢字だけなら中国語、それ以外は英語です。漢字は日本語と中国語で共通なので、かなを手がかりにしています。

## 使いはじめ

### 1. 導入する

```bash
npx skills add veltrea/archify -g
```

対話なしで Cursor に入れる場合:

```bash
npx -y skills add veltrea/archify --skill archify --agent cursor --global --copy --yes
```

導入せずに試す場合:

```bash
npx skills use veltrea/archify@archify --agent codex
```

導入先は、エージェントごとに次のとおりです。

| 使うもの | 置き場所 |
|---|---|
| **Claude Code** | `~/.claude/skills/` または `.claude/skills/` |
| **Codex CLI** | `~/.agents/skills/` または `.agents/skills/` |
| **opencode** | `~/.config/opencode/skills/`、`.opencode/skills/`、`.agents/skills/` |
| **Claude.ai** | 設定 → 機能 → スキル から `archify.zip` を上げる |

### 2. 説明から作る（リポジトリは要りません）

```text
Archify で図にしてください。ブラウザ → API → Redis のキャッシュ → PostgreSQL への代替。
meta.locale は "ja" にしてください。
```

コードを根拠にする場合は、リポジトリを開いてこう頼みます。

```text
このリポジトリを調べ、Archify で全体の構成図を作ってください。
中心となる部品を 8〜12 個、主な経路を 1 本、外部への依存、信頼の境界を示してください。
補足はカードに入れ、線を増やさないでください。meta.locale は "ja" にしてください。
```

### 3. チャットで直していく

「Redis を足して」「認証を左に移して」「切り戻しの経路を強調して」のように、続けて頼めます。Archify は元の JSON を残すので、必要な箇所だけ直せます。

## 図の種類を選ぶ

| 種類 | 向いている用途 | 指示に入れるもの |
|---|---|---|
| **構成図（architecture）** | 部品・サービス・保管先・境界 | 範囲、中心となる部品、主な経路 |
| **業務の流れ（workflow）** | CI/CD、承認、ツールの呼び出し、手順書 | 参加者、順番、分岐、例外 |
| **シーケンス（sequence）** | API の呼び出し、キャッシュの代替、認証、非同期の追跡 | 呼ぶ側、呼ばれる側、戻り、時間の関係 |
| **データの流れ（dataflow）** | 処理の連なり、出どころ、個人情報、利用者 | 取得元、変換、保管先、境界 |
| **ライフサイクル（lifecycle）** | 状態、再試行、待ち、終わりの結果 | 状態、きっかけ、再試行と取り消しの経路 |

どれが合うか分からないときは、CLI に聞けます。

```bash
node archify/bin/archify.mjs guide "Redis のキャッシュが外れたときの API の呼び出しを見せたい"
node archify/bin/archify.mjs guide "Kafka の話題・受け取り組・再処理・失敗の置き場を整理したい" --json
```

## 出来上がった図を見る・渡す

| したいこと | 操作 |
|---|---|
| 図の手引きを開く | <kbd>?</kbd> |
| 節点を探して注目する | <kbd>/</kbd> |
| 上流・下流の到達範囲をたどる | 節点に注目 → `上流` / `下流` |
| 向きのある経路をたどる | <kbd>R</kbd> または `経路` |
| 役割を 1〜2 種類比べる | <kbd>L</kbd> または `役割` |
| 全体図を開く | <kbd>M</kbd> または `全体` |
| 解説を再生する / 章を変える | <kbd>P</kbd> / <kbd>[</kbd> <kbd>]</kbd> |
| 発表モードに入る | <kbd>F</kbd> |
| 見た目を選ぶ / 配色を変える / 書き出す | <kbd>S</kbd> / <kbd>T</kbd> / <kbd>E</kbd> |
| 拡大・縮小・元に戻す | <kbd>+</kbd> / <kbd>-</kbd> / <kbd>0</kbd> |

リンクには表示の状態を持たせられます。`#focus=<id>`、`#focus=<id>&reach=upstream|downstream`、`#relation=<id>`、`#route=<起点>~<行き先>`、`#lens=<種類>~<種類>`、`#view=<表示の id>` が使えます。読み手の操作で起きる動きは必ず終わり、`prefers-reduced-motion`（動きを減らす設定）に従い、書き出した画像には入りません。

## よく使うコマンド

```bash
cd archify
node bin/archify.mjs doctor
node bin/archify.mjs demo /tmp/archify-demo
node bin/archify.mjs guide "CI の検査・承認・配布・切り戻しを見せたい"
node bin/archify.mjs validate workflow examples/agent-tool-call.workflow.json --quality showcase --json
node bin/archify.mjs preview workflow examples/agent-tool-call.workflow.json /tmp/workflow.html --quality showcase
node bin/archify.mjs deliver workflow examples/agent-tool-call.workflow.json /tmp/workflow.html --quality showcase --open --json
```

`preview` は自分の機械の中だけで動く下見の機能です。JSON を 1 つ見張り、`127.0.0.1` の空いている番号で待ち受けます。検査に失敗している間は、最後に通った図を表示したままにします。Ctrl-C で止まります。

`deliver --open` は、書き出しが終わったあとにそのファイルを開きます。開くのに失敗しても、書き出しの成功は変わりません。

失敗したときの `validate --json` と `deliver --json` は、決まった規則の番号・対象・測った値・直せる操作だけを返します。

## 上流（本家）との関係

- 本家: [tt-a1i/archify](https://github.com/tt-a1i/archify)（MIT ライセンス）
- この版の変更点は上の [日本語対応について](#日本語対応について) にまとめてあります
- 本家の更新は、随時この版に取り込みます

## ライセンス

[MIT](LICENSE)。自由に使い、変更し、配布できます。本家 tt-a1i/archify の著作権表示はそのまま残しています。
