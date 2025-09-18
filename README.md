# DevSecOps モデルケース リポジトリ

このリポジトリは、GitLab・OpenShift・Gatekeeper を組み合わせた DevSecOps モデルケースのソースコードとマニフェストをまとめたものです。
閉域環境を想定し、CI/CD パイプラインとポリシー検証を体験できる構成になっています。

## 構成

```bash
.
├── devsecops-build-project      # アプリケーションのビルド用プロジェクト
│   ├── .gitlab-ci.yml           # コンテナイメージビルド用パイプライン定義
│   ├── README.md
│   └── build
│       ├── .dockerignore
│       ├── Containerfile        # Nginx ベースのコンテナ定義
│       └── src
│           ├── html/index.html  # サンプルの Web ページ
│           └── nginx.conf       # Nginx 設定
└── devsecops-deploy-project     # アプリケーションのデプロイ用プロジェクト
    ├── .gitlab-ci.yml           # Helm を利用したデプロイ用パイプライン定義
    ├── README.md
    └── deploy
        ├── Config
        │   ├── develop-values.yaml   # 開発環境向け設定
        │   └── product-values.yaml   # 本番環境向け設定
        └── devsecops-nginx-chart     # Helm Chart
            ├── Chart.yaml
            ├── templates/            # Deployment / Service / Route 定義
            └── values.yaml
```

- devsecops-build-project
  - ソースコードとコンテナイメージのビルドを担当
  - GitLab CI/CD によりコンテナイメージをビルドし、GitLab Container Registryにpushします
- devsecops-deploy-project
  - Helmチャートと環境別設定を管理
  - GitLab CI/CD により Registryからイメージをpullし、OpenShiftにデプロイします

## ブランチ戦略
- feature/* : 開発作業用ブランチ
- develop : 開発環境向けブランチ
- main : 本番環境向けブランチ

## パイプライン概要
1. ビルドパイプライン（build-project）  
ソースコード更新をトリガーにイメージをビルドし、Registryにpush

2. デプロイパイプライン（deploy-project）  
マニフェスト更新をトリガーに Helm を用いてOpenShiftにデプロイ

## 利用方法
- devsecops-build-projectをGitLab にインポートし、GitLab CI/CDパイプラインを起動してサンプルアプリをビルド
- devsecops-deploy-projectをGitLab にインポートし、GitLab CI/CDパイプラインを起動してHelmでアプリをデプロイ
- Gatekeeperを導入した環境では、ポリシー違反時にデプロイが拒否される動作を確認可能
