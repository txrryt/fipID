# fipID

Sistema de identidade e autenticação com **login único (SSO)**, desenvolvido em equipe.

Com uma única conta no fipID, o usuário acessa diferentes sistemas integrados sem precisar de um login e uma senha para cada aplicação, no mesmo conceito do "Entrar com Google".

O fipID utiliza o **Keycloak** como plataforma de gerenciamento de identidade, responsável por autenticação, sessões, permissões e integração com as aplicações, por meio dos padrões OAuth 2.0 e OpenID Connect (OIDC).

## Objetivos

- Centralizar o gerenciamento de usuários
- Permitir login único em diferentes sistemas
- Reduzir a quantidade de senhas que o usuário precisa memorizar
- Aumentar a segurança da autenticação (MFA, bloqueio por tentativas, auditoria)
- Controlar sessões, permissões e acessos por aplicação
- Facilitar a integração de novos sistemas

## Como funciona

1. O usuário acessa uma aplicação integrada.
2. A aplicação identifica que ele não está autenticado e o redireciona para o fipID.
3. O usuário informa suas credenciais e o fipID cria uma sessão.
4. O fipID devolve à aplicação as informações para identificar o usuário, e a aplicação concede o acesso conforme as permissões dele.
5. Ao acessar outra aplicação integrada, o usuário reaproveita a sessão do fipID e entra sem novo login.

```text
                    ┌──────────────────┐
                    │      USUÁRIO     │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │      fipID       │
                    │     Keycloak     │
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
       ┌────────────┐ ┌────────────┐ ┌────────────┐
       │   Portal   │ │  Sistema   │ │ Biblioteca │
       │  do Aluno  │ │ Acadêmico  │ │  Digital   │
       └────────────┘ └────────────┘ └────────────┘
```

## Tecnologias

- Keycloak 26.8.1 (identidade e autenticação)
- OpenID Connect e OAuth 2.0
- PostgreSQL 16 (banco de dados do Keycloak)
- Docker e Docker Compose

## Status da configuração

- [x] Iniciar o Keycloak via Docker
- [x] Criar o Realm `fipid-dev`
- [x] Criar o Client `fipid-app`
- [x] Criar usuário de teste
- [ ] Testar o fluxo de autenticação
- [ ] Padronizar a configuração para a equipe

## Roadmap

- [ ] Exportar o realm para `infra/keycloak/` e importá-lo automaticamente ao subir o ambiente
- [ ] Configurar o realm conforme os requisitos (campos do cadastro, política de senha, MFA, eventos, roles e grupos)
- [ ] Tema em português e responsivo
- [ ] Aplicações de exemplo integradas, para validar o SSO e a autorização por aplicação
- [ ] Guia de integração para novos sistemas
- [ ] Dashboard administrativo
- [ ] Preparação para produção (proxy reverso com HTTPS, backups e monitoramento)
- [ ] Testes de aceitação

## Como rodar o ambiente local

### Pré-requisitos

- [Git](https://git-scm.com)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Windows/macOS) ou Docker Engine com Compose (Linux)
- Windows: WSL2 habilitado (`wsl --install` no PowerShell como administrador)

Confira se o Docker está funcionando:

```bash
docker --version
docker compose version
```

### Passo a passo

1. Clone o repositório (de preferência fora de pastas sincronizadas, como o OneDrive):

```bash
   git clone https://github.com/txrryt/fipID.git
   cd fipID
```

2. Crie seu arquivo de variáveis a partir do modelo:

```bash
   # Windows (PowerShell)
   copy .env.example .env
   # Linux/macOS
   cp .env.example .env
```

3. Abra o `.env` e troque os valores `troque-isto` por senhas locais de desenvolvimento.
   Use apenas letras e números e **nunca** reutilize senhas reais.

4. Suba o ambiente:

```bash
   docker compose up -d
```

5. Aguarde cerca de 1 minuto (o Keycloak demora para iniciar). Para acompanhar:

```bash
   docker compose logs -f keycloak
```

6. Acesse:
   - Console de administração: http://localhost:8080/admin (realm `master`, usuário e senha definidos no `.env`)
   - Conta de usuário do realm de desenvolvimento: http://localhost:8080/realms/fipid-dev/account

### Comandos úteis

| Comando | O que faz |
|---|---|
| `docker compose ps` | Mostra os serviços em execução |
| `docker compose logs -f keycloak` | Acompanha os logs do Keycloak |
| `docker compose stop` | Para os containers, mantendo os dados |
| `docker compose up -d` | Sobe (ou retoma) os containers |
| `docker compose down` | Remove os containers, mantendo os dados |
| `docker compose down -v` | Remove containers **e apaga os dados** (volume) |

### Atenção

- O arquivo `.env` contém senhas e **nunca deve ser commitado** (já está no `.gitignore`).
- `docker compose down -v` apaga o banco do Keycloak, incluindo realms e usuários.
- O usuário e a senha do admin (`KC_ADMIN_USER` / `KC_ADMIN_PASSWORD`) só são aplicados na **primeira** inicialização com o banco vazio. Alterar o `.env` depois não muda a senha do admin existente.

### Solução de problemas

- **A página não abre logo após o `up`:** o Keycloak ainda está iniciando. Espere e confira os logs.
- **Erro de porta 8080 em uso:** outro programa ou container usa a porta. Pare-o ou rode `docker ps` para identificar.
- **O admin não entra:** o login do admin é em `http://localhost:8080/admin`, e não em `/realms/fipid-dev/...`. Confira também a senha usada na primeira inicialização.
- **Precisa recomeçar do zero (ambiente de dev):** `docker compose down -v` e depois `docker compose up -d`.

## Observação

A configuração do realm (client, roles e usuários de teste) foi feita localmente e ainda precisa ser exportada para o repositório, para que todos os integrantes tenham exatamente o mesmo ambiente.

## Como contribuir

- Crie uma branch por tarefa, a partir da branch principal da equipe (por exemplo, `feature/nome-da-tarefa`).
- Use mensagens de commit padronizadas: `feat:`, `fix:`, `docs:`.
- Abra um Pull Request e peça revisão de outro integrante antes do merge.
- Nunca faça commit do `.env` nem de senhas reais.