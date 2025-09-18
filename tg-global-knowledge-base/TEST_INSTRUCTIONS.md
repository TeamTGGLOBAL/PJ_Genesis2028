# CI/CD テスト手順書

## 概要
このドキュメントは、CI/CDワークフローのテスト手順を説明します。

## テスト用ワークフロー
- **ファイル**: `.github/workflows/test-branch-merge.yml`
- **機能**: test-* ブランチからの自動マージ

## テスト手順

### ステップ1: 現在の変更をコミット・プッシュ

```bash
# 現在のディレクトリに移動
cd PJ_Genesis2028/tg-global-knowledge-base

# 変更をステージング
git add .

# コミット
git commit -m "feat: Add test workflow and test content"

# developブランチにプッシュ
git push origin develop
```

### ステップ2: テストブランチを作成

```bash
# テストブランチを作成・切り替え
git checkout -b test-auto-merge-001

# テスト用の変更を加える
echo "## テスト変更" >> test-content.md
echo "この変更は自動マージのテスト用です。" >> test-content.md

# 変更をコミット
git add test-content.md
git commit -m "test: Add test change for auto merge"

# テストブランチにプッシュ
git push origin test-auto-merge-001
```

### ステップ3: 自動実行の確認

1. **GitHub Actionsの確認**
   - GitHubリポジトリの「Actions」タブを開く
   - 「Test Branch Auto Merge」ワークフローが実行されることを確認

2. **プルリクエストの確認**
   - 自動的にdevelopブランチへのプルリクエストが作成されることを確認
   - プルリクエストの内容を確認

3. **自動マージの確認**
   - プルリクエストが自動的にマージされることを確認
   - テストブランチが削除されることを確認

### ステップ4: 結果の確認

```bash
# developブランチに切り替え
git checkout develop

# 最新の変更を取得
git pull origin develop

# テストファイルの内容を確認
cat test-content.md
```

## 期待される結果

1. ✅ テストブランチが作成される
2. ✅ プッシュ時にGitHub Actionsが実行される
3. ✅ developブランチへのプルリクエストが自動作成される
4. ✅ プルリクエストが自動的にマージされる
5. ✅ テストブランチが削除される
6. ✅ developブランチに変更が反映される

## トラブルシューティング

### 問題1: ワークフローが実行されない
**原因**: ブランチ名が `test-*` パターンに一致しない
**解決策**: ブランチ名を `test-` で始まるようにする

### 問題2: プルリクエストが作成されない
**原因**: 権限不足またはワークフローのエラー
**解決策**: GitHub Actionsのログを確認

### 問題3: 自動マージが失敗する
**原因**: プルリクエストの作成タイミングの問題
**解決策**: ワークフローの待機時間を調整

## テスト完了後のクリーンアップ

```bash
# テストファイルを削除
git rm test-content.md
git commit -m "cleanup: Remove test content"
git push origin develop
```

## 次のステップ

テストが成功したら：
1. より複雑なワークフローのテスト
2. 本番用ワークフローの実装
3. エラーハンドリングの追加
