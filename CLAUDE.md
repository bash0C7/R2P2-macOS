## このリポジトリ

`R2P2-macOS` ＝ Darwin native (CoreBluetooth はじめ Apple framework) を mrbgem として統合した
picoruby を build / run するための staging 用 harness。具体的な責務は 3 つ:

1. `rake check` で macOS の build 前提 (Xcode CLT / brew openssl@3 / Swift) を verify
2. Darwin-targeted build config (`build_config/r2p2-picoruby-darwin.rb`) を保持。picoruby 命名規約
   (`r2p2-<runtime>-<target>.rb`、pico2 等と同列) に沿う
3. 薄い rake wrapper として `vendor/picoruby/` への fetch + `MRUBY_BUILD_DIR=./build` redirect で
   fetched source を pristine に保ちながら build / run

依存する picoruby は `PICORUBY_REPO` / `PICORUBY_REF` で切替可能 (default: upstream
`picoruby/picoruby` master)。Darwin port を含む build をするには Darwin port を抱える picoruby
tree (例: `bash0C7/picoruby` の `picoruby-ble-darwin-port` branch) を指して
`MRUBY_CONFIG=$(pwd)/build_config/r2p2-picoruby-darwin.rb rake build`。

Darwin port の実装 (`mrbgems/picoruby-ble/ports/darwin/`) と verification scaffold
(`mrbgems/picoruby-ble/ports/darwin/test/`, `README.md`) は picoruby tree 側に住む。本 repo は
build wrapper + 前提 check + 暫定 build config の格納場所。R2P2-ESP32 が ESP-IDF という別建て
build system を picoruby に接続する harness として恒久的に必要なのとは対照的に、こちらは
upstream merge までの transitional。

## 行動

- **足る情報が揃ったら即行動する**。会話で確立済みの事実の再導出、決定済み事項の再審議、採らない選択肢の説明をしない。選択に迷うなら推奨を 1 つ示す
- **タスクが要求する以上のことをしない**: 機能追加・refactor・抽象化・将来要件への設計・起こり得ない事象への error handling / fallback / validation を足さない。動く最も単純なものを書く。検証は system boundary（user 入力・外部 API）のみ
- **user 確認で止まるのは 3 条件のみ**: 破壊的/不可逆な操作・実質的な scope 変更・user にしか提供できない入力。turn の末尾が計画・質問・未実行の約束なら、いま tool call で実行してから終える
- user が問題の記述・質問・思考の言語化をしている時の成果物は assessment。**修正は依頼されてから**

## 報告

- **進捗・完了の主張は当 session の tool result で裏付けてから報告する**。未検証は未検証と明言。test 失敗は出力ごと報告。skip した step は skip と言う
- **outcome を先頭に**: 最初の 1 文が「何が起きたか / 何が見つかったか」
- **最終 message は作業を見ていない読者への re-grounding** として書く

## memory

- 1 知見 1 ファイル、先頭に 1 行 summary。修正指摘も確認済みアプローチも理由とともに記録。repo や履歴が既に記録するものは保存しない。重複を作らず既存を更新し、誤りと判明したら削除
