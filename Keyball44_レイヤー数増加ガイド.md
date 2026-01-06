# Keyball44 レイヤー数増加ガイド（簡易版）

## このガイドについて

QMK環境を構築せずに、Keyball44キーボードのレイヤー数を増やす方法を解説します。
元記事: https://zenn.dev/yamavel/articles/4d68ba3c8f01c4

---

## 前提知識

### レイヤーとは？
- キーボードの「階層」のようなもの
- 例: 通常の文字入力＝レイヤー0、記号や数字＝レイヤー1、など
- デフォルトは4レイヤーですが、これを増やすことができます

### なぜQMK環境なしでやるのか？
- QMK環境の構築には数十GBのストレージが必要
- GitHub Actionsを使えば、ローカルでのビルド環境構築が不要

---

## 必要なもの

1. GitHubアカウント
2. Git（ローカル環境）
3. Keyball44本体（左右両方）

---

## 手順の全体像

```
1. 公式ファームウェアを書き込む（必要な場合のみ）
   ↓
2. GitHubでリポジトリをforkする
   ↓
3. ローカルにcloneする
   ↓
4. 自分用のキーマップディレクトリを作成
   ↓
5. レイヤー数を設定ファイルに記載
   ↓
6. GitHub Actionsの設定を修正
   ↓
7. 変更をpushする
   ↓
8. 自動ビルドされたファームウェアをダウンロード
   ↓
9. Keyball44に書き込む
```

---

## 詳細手順

### ステップ1: 公式ファームウェアの書き込み（必要な場合のみ）

**すでにKeyball44を正常に使用している場合は、このステップをスキップしてステップ2に進んでください。**

最終的にステップ7でカスタムファームウェアを書き込むため、事前の書き込みは不要です。

**このステップが必要なケース**:
- 新品のKeyball44で初めてファームウェアを書き込む場合
- キーボードが正常に動作しない場合（リカバリー目的）

**手順**:
1. [REMAP](https://remap-keys.app/)にアクセス
2. Keyball44を接続
3. 公式ファームウェアを書き込む（左右両方）

---

### ステップ2: GitHubでリポジトリをfork

1. [Keyball公式リポジトリ](https://github.com/Yowkees/keyball)にアクセス
2. 右上の「Fork」ボタンをクリック
3. 自分のGitHubアカウントにリポジトリがコピーされます

---

### ステップ3: ローカルにclone

Forkしたリポジトリをローカル環境にコピーします。

```bash
git clone https://github.com/あなたのユーザー名/keyball.git
cd keyball
```

**補足**: `あなたのユーザー名`の部分は、自分のGitHubユーザー名に置き換えてください。

---

### ステップ4: 自分用のキーマップディレクトリを作成

**作業場所**: `qmk_firmware/keyboards/keyball/keyball44/keymaps/`

1. `via`ディレクトリをコピー
2. 新しい名前をつける（例: `my_keymap`）

**なぜviaをコピーするのか？**
→ REMAPで編集できるようにするため

---

### ステップ5: レイヤー数を設定

**編集ファイル**: `自分のkeymapディレクトリ/config.h`

以下の行を追加:

```c
#define DYNAMIC_KEYMAP_LAYER_COUNT 8
```

**説明**:
- `8`の部分が設定したいレイヤー数
- 好きな数字に変更できます（例: 6, 10など）

---

### ステップ6: GitHub Actionsの設定修正

**編集ファイル**: `.github/workflows/build-firmware.yml`

**追加位置**: `Checkout qmk_firmware`ステップの直後

以下のステップを追加:

```yaml
- name: Install Python dependencies
  run: |
    python3 -m pip install --upgrade pip
    python3 -m pip install appdirs
```

**説明**:
- そのままだと`appdirs`が足りないというエラーが発生する
- pipを最新化してから`appdirs`をインストールすることで解決

---

### ステップ7: 変更をpush

ローカルでの編集が完了したら、変更をGitHubにpushします。

```bash
git add .
git commit -m "Add custom keymap with increased layers"
git push origin main
```

pushが完了すると、GitHub Actionsが自動的にビルドを開始します。

---

### ステップ8: ファームウェアのダウンロード

1. GitHub Actionsが自動的にビルドを開始
2. 完了したら「Artifacts」からHEXファイルをダウンロード

**ファイル名の例**: `keyball_keyball44_my_keymap.hex`

---

### ステップ9: Keyball44に書き込む

1. [ProMicro Web Updater](https://sekigon-gonnoc.github.io/promicro-web-updater/)にアクセス
2. ダウンロードしたHEXファイルを選択
3. **左右両方**のキーボードに書き込む

**重要**: 必ず左右両方に書き込んでください

---

## トラブルシューティング

### トラックボールが認識しない
→ 左右両方のキーボードにファームウェアを書き込んだか確認

### GitHub Actionsでビルドが失敗する
→ `appdirs`のインストールステップを追加したか確認

### REMAPで編集できない
→ `via`ディレクトリをベースにkeymapを作成したか確認

---

## まとめ

この方法の利点:
- QMK環境の構築不要（ストレージ節約）
- GitHub Actionsで自動ビルド

必要なスキル:
- 基本的なGit操作（clone, commit, push）

注意点:
- 左右両方のファームウェア書き込みを忘れずに
- `via`ベースのkeymapでREMAP互換性を維持

---

## 参考リンク

- [元記事（Zenn）](https://zenn.dev/yamavel/articles/4d68ba3c8f01c4)
- [Keyball公式リポジトリ](https://github.com/Yowkees/keyball)
- [REMAP](https://remap-keys.app/)
- [ProMicro Web Updater](https://sekigon-gonnoc.github.io/promicro-web-updater/)
