# TG Global Knowledge Base CI/CD プロセス マニュアル

## 概要

本ドキュメントは、TG Global Knowledge BaseプロジェクトにおけるCI/CD（継続的インテグレーション・継続的デプロイ）プロセスの実装と運用について説明します。

## 目次

1. [CI/CDとは](#cicdとは)
2. [プロジェクト構成](#プロジェクト構成)
3. [実装ステップ](#実装ステップ)
4. [ワークフロー詳細](#ワークフロー詳細)
5. [運用ガイド](#運用ガイド)
6. [トラブルシューティング](#トラブルシューティング)

## CI/CDとは

### 基本概念

**CI（Continuous Integration）** = 継続的インテグレーション
- コードの変更を頻繁に統合
- 自動テストの実行
- 品質の早期発見
- 開発チームの協力促進

**CD（Continuous Deployment/Delivery）** = 継続的デプロイ/デリバリー
- 自動的に本番環境にデプロイ
- 手動デプロイのリスクを削減
- 迅速なリリースサイクル

### メリット

1. **時間短縮**: 手動作業の削減
2. **一貫性**: 同じプロセスで毎回実行
3. **品質向上**: 自動チェックによる品質保証
4. **エラー削減**: 人的ミスの防止
5. **チーム効率**: 開発者の集中力向上

## プロジェクト構成

```
tg-global-knowledge-base/
├── .github/
│   └── workflows/
│       ├── auto-pr.yml              # プルリクエスト自動作成
│       ├── content-quality-check.yml # コンテンツ品質チェック
│       ├── auto-review-request.yml  # 自動レビュー依頼
│       └── auto-merge.yml           # 自動マージ（条件付き）
├── tg-global-knowledge-base/
│   ├── 01_ECONOMICS/
│   ├── 02_PSYCHOLOGY/
│   └── 03_SOCIOLOGY/
├── manual_CICDprocess.md            # 本マニュアル
└── README.md
```

## 実装ステップ

### ステップ1: 基本的なプルリクエスト自動作成 ✅

**目的**: developブランチへのプッシュ時に、自動的にmainブランチへのプルリクエストを作成

**実装ファイル**: `.github/workflows/auto-pr.yml`

**機能**:
- developブランチへのプッシュをトリガー
- 自動的にプルリクエストを作成
- レビュー依頼テンプレートを自動挿入

**使用方法**:
1. developブランチで変更をコミット・プッシュ
2. GitHub Actionsが自動実行
3. mainブランチへのプルリクエストが自動作成

### ステップ2: コンテンツ品質チェック（予定）

**目的**: Markdownファイルの品質を自動チェック

**予定機能**:
- Markdownの構文チェック
- スペルチェック
- リンクの有効性チェック
- 画像の存在確認

### ステップ3: 自動レビュー依頼（予定）

**目的**: プルリクエスト作成時に自動的にレビュアーを指定

**予定機能**:
- 特定のレビュアーへの自動依頼
- レビュー期限の設定
- リマインダーの送信

### ステップ4: 自動マージ（予定）

**目的**: 条件を満たした場合の自動マージ

**予定機能**:
- レビュー承認後の自動マージ
- テスト通過後の自動マージ
- 特定のブランチのみ対象

## ワークフロー詳細

### 1. プルリクエスト自動作成ワークフロー

```yaml
name: Auto Create Pull Request

on:
  push:
    branches: [ develop ]

jobs:
  create-pr:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          
      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v5
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          commit-message: "feat: Update from develop branch"
          title: "feat: Add TG Global Knowledge Base updates"
          body: |
            ## レビュー依頼
            
            **レビュー担当者各位**
            
            経済学の基本概念「機会費用」について、更新されたコンテンツをレビューしてください。
            
            ### ＜レビューで確認してほしい観点＞
            - **明確さ**: 説明は分かりやすいか？専門用語が多すぎないか？
            - **具体性**: TG GLOBALの事業に即した具体例は、現実的で示唆に富んでいるか？
            - **網羅性**: この概念を理解する上で、他に加えるべき重要な視点はあるか？
            
            GitHubのコメント機能で、ご意見や修正案をお知らせください。
          base: main
          head: develop
          draft: false
```

**トリガー条件**:
- developブランチへのプッシュ

**実行内容**:
1. リポジトリをチェックアウト
2. mainブランチへのプルリクエストを作成
3. レビュー依頼テンプレートを自動挿入

## 運用ガイド

### 基本的なワークフロー

1. **開発作業**
   ```bash
   git checkout develop
   # ファイルを編集
   git add .
   git commit -m "feat: 新しいコンテンツを追加"
   git push origin develop
   ```

2. **自動実行**
   - GitHub Actionsが自動実行
   - プルリクエストが自動作成

3. **レビュー・マージ**
   - チームメンバーがレビュー
   - 承認後にマージ

### 設定のカスタマイズ

#### レビュー依頼テンプレートの変更

`.github/workflows/auto-pr.yml`の`body`セクションを編集：

```yaml
body: |
  ## カスタムレビュー依頼
  
  あなたのカスタムテキストをここに記入
```

#### トリガー条件の変更

```yaml
on:
  push:
    branches: [ develop, feature/* ]  # 複数ブランチに対応
  pull_request:
    types: [opened, synchronize]      # プルリクエストイベントも追加
```

## トラブルシューティング

### よくある問題

#### 1. ワークフローが実行されない

**原因**: ブランチ名の不一致
**解決策**: `.github/workflows/auto-pr.yml`の`branches`設定を確認

#### 2. プルリクエストが作成されない

**原因**: 権限不足
**解決策**: `GITHUB_TOKEN`の権限を確認

#### 3. 重複するプルリクエストが作成される

**原因**: 既存のプルリクエストの存在
**解決策**: 既存のプルリクエストをクローズしてから再実行

### ログの確認方法

1. GitHubリポジトリの「Actions」タブを開く
2. 実行されたワークフローをクリック
3. 各ステップのログを確認

### デバッグのコツ

1. **ワークフローファイルの構文チェック**
   - YAMLの構文エラーがないか確認
   - インデントが正しいか確認

2. **権限の確認**
   - `GITHUB_TOKEN`の権限を確認
   - リポジトリの設定を確認

3. **段階的なテスト**
   - 小さな変更から始める
   - 各ステップを個別にテスト

## 今後の拡張予定

### フェーズ2: 品質チェックの自動化
- Markdownの構文チェック
- スペルチェック
- リンクの有効性チェック

### フェーズ3: 高度な自動化
- 自動レビュー依頼
- 条件付き自動マージ
- リリースノートの自動生成

### フェーズ4: 分析・レポート
- コンテンツの品質メトリクス
- レビュー時間の分析
- チームの生産性分析

## 参考資料

- [GitHub Actions 公式ドキュメント](https://docs.github.com/ja/actions)
- [peter-evans/create-pull-request](https://github.com/peter-evans/create-pull-request)
- [CI/CD ベストプラクティス](https://docs.github.com/ja/actions/learn-github-actions)

---

**最終更新**: 2025年1月
**バージョン**: 1.0.0
**作成者**: TG Global Knowledge Base Team
