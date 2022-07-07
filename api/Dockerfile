# ベースイメージを指定する
# FROM ベースイメージ：タグ
FROM ruby:2.7.2-alpine

# Dockerfile内で使用する変数
# app
ARG WORKDIR

# 環境変数を定義[Dockerfile,コンテナ参照可]
# Rails ENV["TZ"] => Asia/Tokyo
ENV RUNTIME_PACKAGES="linux-headers libxml2-dev make gcc libc-dev nodejs tzdata postgresql-dev postgresql git" \
  DEV_PACKAGES="build-base curl-dev" \
  # HOME=/app
  HOME=/${WORKDIR} \
  LANG=C.UTF-8 \
  TZ=Asia/Tokyo

# ベースイメージ（2.7.2-alpine）に対してコマンドを実行する
# ${変数}　or $変数
# ${HOME} => /app
RUN echo ${HOME}

# Dockerfile内で指定した命令を実行する ... RUN.COPY,ADD,ENTRYPOINT,CMD　の5つが実行される
# 作業ディレクトリを定義
# コンテナ/app/Railsアプリ　が作成される
WORKDIR ${HOME}

# ホスト側（自身のPC）のファイルをコンテナにコピーする
# COPY コピー元（ホスト）　コピー先（コンテナ）
# Gemfile+ ... Gemfileから始まるファイルを全指定（Gemfile,Gemfile.look）
# ./ ... 今いるディレクトリ（カレントディレクトリ）
# コピー元（ホスト） ... Dockerfileがあるディレクトリ以下を指定(apiディレクトリ)
# コピー先（コンテナ） ... 絶対パス or 相対パスを指定
COPY Gemfile* ./

# apk ... Alpine Linuxのコマンド
# apk update ... パッケージの最新リストを取得
RUN apk update && \
  # apk upgrade ... インストールされたパッケージを最新のものにする
  apk upgrade && \
  # apk add ... パッケージのインストールを実行する
  # -no-cache ... パッケージをキャッシュしない（Dockerイメージを軽量化）
  apk add --no-cache ${RUNTIME_PACKAGES} && \
  # --virtual 名前（任意） ... 仮想パッケージ（パッケージをひとまとめにする）
  apk add --virtual build-dependencies --no-cache ${DEV_PACKAGES} && \
  # bundle install ... Gemのインストールコマンド
  # -j4(jobs=4) ... Gemインストールの高速化
  bundle install -j4 && \
  # apk del ... パッケージを削除（いちいち削除するのはDockerイメージを軽量化するため）
  apk del build-dependencies

# . ... Dockerfileのある全てのディレクトリ（サブディレクトリも含む）
COPY . ./

# コンテナ内で実行したいコマンドを定義
# -b ... バインド　プロセスを指定したIPアドレス(0.0.0.0)に指紐付け（バインド）するオプション
CMD ["rails", "server", "-b", "0.0.0.0"]

# ホスト（PC） 　｜ コンテナ
# ブラウザ(外部) | Rails