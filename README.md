# Laboratório 5G Core com Open5GS usando Docker (Passo a passo)
<img width="886" height="498" alt="image" src="https://github.com/user-attachments/assets/bf284bdf-03db-4895-b7ba-d84190d76cc2" />

## Estrutura do projeto

```text
tarefa-telco_cloud_CristianoSSilva/
├── README.md
├── docker-compose.yml
├── .gitignore
├── start.sh
└── stop.sh


Este guia foi feito para **estudantes iniciantes**, mesmo sem experiência com Linux.

👉 Basta **seguir os passos na ordem**, copiando e colando os comandos.

---

## 1️⃣ O que será feito neste laboratório

Você irá:

* Criar uma máquina virtual com Ubuntu
* Instalar Docker e Docker Compose
* Subir o Core 5G (Open5GS)
* Verificar que o Core está funcionando

Nada de interface gráfica. Tudo via terminal.

---

## 2️⃣ Criar a máquina virtual (resumo)

Use o **VirtualBox** e crie uma VM com:

* Sistema: **Ubuntu Server 22.04**
* Memória: **4 GB**
* CPU: **2**
* Disco: **40 GB**
* Rede: **NAT**

Instale o Ubuntu normalmente e crie um usuário.

---

## 3️⃣ Abrindo o terminal

Após entrar no Ubuntu, você verá uma **tela preta com texto**.
Isso é o **terminal**.

Tudo abaixo deve ser digitado nele.

---

## 4️⃣ Atualizar o sistema

```bash
sudo apt update
sudo apt upgrade -y
```

Reinicie:

```bash
reboot
```

---

## 5️⃣ Instalar o Docker

```bash
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

Permitir usar Docker sem senha:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verifique:

```bash
docker --version
```

---

## 6️⃣ Instalar Docker Compose

```bash
sudo apt install -y docker-compose
```

Verifique:

```bash
docker-compose --version
```

---

## 7️⃣ Criar a pasta do projeto

```bash
mkdir open5gs-docker
cd open5gs-docker
```

---

## 8️⃣ Criar o arquivo `docker-compose.yml`

Crie o arquivo:

```bash
nano docker-compose.yml
```

Cole **TUDO** abaixo (copie exatamente):

```yaml
version: "3.8"

services:
  mongodb:
    image: mongo:6.0
    container_name: mongo
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: "admin"
      MONGO_INITDB_ROOT_PASSWORD: "admin"
    volumes:
      - mongo_data:/data/db
    ports:
      - "27017:27017"

  open5gs:
    image: gradiant/open5gs:2.7.6
    container_name: open5gs
    depends_on:
      - mongodb
    restart: unless-stopped
    environment:
      DB_URI: "mongodb://admin:admin@mongodb:27017/open5gs?authSource=admin"
      TZ: "America/Sao_Paulo"
    ports:
      - "7777:7777"
      - "9090:9090"
      - "38412:38412/sctp"
      - "2152:2152/udp"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:7777"]
      interval: 30s
      timeout: 5s
      retries: 5

volumes:
  mongo_data:
```

Salvar:

* **CTRL + O**
* **ENTER**
* **CTRL + X**

---

## 9️⃣ Subir o Core 5G

```bash
docker-compose up -d
```

Aguarde o download das imagens.

---

## 🔍 10️⃣ Verificações (prints para entrega)

### Ver containers rodando

```bash
docker ps
```

Você deve ver:

* `mongo`
* `open5gs`

---

### Ver logs do Open5GS

```bash
docker logs open5gs
```

---

### Ver portas abertas

```bash
ss -tulpn
```

Procure pelas portas:

* 7777
* 9090
* 38412
* 2152

---

## ✅ Resultado esperado

* Core 5G está ativo
* Containers em execução
* Interfaces prontas para OpenRAN / UE
* Ambiente reproduzível

---

## 🛑 Parar o ambiente (se necessário)

```bash
docker-compose down
```
