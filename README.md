

# fipID



Plataforma Backend as a Service (BaaS) desenvolvida em equipe.



## Módulo: Autenticação



O Keycloak será utilizado para gerenciar a autenticação e os usuários.



## Ambiente de desenvolvimento



- Docker

- Keycloak 26.8.1



## Configuração inicial



- [x] Iniciar o Keycloak via Docker

- [x] Criar o Realm `fipid-dev`

- [x] Criar o Client `fipid-app`

- [x] Criar usuário de teste

- [ ] Testar o fluxo de autenticação

- [ ] Padronizar a configuração para a equipe



## Acesso local



O painel administrativo fica disponível em:



http://localhost:8080



## Observação



As configurações foram realizadas localmente e ainda precisam ser

documentadas ou automatizadas para que outros integrantes possam

reproduzir o ambiente.

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

