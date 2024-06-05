FROM ubuntu:20.04

# 必要なパッケージをインストール
RUN apt-get update && \
    apt-get install -y mysql-server glibc-langpack-ja glibc-locale-source git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# MySQL設定
RUN service mysql start

# ロケール設定
ENV LANG=ja_JP.UTF-8
