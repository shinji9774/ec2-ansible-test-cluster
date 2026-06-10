# ec2-ansible-test-cluster

## サーバ構成

### インスタンス構成

- **台数** : 3台
- **OS** : Ubuntu
- **用途** : Ansibleの検証・テスト環境

### マスター・ターゲット構成

| ロール | インスタンス名 | 用途 | 備考 |
| -------- | ------------ | ------ | ------------ |
| **Master** | `*-master` | Ansible実行元ノード | ansibleインストール・疎通設定済 |
| **Target** | `*-dev-target-1` | 管理対象ノード（1） | - |
| **Target** | `*-dev-target-2` | 管理対象ノード（2） | - |
| **Target** | `*-test-target-1` | 管理対象ノード（3） | - |
| **Target** | `*-test-target-2` | 管理対象ノード（4） | - |

## クライアントサーバ使用方法

### サーバを起動する

1. **AWSマネジメントコンソールにIAMユーザでログイン**
   - [AWSログインページ](https://signin.aws.amazon.com/)にアクセスし、アカウントID、IAMユーザ名、パスワードを入力してログイン

2. **EC2ダッシュボードに移動**
   - サービス一覧から「EC2」を選択

3. **インスタンスを確認**
   - 左側メニューの「インスタンス」をクリックし、サーバのインスタンスを確認

4. **対象インスタンスを起動 (停止状態のときのみ実行)**
   - 起動したいインスタンスをクリックして選択
   - 右上の「インスタンスの状態」から「インスタンスを開始」を選択
   - インスタンスが「実行中」になることを確認

### サーバにログインする

1. **AWSマネジメントコンソールにIAMユーザでログイン**
   - [AWSログインページ](https://signin.aws.amazon.com/)にアクセスし、アカウントID、IAMユーザ名、パスワードを入力してログイン

2. **EC2ダッシュボードに移動**
   - サービス一覧から「EC2」を選択

3. **インスタンスを確認**
   - 左側メニューの「インスタンス」をクリックし、ログインしたいインスタンスを確認

4. **下記のいずれかの方法でログインする**

#### EC2 Instance Connectでログインする

1. **インスタンスに接続**
   - ログインしたいインスタンスをクリックして選択
   - 右上の「接続」ボタンをクリック
   - 「EC2 Instance Connect」タブでユーザー名が「ubuntu」であることを確認
   - 「接続」ボタンをクリック

#### PuttyでSSH接続する

1. **Puttyで接続情報を設定**
   - Putty を起動
   - **Session** タブで以下を入力:
     - Host Name (or IP address): インスタンスのパブリック IPv4 アドレス
     - Port: 22
     - Saved Sessions: 接続設定を保存する場合は任意の名前を入力
   - **Connection** > **SSH** > **Auth** タブで以下を設定:
     - Private key file for authentication: 変換した `.ppk` ファイルを選択
   - **Connection** > **Data** タブで以下を設定:
     - Auto-login username: `ubuntu`

2. **接続**
   - Session タブに戻り「Open」ボタンをクリック
   - セキュリティアラートが表示された場合は「Accept」を選択

### サーバを停止する

> [!IMPORTANT]
> Amazon EC2は動作中は従量課金のため、作業終了時には本手順ですべてのサーバを停止しておくこと

1. **[サーバにログインする](#サーバにログインする)** セクションの共通準備手順を実施
   - AWSマネジメントコンソールにログインしインスタンスを確認

2. **インスタンスに接続**
   - 停止したいインスタンスをクリックして選択
   - 「[サーバにログインする](#サーバにログインする)」のいずれかの方法を参照して接続

3. **shutdownを実行**
   ```
   sudo shutdown -h now
   ```
   - すぐに停止されるためコンソールの応答がなくなる。そのままコンソールのページを閉じてよい

4. **対象インスタンスの停止を確認**
   - インスタンス一覧の画面でインスタンスの状態が「停止済み」になることを確認

5. **他のサーバについても本手順の2～4の操作を実施**

## Ansible実行

### サンプルhostsファイル

- `/opt/hosts_sample` 参照
  ```ini
  [servers]
  test-dev-target-1 ansible_host=<private ip>
  test-dev-target-2 ansible_host=<private ip>
  test-test-target-1 ansible_host=<private ip>
  test-test-target-2 ansible_host=<private ip>

  [all:vars]
  ansible_user=ubuntu
  ```
