
```markdown
# 🚀 Laravel + Docker + Nginx + MySQL

Ambiente completo de desenvolvimento Laravel utilizando **Docker Compose**, com containers separados para:
- **PHP-FPM** (rodando o Laravel)
- **Nginx** (servidor web)
- **MySQL** (banco de dados)
- (opcional) **phpMyAdmin**

---

## 📁 Estrutura do Projeto

```

laravel-docker/
├── docker-compose.yml
├── Dockerfile
├── nginx/
│   └── default.conf
├── .env
└── src/
└── (aqui fica o projeto Laravel)

````

---

## 🧰 Requisitos

Antes de começar, você precisa ter instalado:

- [Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Git](https://git-scm.com/downloads)
- (Opcional) [Composer](https://getcomposer.org/)

---

## ⚙️ Configuração Inicial

### 1️⃣ Clonar o repositório

```bash
git clone https://github.com/seu-usuario/laravel-docker.git
cd laravel-docker
````

### 2️⃣ Criar o projeto Laravel (caso ainda não exista)

```bash
docker compose run --rm app composer create-project --prefer-dist laravel/laravel src
```

---

## 🐳 Subindo os Containers

```bash
docker compose up -d --build
```

Aguarde o build inicial.
Após isso, acesse no navegador:

👉 **[http://localhost:8080](http://localhost:8080)**

---

## 💾 Configuração do Banco de Dados (MySQL)

No arquivo `.env` do Laravel (`src/.env`), use as seguintes configurações:

```env
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=secret
```

Em seguida, rode as migrations:

```bash
docker compose exec app php artisan migrate
```

---

## 🌐 Serviços Disponíveis

| Serviço                     | URL/Host                                       | Porta | Descrição              |
| --------------------------- | ---------------------------------------------- | ----- | ---------------------- |
| **App (Laravel)**           | [http://localhost:8080](http://localhost:8080) | 8080  | Aplicação Laravel      |
| **MySQL**                   | `db` (interno) / `localhost:3307`              | 3307  | Banco de dados MySQL   |
| **phpMyAdmin** *(opcional)* | [http://localhost:8081](http://localhost:8081) | 8081  | Interface web do MySQL |

> 💡 A porta externa do MySQL pode variar conforme configurado no `docker-compose.yml`.

---

## 🔐 Permissões (importante no Windows/Linux)

Se ocorrer erro de permissão (`storage` ou `cache`), execute:

```bash
docker compose exec app bash
chown -R www-data:www-data storage bootstrap/cache
chmod -R 775 storage bootstrap/cache
exit
```

---

## 🧪 Comandos Úteis

| Comando                                       | Descrição                              |
| --------------------------------------------- | -------------------------------------- |
| `docker compose up -d`                        | Inicia os containers em segundo plano  |
| `docker compose down`                         | Para e remove os containers            |
| `docker compose exec app bash`                | Acessa o terminal do container Laravel |
| `docker compose exec app php artisan migrate` | Executa as migrations                  |
| `docker compose exec app php artisan tinker`  | Abre o console interativo do Laravel   |
| `docker compose logs -f`                      | Mostra logs em tempo real              |

---

## 🧱 Estrutura dos Containers

* **app** → PHP 8.2-FPM + Composer
* **nginx** → Servidor web configurado em `nginx/default.conf`
* **db** → MySQL 8.0 com volume persistente
* *(opcional)* **phpmyadmin** → Interface web para o banco de dados

---

## 🧹 Limpeza e Reset

Para parar e remover todos os containers, volumes e redes:

```bash
docker compose down -v
```

---

## 🧩 Dica Extra: phpMyAdmin (opcional)

Para adicionar o phpMyAdmin, inclua este trecho no seu `docker-compose.yml`:

```yaml
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: laravel_phpmyadmin
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: db
      PMA_USER: laravel
      PMA_PASSWORD: secret
    depends_on:
      - db
    networks:
      - laravel
```

Acesse: [http://localhost:8081](http://localhost:8081)

---

## 🧠 Observações

* A linha `version:` no `docker-compose.yml` é opcional (Docker Compose v2 ignora).
* O Laravel roda dentro do container, **não instale o PHP localmente**.
* Qualquer alteração em `.env` requer limpar cache:

  ```bash
  docker compose exec app php artisan config:clear
  docker compose exec app php artisan cache:clear
  ```

---
