# Music API --- Spring Boot 4.0.2 + Java 21

API REST para gerenciamento de **Artistas e Álbuns** com foco em
produção: segurança, escalabilidade, testes, observabilidade e
integração com S3 (MinIO).

------------------------------------------------------------------------

## 🔍 Visão Geral

Este projeto implementa uma API versionada (`/api/v1`) que:

-   Gerencia **artistas** (cantores e bandas) e seus **álbuns** (N:N);
-   Possui **segurança por domínio (CORS)**;
-   Usa **JWT** com expiração de **5 minutos** + **refresh token**;
-   Aplica **rate limit**: 10 requisições/minuto por usuário;
-   Oferece **paginação** e **filtros avançados**;
-   Realiza **upload de capas** no **MinIO (S3)**;
-   Retorna **links pré‑assinados** com expiração de 30 minutos;
-   Notifica o front via **WebSocket** ao cadastrar novo álbum;
-   Sincroniza **Regionais** a partir de endpoint externo;
-   Possui **Flyway** para versionamento do banco;
-   Expõe documentação via **Swagger/OpenAPI**;
-   Fornece **Health Checks** (liveness/readiness);
-   É entregue em **containers Docker** orquestrados via
    `docker-compose`.

------------------------------------------------------------------------

## 🚀 Como Executar

Pré-requisitos: - Docker + Docker Compose

``` bash
docker compose up -d --build
```

Acessos: - API: http://localhost:8080 - Swagger:
http://localhost:8080/swagger-ui/index.html - Health:
http://localhost:8080/actuator/health - MinIO Console:
http://localhost:9001 (minioadmin/minioadmin)

Usuário demo: - `admin / admin123`

------------------------------------------------------------------------

## 🔐 Autenticação

``` bash
POST /api/v1/auth/login
POST /api/v1/auth/refresh
```

-   Access Token: 5 minutos
-   Refresh Token: persistido no banco

------------------------------------------------------------------------

## 📚 Endpoints

### Artistas

-   POST `/api/v1/artists`
-   PUT `/api/v1/artists/{id}`
-   GET `/api/v1/artists?nome=serj&sort=nome,asc`

### Álbuns

-   POST `/api/v1/albums`
-   PUT `/api/v1/albums/{id}`
-   GET `/api/v1/albums?page=0&size=10&sort=titulo,asc`
-   GET `/api/v1/albums?artistName=mike`
-   GET `/api/v1/albums?artistType=CANTOR`

### Capas

-   POST `/api/v1/albums/{id}/covers`
-   GET `/api/v1/albums/{id}/covers` → URLs pré‑assinadas (30 min)

### Regionais

-   POST `/api/v1/regionais/sync`
-   GET `/api/v1/regionais?ativo=true`

Regras: 1. Novo no endpoint → inserir\
2. Ausente → inativar\
3. Alterado → inativar antigo + criar novo

------------------------------------------------------------------------

## 🔔 WebSocket

-   Endpoint: `/ws`
-   Topic: `/topic/albums`

Payload:

``` json
{ "albumId": 10, "titulo": "Harakiri" }
```

------------------------------------------------------------------------

## 🧪 Testes

``` bash
mvn test
```

-   Services críticos testados
-   Regras de sincronização cobertas
-   Evento de álbum validado

------------------------------------------------------------------------

## 📦 Checklist de Requisitos

-   [x] Java 21 + Spring Boot 4.0.2
-   [x] API REST versionada
-   [x] Relacionamento N:N Artista--Álbum
-   [x] POST / PUT / GET
-   [x] Paginação em álbuns
-   [x] Filtros por cantor/banda
-   [x] Busca por nome do artista (asc)
-   [x] JWT (5 min) + Refresh Token
-   [x] Bloqueio por domínio (CORS)
-   [x] Rate limit (10/min por usuário)
-   [x] Upload múltiplo de capas
-   [x] Armazenamento no MinIO (S3)
-   [x] Links pré‑assinados (30 min)
-   [x] WebSocket (novo álbum)
-   [x] Flyway (create + seed)
-   [x] OpenAPI / Swagger
-   [x] Health / Liveness / Readiness
-   [x] Docker + Docker Compose
-   [x] Projeto pronto para produção

------------------------------------------------------------------------

## 🧠 Decisões Técnicas

-   **N:N** via tabela `artist_album` para flexibilidade futura;
-   **MinIO** para simular S3 real;
-   **Rate limit em memória** (simples e claro; pronto para Redis);
-   **Refresh token persistido** para permitir revogação;
-   **WebSocket desacoplado** via eventos de domínio;
-   **Flyway** garante versionamento e rastreabilidade do schema.

------------------------------------------------------------------------

## 📈 Evoluções Futuras

-   Cache Redis para consultas frequentes;
-   Rate limit distribuído;
-   Autenticação OAuth2;
-   Auditoria completa de alterações;