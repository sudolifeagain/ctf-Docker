# Docker Compose & Docker

## 前提条件
- Docker と Docker Compose がインストールされていること。
- Windows の場合、Git Bash (MINGW64) や PowerShell を使用。
- プロジェクトルートに `picoCTF` フォルダを配置（docker-compose.yml の volumes 設定に基づく）。

## Docker Compose コマンド

### コンテナの起動（バックグラウンド）
```shell
docker compose up -d
```
- -dオプションはデタッチモードでの起動。
- 初回実行時はイメージのビルドも自動で行われる。

### コンテナのシェルアクセス（docker compose exec 方式）
```shell
docker compose exec kali bash
```
- docker-compose.yml のサービス名（`kali`）を使って接続。

### キャッシュを使わずに再ビルドして起動
```shell
docker compose up -d --build
```
- `--build` オプションで Dockerfile の変更を反映し、キャッシュを無視して再ビルド。
- 開発中に Dockerfile を修正した場合に使う。

### その他
- **ログの確認**: `docker compose logs`（リアルタイム表示: `docker compose logs -f`）
- **コンテナ停止**: `docker compose down`
- **コンテナとボリューム削除**: `docker compose down -v`
- **サービス一覧**: `docker compose ps`

## Docker コマンド（直接ビルド・実行の場合）

### イメージのビルド
```shell
docker build -f Dockerfile.kali -t my-kali:latest .
```
- `-f` : Dockerfile の指定（ここでは `Dockerfile.kali`）。
- `-t` : タグ付け（例: `my-kali:latest`）。
- `.` : ビルドコンテキスト（現在のディレクトリ）。

### 対話形式でコンテナ起動（一時実行）
```shell
docker run -it --rm my-kali:latest
```
- `-it` : 対話モード。
- `--rm` : コンテナ終了時に自動削除（一時使用に便利）。
- ボリュームマウントが必要な場合は `-v ./picoCTF:/workspace` を追加。

### コンテナ停止
```shell
docker stop <container-id>
```
- `<container-id>` は `docker ps` で確認（例: `docker ps` で ID を取得）。

### コンテナ削除
```shell
docker rm <container-id>
```
- 停止済みのコンテナを削除。実行中のものは先に `docker stop`。

### その他
- **イメージ一覧**: `docker images`
- **コンテナ一覧**: `docker ps`（全コンテナ: `docker ps -a`）
- **イメージ削除**: `docker rmi <image-id>`
- **ボリューム一覧**: `docker volume ls`
- **システムクリーンアップ**: `docker system prune`（未使用リソース削除）

## Docker リソースの管理（イメージ、コンテナ、ボリュームの表示と削除）

Docker のリソース（イメージ、コンテナ、ボリューム）を表示・削除する方法です。Windows の Git Bash から実行可能です。

### イメージの表示と削除
- **表示**: `docker images`
  - ビルド済みのイメージ一覧を表示。
- **削除**: `docker rmi <image-id>` または `docker rmi <image-name>:<tag>`
  - 例: `docker rmi my-kali:latest`
  - 強制削除: `docker rmi -f <image-id>`

### コンテナの表示と削除
- **表示**: `docker ps -a`
  - 実行中・停止中の全コンテナを表示。
- **削除**: `docker rm <container-id>` または `docker rm <container-name>`
  - 例: `docker rm docker-kali-1`
  - 実行中の場合は先に `docker stop <container-id>`。
  - 強制削除: `docker rm -f <container-id>`

### ボリュームの表示と削除
- **表示**: `docker volume ls`
  - 作成されたボリューム一覧を表示。
- **削除**: `docker volume rm <volume-name>`
  - 例: `docker volume rm my-volume`
  - 未使用のボリュームを一括削除: `docker volume prune`

#### 注意点
- 削除前に `docker compose down` でコンテナを停止。
- システム全体のクリーンアップ: `docker system prune`（未使用のリソースを削除）。
- 誤削除を避けるため、ID や名前を確認してから実行。

## メモ
- パス区切りは `/` で統一（Git Bash の場合）。ボリュームマウントで権限エラーが出る場合は、Docker Desktop の設定を確認。
- コンテナが起動しない場合、`docker compose logs` でエラーログを確認。ビルドエラー時は `--no-cache` を追加。
- Dockerfile や docker-compose.yml を変更したら、`docker compose up -d --build` で反映。

---

## dockerコマンドでDockerデーモンにアクセスする権限がない場合

sudoをつければ実行できるが、dockerグループにユーザーを追加したほうが簡便になる


```shell
sudo usermod -aG docker $USER
newgrp docker
```