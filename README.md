<p align="center">
<img src="https://cwmkt.com.br/wp-content/uploads/2023/08/logo-github-cwmkt.svg" alt="DispZap Whats Marketing" width="240" />
<p align="center">Seja bem-vindo ao Guia de Instalação TypeBot 🚀</p>
</p>
  
<p align="center">
<img src="https://whatsapp.com/favicon.ico" alt="WhatsAPP-logo" width="32" />
<span>Grupo WhatsaAPP: </span>
<a href="https://chat.whatsapp.com/GzzMFvPxsKo0esM2w25uCB" target="_blank">Grupo</a>
</p>

<hr />
<hr />

<details>
<summary>Manual de Instalação TypeBot</summary>

```bash
sudo apt update && apt upgrade -y
```

```bash
apt  install docker-compose
```

```bash
mkdir typebot
```

```bash
nano typebot/docker-compose.yml
```

```bash
version: '3.3'
services:
  caddy-gen:
    image: 'wemakeservices/caddy-gen:latest'
    restart: always
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - caddy-certificates:/data/caddy
    ports:
      - '80:80'
      - '443:443'
    depends_on:
      - typebot-builder
      - typebot-viewer
  typebot-db:
    image: postgres:13
    restart: always
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=typebot # Troque se necessario
      - POSTGRES_PASSWORD=typebot # Troque se necessario
  typebot-builder:
    labels:
      virtual.host: 'typebot.dominio.com.br' # Troque pelo seu dominio ou subdominio
      virtual.port: '3000'
      virtual.tls-email: 'contato@seudominio.com.br' # Troque pelo seu email
    image: baptistearno/typebot-builder:latest
    restart: always
    depends_on:
      - typebot-db
    extra_hosts:
      - 'host.docker.internal:host-gateway'
    # See https://docs.typebot.io/self-hosting/configuration for more configuration options
    environment:
      - DATABASE_URL=postgresql://postgres:typebot@typebot-db:5432/typebot
      - NEXTAUTH_URL=https://typebot.dominio.com.br # Troque pelo seu dominio ou subdominio
      - NEXT_PUBLIC_VIEWER_URL=https://bot.dominio.com.br # Troque pelo seu dominio ou subdominio
      - ENCRYPTION_SECRET=K+Bar660Ofaec7v1jHC25tAn3l2b7c81
      - ADMIN_EMAIL=contato@seudominio.com.br # Troque pelo seu email
      - DISABLE_SIGNUP=true # Mude Para false caso queira permitir que outras pessoas criem contas é nescessario estar como false no primeiro login do administrador
      - SMTP_AUTH_DISABLED=false
      - SMTP_SECURE=true # Troque para false seu nao usar a porta 465 ou se estiver enfretando problemas no login
      - SMTP_HOST=smtp.hostinger.com # Troque pelo seu SMTP USE SOMENTE DOMINIO PROPRIETARIOS
      - SMTP_PORT=465 # altere aqui se nescessario portas comuns 25, 587, 465, 2525
      - SMTP_USERNAME=contato@seudominio.com.br # Troque pelo seu email
      - SMTP_PASSWORD=SenhadoEmail # Troque pela sua senha
      - NEXT_PUBLIC_SMTP_FROM=contato@seudominio.com.br # Troque pelo seu email
      - S3_ACCESS_KEY=minio # Troque se necessario
      - S3_SECRET_KEY=minio123 # Troque se necessario
      - S3_BUCKET=typebot
      - S3_ENDPOINT=storage.typebot.dominio.com.br # Troque pelo seu dominio ou subdominio
  typebot-viewer:
    labels:
      virtual.host: 'dominiotypebotfront # Troque pelo seu dominio ou subdominio
      virtual.port: '3000'
      virtual.tls-email: 'contato@seudominio.com.br' # Troque pelo seu email
    image: baptistearno/typebot-viewer:latest
    restart: always
    # See https://docs.typebot.io/self-hosting/configuration for more configuration options
    environment:
      - DATABASE_URL=postgresql://postgres:typebot@typebot-db:5432/typebot
      - NEXTAUTH_URL=https://typebot.dominio.com.br # Troque pelo seu dominio ou subdominio
      - NEXT_PUBLIC_VIEWER_URL=https://bot.dominio.com.br # Troque pelo seu dominio ou subdominio
      - ENCRYPTION_SECRET=K+Bar660Ofaec7v1jHC25tAn3l2b7c81
      - SMTP_HOST=mail.socialnexo.com # Troque pelo seu SMTP USE SOMENTE DOMINIO PROPRIETARIOS
      - NEXT_PUBLIC_SMTP_FROM=contato@seudominio.com.br # Troque pelo seu email
      - S3_ACCESS_KEY=minio # Troque se necessario - Deve ser Igual ao Declarado no Typebot Builder S3_ACCESS_KEY=
      - S3_SECRET_KEY=minio123 # Troque se necessario - Deve ser Igual ao Declarado no Typebot Builder S3_SECRET_KEY=
      - S3_BUCKET=typebot
      - S3_ENDPOINT=storage.typebot.dominio.com.br # Troque pelo seu dominio ou subdominio
  mail:
    image: bytemark/smtp
    restart: always
  minio:
    labels:
      virtual.host: 'storage.dominiotypebotfront # Troque pelo seu dominio ou subdominio
      virtual.port: '9000'
      virtual.tls-email: 'contato@seudominio.com.br' # Troque pelo seu email
    image: minio/minio
    command: server /data
    ports:
      - '9000:9000'
    environment:
      MINIO_ROOT_USER: minio # Troque se necessario - Deve ser Igual ao Declarado no Typebot Builder S3_ACCESS_KEY=
      MINIO_ROOT_PASSWORD: minio123 # Troque se necessario - Deve ser Igual ao Declarado no Typebot Builder S3_SECRET_KEY=
    volumes:
      - s3_data:/data
  # This service just make sure a bucket with the right policies is created

  # Certifique-se de atualizar S3_ACCESS_KEY , S3_SECRET_KEY abaixo para corresponder às suas configurações do S3, elas estão no final dessa linha /usr/bin/mc config host add minio http://minio:9000 minio minio123; sendo o usuario e a senha em sequencia.
  createbuckets:
    image: minio/mc
    depends_on:
      - minio
    entrypoint: >
      /bin/sh -c "
      sleep 10;
      /usr/bin/mc config host add minio http://minio:9000 minio minio123;
      /usr/bin/mc mb minio/typebot;
      /usr/bin/mc anonymous set public minio/typebot/public;
      exit 0;
      "
volumes:
  db_data:
  s3_data:
  caddy-certificates:
    driver: local
```

```bash
docker-compose up -d
```


</details>
