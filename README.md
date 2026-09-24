# DevShowcase API — Spring Boot

Backend da plataforma DevShowcase, etapa 1: modelagem de domínio, persistência e endpoints básicos.

## Stack
- Java 17 + Spring Boot 3.3
- Spring Data JPA + H2 (arquivo local `./data/devshowcase.mv.db`, criado automaticamente)
- Bean Validation (`jakarta.validation`)

## Entidades e relacionamentos
- `Profile` 1:N `Project`
- `Project` N:N `Technology`
- `Project` 1:N `Feedback`

## Como rodar

```bash
mvn spring-boot:run
```

ou, para gerar o jar e executar:

```bash
mvn clean package
java -jar target/devshowcase-api-1.0.0.jar
```

O servidor sobe em `http://localhost:8080`. As tabelas são criadas/atualizadas automaticamente (`spring.jpa.hibernate.ddl-auto=update`). O console do H2 fica disponível em `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:file:./data/devshowcase`, usuário `sa`, senha em branco) — útil para conferir os dados durante a gravação.

## Endpoints

### Perfis
- `POST /api/profiles`
  ```json
  { "name": "Maria Silva", "email": "maria@email.com", "bio": "Dev backend", "githubUrl": "https://github.com/mariasilva" }
  ```
- `GET /api/profiles/{id}`

### Tecnologias
- `POST /api/technologies`
  ```json
  { "name": "Java" }
  ```
- `GET /api/technologies`

### Projetos
- `POST /api/projects`
  ```json
  {
    "title": "DevShowcase API",
    "description": "API de vitrine de projetos",
    "repositoryUrl": "https://github.com/maria/devshowcase-api",
    "demoUrl": "https://devshowcase.exemplo.com",
    "profileId": 1,
    "technologyIds": [1, 2]
  }
  ```
- `GET /api/projects`

## Validações
- `name`/`title` não podem ser vazios (`@NotBlank`).
- `email` deve ter formato válido (`@Email`).
- `repositoryUrl`, `demoUrl` e `githubUrl` (quando informados) devem começar com `http://` ou `https://`.
- `profileId` é obrigatório ao criar um projeto e precisa existir (senão retorna 404).
- Erros de validação retornam `400` com a lista de mensagens; e-mail/tecnologia duplicados retornam `409`.

## Testando no Postman
1. Suba a aplicação (`mvn spring-boot:run`).
2. Crie uma collection com as 6 requisições acima, usando `http://localhost:8080` como base.
3. Sequência sugerida para a gravação: criar perfil → criar tecnologias → criar projeto (usando o `id` do perfil e das tecnologias) → listar projetos → listar tecnologias → buscar perfil por id.
