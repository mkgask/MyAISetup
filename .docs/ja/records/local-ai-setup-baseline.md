# 決定記録: local-ai-setup-baseline

## メタデータ
- 作成日: 2026-10-27
- 範囲: Ryzen AI Max+ 395を対象とするローカルAIセットアップスクリプトの初期サポート範囲とインストール制約。

## 注記
- このファイルは追記専用の議論履歴です。
- 可変の追跡情報（ステータス、残作業、未処理アクション）は追加しません。
- 未解決の質問一覧は残しません。確認が必要な場合はチャットで質問し、解決した事実を追記します。
- 実装を拘束する事実は DECISIONS.yml に昇格します。
- 各記録は可能な限り簡潔にします。
- 根拠や昇格の詳細情報は、明確さを損なわない範囲で省略できます。

記録の境界:
- ひとつの記録は、まとまりのある一つの決定テーマに限定します。独立した質問、コンポーネント、後続作業が生じた場合は、無関係な履歴を継ぎ足さず、新しい `discussion-id` で記録を開始します。前の記録を継続する場合は、新しい記録の `Evidence / references` に記載します。
- レビューしやすさの目安として、記録が約10件または1,000語に達した場合は自然な区切りで閉じ、新しい `discussion-id` の記録へ続けます。これは厳密な制限ではなく、次の同テーマの記録が簡潔でレビュー可能な場合に限り継続します。

追記ルール:
- 末尾にのみ追記し、既存の記述は編集しません。
- ステータスや残作業の追跡情報は追加しません。

## 記録一覧

### 記録 0001 (2026-10-27)
- 今回記録する理由: テンプレート状態の決定一覧をプロジェクト固有の制約に置き換えるための初回議論です。ユーザーは今回を議論フェーズに限定しているため、決定の昇格や実装は行いません。
- 調査結果 / トレードオフ:
  - ルートの `DECISIONS.yml` はテンプレートのままで、プロジェクトレベルの唯一の正規決定一覧として記載されています。インストーラー、テストスクリプト、過去の議論記録、スコープ付き決定ファイルの規則は見つかりませんでした。
  - ユーザーはUbuntu 24.04 LTSとRyzen AI Max+ 395を対象とし、llama.cpp、Ollama、vLLM、Stable Diffusion WebUIを1つ、ComfyUIを指定しました。`install.sh` は引数なしで全セットアップを実行し、`--help` でUsageを表示します。リモートからの `curl | bash` では、ローカルコンポーネントを実行元のプロジェクトディレクトリ内にインストールします。ROCmとOS依存関係には `sudo` を使用できますが、AIアプリケーションはユーザー所有とします。互換性のある既存バージョンは維持し、不適合なバージョンを無断で変更しません。
  - 確認事項: Pythonバージョンはアプリごとに選定します。`proto` があれば `proto`、`mise` があれば `mise` を使い、必要に応じてアプリ別のPython/PyTorch環境を作成します。両方またはいずれも存在しない場合の扱いは、検証時に確定します。バージョンを固定し、既存環境を破壊しないインストールとします。
  - AMDの現行ROCm 10.1.0互換表には、Ryzen AI Max+ 395（`gfx1151`）とUbuntu 24.04.5、HWEカーネル7.0が記載されています。これはROCmのハードウェア/OS対応であり、vLLM wheelの互換性を示すものではありません。Ryzen向けAMD PyTorch手順では、ROCm 7.2.1、PyTorch 2.9.1、Python 3.12の組み合わせをproduction構成として記載し、カーネル6.14-1018以降を要求しています。文書とバージョンの対象範囲が異なるため、固定前にホストランタイムとカーネルを照合する必要があります。
  - vLLMのstableインストール資料では、ROCm 7.0 wheelはvLLM 0.14–0.18向け、ROCm 7.2.1 wheelはnightlyのみです。Ryzen AI MaxのサポートにはROCm 7.0.2以降が必要です。最新のvLLM開発者向け資料にはROCm 10.0 nightly版が記載されていますが、10.1 wheelはありません。vLLMはwheelにPyTorchを同梱し、他のROCm/PyTorchビルドとのバイナリ非互換を避けるため、新しいPython環境を推奨しています。異なるROCmバージョンを使う場合はソースからビルドするよう案内しています。`ROCm/vllm` リポジトリは廃止済みとし、上流の `vllm-project/vllm` を案内しています。
  - OllamaのAMD対応資料にはRyzen AI Max+ 395が記載され、LinuxではROCm v7が必要とされていますが、ROCm 10.1との正確な組み合わせは検証が必要です。ComfyUIはLinux上のAMD GPUを含む手動インストールを案内しています。
  - 追加WebUIの候補としてSD.Nextを推奨します。公式ドキュメントはROCmバックエンドを明示的にサポートし、Python 3.12を推奨しています。また、上流リポジトリは2026-10-26に更新されていました。Forge Neoも活発ですが、プロジェクトページではAMD GPUの問題をサポート対象外としており、この対象環境には適合性が低いと判断しました。SD.Nextが `gfx1151` で検証できなければ、ComfyUIのみを残す選択肢があります。
- 現時点の結論: まだ拘束力のない候補方針です。ROCm/OS依存関係はホストに `sudo` でインストールし、AIアプリとアプリ別Python/PyTorch環境は実行元プロジェクト内のユーザー所有とします。検証済みバージョンを固定し、互換性のある既存環境は維持します。画像生成向けにはSD.NextとComfyUIを候補とします。選択したROCmに適合するstable vLLM wheelがない場合、upstream vLLMの固定コミットをソースビルドし、ビルドとGPUスモークテストを検証できた場合に限り利用します。`install.test.sh` は `sudo` や実際のシステムインストールを行わずにテストする想定です。正確なバージョン、Python管理ツールの優先順位/失敗条件、vLLMのビルド対象はdiscussion-validationで確定します。
- DECISIONS.ymlへの昇格: なし
- 根拠 / 参考資料:
  - [AMD ROCm 10.1.0互換表](https://rocm.docs.amd.com/en/latest/compatibility/compatibility-matrix.html)
  - [AMD Ryzen向けPyTorchインストール](https://rocm.docs.amd.com/projects/radeon-ryzen/en/latest/docs/install/installryz/native_linux/install-pytorch.html)
  - [vLLM stable GPUインストール](https://docs.vllm.ai/en/stable/getting_started/installation/gpu/) と [最新の開発者向けGPUインストール](https://docs.vllm.ai/en/latest/getting_started/installation/gpu/)
  - [Ollama GPU対応](https://docs.ollama.com/gpu)、[ComfyUI](https://github.com/Comfy-Org/ComfyUI)、[SD.Nextインストール](https://vladmandic.github.io/sdnext-docs/Installation/)
  - [Forge Neo](https://github.com/Haoming02/sd-webui-forge-classic/tree/neo) と [廃止済みROCm/vLLMリポジトリ](https://github.com/ROCm/vllm)

## 追記テンプレート（末尾にコピーして追記）

### 記録 {next-sequence} ({timestamp})
- 今回記録する理由:
- 調査結果 / トレードオフ:
- 現時点の結論:
- DECISIONS.ymlへの昇格:
- 根拠 / 参考資料（任意）:

### 記録 0002 (2026-10-27)
- 今回記録する理由: discussion-validationの前に、ユーザーがランタイムのバージョン方針を補足し、追加する画像生成UIとしてSD.Nextを承認しました。
- 調査結果 / トレードオフ: Ubuntu 24.04 LTSの範囲で、Ryzen AI Max+ 395に対応し、選定したアプリ群とともに検証できる最も新しいROCm 10.xを優先します。これまでの「固定バージョン・非破壊」方針は維持し、インストールごとに変動するlatestを追うのではなく、検証後に正確なバージョンを固定します。選択したROCmに適したstable vLLM wheelがなければ、ビルドとGPUスモークテストを条件として、上流の固定コミットをソースビルドする案を優先します。SD.Nextは承認され、その他のスコープ変更はありません。検証対象を、共通ホストROCmとアプリのランタイム境界、アプリごとのPython/PyTorch分離、Ubuntu 24.04のカーネル要件、vLLMのビルド経路に絞ります。他のOSリリースとモデルのダウンロードは対象外です。
- 現時点の結論: 候補方針を、Ubuntu 24.04 LTS内で検証可能な最も新しいROCm 10.xを優先し、正確なバージョンを固定して無断更新しない形に修正します。対象はllama.cpp、Ollama、vLLM、SD.Next、ComfyUIの5つを維持します。ROCm SDKのハードウェア対応だけでアプリ互換性が保証されると仮定せず、アプリが独自ランタイムまたはソースビルドを必要とするか検証します。
- DECISIONS.ymlへの昇格: なし
- 根拠 / 参考資料（任意）: 本議論でのユーザー補足。互換性の根拠は記録0001を参照。

### 記録 0003 (2026-10-27)
- 今回記録する理由: 事前昇格監査で、単一のホストROCmユーザー空間版では全アプリを満たせない可能性が見つかり、ユーザーが分離を許容する範囲を明確にしました。
- 調査結果 / トレードオフ: ユーザーは、アプリごとに異なるROCmユーザー空間ランタイムを必要に応じて使うことを、アプリ別Python/PyTorchに加えて許容しました。これにより、ホストのROCmドライバー/ランタイムは対応する最新10.xを優先しつつ、アプリごとに互換性のあるユーザー空間構成を固定できます。ただし、ホストドライバーが各アプリランタイムをサポートする必要があり、ROCm SDKのハードウェア対応表だけではその互換性を判断できません。
- 現時点の結論: Ubuntu 24.04 LTSの対応ホストROCm/ドライバーは検証済みの最新10.xを優先し、必要な場合は固定したアプリ別ROCmユーザー空間ランタイムを許可する候補方針とします。検証済みの経路がない場合、アプリを無断で省略したりホストROCmをダウングレードしたりしません。
- DECISIONS.ymlへの昇格: なし
- 根拠 / 参考資料（任意）: 本議論でのユーザー補足。AMD ROCmインストール資料では、システム全体とPython仮想環境の両方のインストール範囲が案内されています。

### 記録 0004 (2026-10-27)
- 今回記録する理由: ユーザーがROCm 10.x優先、アプリ別ROCmユーザー空間、SD.Nextの採用を明確にしたため、昇格前の議論検証を実施しました。
- 調査結果 / トレードオフ:
  - プロジェクトの決定ベースラインと、Ubuntu/Ryzenハードウェア対応表、AMD PyTorch、vLLMのstableおよび開発者向けwheel、OllamaのAMD対応、llama.cppのHIPビルド、SD.NextのROCm/Python対応、ComfyUIのAMD対応を確認しました。限定した調査範囲で5つすべての対象アプリとインストーラー/ランタイム境界を含めており、既存の実装やテストはありませんでした。
  - 焦点の絞り込みは妥当です。ホストのドライバー/OS対応と、アプリ別ROCmユーザー空間、PyTorch、wheelの対応は区別する必要があります。SD.NextはROCm対応を明記しているため画像WebUIの候補として維持し、ComfyUIも対象とします。他のOSリリースとモデルのダウンロードは範囲外です。
  - 方針は当初の目的に適合しています。5アプリすべてを対象に維持し、Ubuntu 24.04 LTS上で検証できる最新のROCm 10.xを優先し、バージョンを固定し、必要ならアプリ別のROCm/Python/PyTorch環境を許容します。適切なstable wheelがないvLLMは上流の固定コミットをソースビルドする方針です。これはバージョン選択方針であり、全アプリがROCm 10.1に既に対応しているという主張ではありません。
  - 現行の制約との矛盾はありません。プロジェクトレベルの `DECISIONS.yml` はテンプレート例のみで、正規の決定一覧として定義されています。プロジェクト固有の拘束条件は `DECISIONS.yml` に置き、記録には理由を残します。`AGENTS.md` の追記は索引のみです。
  - 実装リスクは明示されていますが、方向性を妨げるものではありません。AMDのハードウェア対応表だけではアプリ互換性を証明できず、vLLMの現行wheel一覧には10.1がなく、Ollama資料はROCm v7、Ryzen向けPyTorch手順は7.2.1、llama.cppは `gfx1151`/10.1を保証しない一般的なHIP経路を示しています。アプリ別ユーザー空間ランタイムはホストドライバーとの互換性を確認し、インストール前に検証する必要があります。対象アプリに検証済み経路がなければ、アプリを無断で省略したりホストをダウングレードしたりせず、明示的に報告します。
- 現時点の結論: **議論の方向性はPASS**です。具体的なパッケージ版と実行可能な互換性は実装時に検証します。昇格候補は次のとおりです。`platform-001-supported-host`（Ryzen AI Max+ 395とUbuntu 24.04 LTS）、`platform-002-rocm-version-policy`（検証済み最新10.xをホストに採用、版を固定、アプリ別ROCmユーザー空間を許可、互換性エラーを明示）、`runtime-001-per-app-python-pytorch`（既存の `proto` / `mise` を使うアプリ別バージョン）、`applications-001-supported-ai-stack`（llama.cpp、Ollama、vLLM、SD.Next、ComfyUI）、`installation-001-installer-contract`（引数なしのセットアップ、`--help`、リモート実行、ローカル配置、権限境界、非破壊性）、`testing-001-install-test`（システムインストールの副作用なしでテスト）。この検証段階では決定を昇格しません。
- DECISIONS.ymlへの昇格: なし
- 根拠 / 参考資料（任意）:
  - [llama.cpp HIPビルド資料](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
  - [ROCm 10.1インストール方法](https://rocm.docs.amd.com/en/latest/install/rocm.html)
  - AMD、vLLM、Ollama、SD.Next、ComfyUI、Forgeの資料は記録0001を参照。
