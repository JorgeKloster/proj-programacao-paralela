# Sistema de Ingestão de Webhooks e Processamento Assíncrono

API REST em Laravel que recebe webhooks de CRM, responde `202 Accepted` imediatamente e delega a persistência relacional a workers em background gerenciados pelo Laravel Horizon.

## Pré-requisitos

- [Docker Desktop](https://www.docker.com/products/docker-desktop) (ou Docker + Docker Compose no Linux)
- [Git](https://git-scm.com/)

PHP e Composer locais são opcionais: todos os comandos abaixo rodam dentro dos containers.

## Subindo o ambiente

```bash
cp .env.example .env
docker compose up --build -d
docker exec -it laravel_app composer install
docker exec -it laravel_app php artisan key:generate
docker exec -it laravel_app php artisan migrate
```

Após o `composer install`, reinicie o Horizon para ele enxergar as dependências:

```bash
docker compose restart horizon
```

## Serviços

| Serviço  | URL / Porta                       |
|----------|-----------------------------------|
| API      | http://localhost:8010             |
| Horizon  | http://localhost:8010/horizon     |
| Adminer  | http://localhost:8080 (servidor `mysql`, usuário `laravel`, senha `secret`) |
| MySQL    | localhost:3307                    |
| Redis    | localhost:6379                    |

Teste rápido: `curl http://localhost:8010/api/health` deve retornar `{"status":"ok"}`.

## ngrok (opcional, para expor o webhook publicamente)

O container `ngrok` não sobe junto com os demais (usa `profiles`). Preencha `NGROK_AUTHTOKEN` e `NGROK_DOMAIN` no `.env` e suba explicitamente:

```bash
docker compose --profile ngrok up -d ngrok
```

## Comandos úteis

```bash
docker exec -it laravel_app php artisan make:model NomeDaModel --migration
docker exec -it laravel_app php artisan make:job NomeDoJob
docker exec -it laravel_app php artisan make:request NomeDaRequest
docker exec -it laravel_app php artisan queue:failed
docker exec -it laravel_app php artisan queue:retry all
```
