# Postgres

## 1. 安装（包含 pgvector 扩展）

Ubuntu 22.04 下安装（该发行版本软件仓库的 Postgres 为 14）

```bash
apt install postgres postgresql-server-dev-14
```

修改默认密码

```bash
sudo -u postgres psql

ALTER USER postgres WITH PASSWORD 'E1Wdg9qi';
```

安装 pgvector

```bash
mkdir tmp
cd tmp
git clone --branch v0.8.0 https://gitclone.com/github.com/pgvector/pgvector.git
cd pgvector
make
make install
```

