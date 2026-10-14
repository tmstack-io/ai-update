# ai-update

PC上に存在するAIエージェントのハーネス（CLI）を検出し、各ハーネス自身の更新コマンドで一括更新するbashスクリプト。macOS（標準のbash 3.2を含む）とLinuxで動く。

## 使い方

```sh
ai-update               # 更新対象のうちPATH上にあるものをすべて更新
ai-update codex grok    # 指定したハーネスだけ更新
ai-update --list        # 更新対象と現在のバージョンを表示（更新しない）
ai-update --discover    # PC上にあるが更新対象に入っていないエージェントを表示（更新しない）
ai-update --add         # --discover で見つかったエージェントを対話で更新対象に加える
```

`--discover` / `--add` は [vercel-labs/skills](https://github.com/vercel-labs/skills) のエージェント定義（`src/agents.ts`）を取得してNodeで実行し、各エージェントの設定ディレクトリの有無で検出する。IDEや、削除済みで設定ディレクトリだけ残っているものも含まれる。node（型除去に対応した22.18以降）が必要。

## 導入

```sh
ln -s ~/Development/repos/ai/ai-update/ai-update ~/.local/bin/ai-update
```

## 更新対象

`harnesses.tsv` に1行1ハーネス（タブ区切りで「コマンド名・vercel-labs/skills のエージェント名・更新コマンドの引数」）で記載し、上から順に更新する。`ai-update --add` で対話的に追記するか、直接編集する。

`ai-update --add` では、検出したエージェントごとに次の3つから選ぶ。

- 加える: 実行コマンド名と更新引数を入力して `harnesses.tsv` に追記する。更新引数は `--help` の update / upgrade 行から推定した値を既定値として提示する。
- 今回はスキップ: 何も記録しない。次回も候補に出る。
- 今後表示しない: `ignored.txt` に記録し、以後の `--discover` / `--add` で表示しない。

ai-update は「コマンド名 更新引数」を実行して更新するため、自分で更新する機能を持たないCLI（npm等で入れて自己更新サブコマンドが無いもの）は扱えない。
