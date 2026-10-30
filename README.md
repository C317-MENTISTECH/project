# MentisTech – Project

> **[PREENCHER]** Uma ou duas frases dizendo o que o projeto faz e para quem ele serve.

Projeto desenvolvido pela equipe **C317-MENTISTECH**. É uma aplicação web com **API em Python (FastAPI)**, banco de dados relacional gerenciado com **SQLAlchemy + Alembic** e uma pasta de **frontend** separada.

---

## Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Tecnologias](#tecnologias)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como funciona](#como-funciona)
- [Como rodar](#como-rodar)
- [Testes](#testes)
- [Equipe](#equipe)

---

## Sobre o projeto

**[PREENCHER]** Explique aqui:

- Qual problema o projeto resolve
- Quem são os usuários
- Principais funcionalidades (ex.: cadastro/login, listagens, recomendações, painéis etc.)

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Linguagem | Python 3 |
| API / Backend | [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/) |
| Validação de dados | Pydantic / pydantic-settings |
| Banco de dados | SQLite (`app.db`) via [SQLAlchemy 2](https://www.sqlalchemy.org/) |
| Migrações | [Alembic](https://alembic.sqlalchemy.org/) |
| Autenticação | JWT (`python-jose`) e hash de senha com `passlib` + `bcrypt` |
| Templates | Jinja2 |
| Machine Learning / dados | scikit-learn, NumPy, SciPy, joblib |
| Monitoramento de erros | Sentry (`sentry-sdk`) |
| Testes | pytest |
| Frontend | **[PREENCHER: ex. React, Vue, HTML/JS puro]** |

## Estrutura do repositório

```
project/
├── app/                   # Código da API (aplicação FastAPI, criada por create_app())
├── frontend/              # Interface do usuário
├── migrations/            # Migrações do banco de dados (Alembic)
├── scripts/               # Scripts auxiliares
├── tests/                 # Testes automatizados
├── alembic.ini            # Configuração do Alembic
├── app.db                 # Banco SQLite local
├── main.py                # Ponto de entrada: sobe o servidor com Uvicorn
├── pytest.ini             # Configuração do pytest
├── requirements.txt       # Dependências da aplicação
└── requirements-test.txt  # Dependências para testes
```

## Como funciona

1. O arquivo `main.py` importa a função `create_app()` do pacote `app` e cria a aplicação FastAPI.
2. O servidor é iniciado com **Uvicorn** em `http://127.0.0.1:8000`, com *reload* automático durante o desenvolvimento.
3. Os dados ficam em um banco **SQLite** (`app.db`), acessado pelo **SQLAlchemy**. A estrutura das tabelas é versionada pelo **Alembic** (pasta `migrations/`).
4. O acesso protegido é feito com **tokens JWT**, e as senhas são armazenadas com hash **bcrypt**.
5. O **frontend** consome a API e exibe os dados para o usuário.

**[PREENCHER]** Descreva aqui o fluxo principal do sistema (por exemplo: usuário se cadastra → faz login → usa a funcionalidade X → recebe o resultado Y) e, se houver, o papel do modelo de machine learning.

## Como rodar

### Pré-requisitos

- [Python 3.11+](https://www.python.org/downloads/) (confirme a versão usada pela equipe)
- [Git](https://git-scm.com/)
- **[PREENCHER]** Node.js, caso o frontend precise

### 1. Clonar o repositório

```bash
git clone https://github.com/C317-MENTISTECH/project.git
cd project
```

### 2. Criar e ativar o ambiente virtual

```bash
python -m venv venv

# Linux / macOS
source venv/bin/activate

# Windows (PowerShell)
venv\Scripts\Activate.ps1
```

### 3. Instalar as dependências

```bash
pip install -r requirements.txt
```

### 4. Configurar variáveis de ambiente

O projeto usa `python-dotenv`, então as configurações ficam em um arquivo `.env` na raiz:

```env
# [PREENCHER] Ajuste conforme as variáveis que o projeto realmente usa
SECRET_KEY=troque-por-uma-chave-secreta
DATABASE_URL=sqlite:///./app.db
SENTRY_DSN=
```

### 5. Aplicar as migrações do banco

```bash
alembic upgrade head
```

### 6. Iniciar o backend

```bash
python main.py
```

ou, diretamente com o Uvicorn:

```bash
uvicorn main:app --reload
```

A API ficará disponível em **http://127.0.0.1:8000**.
A documentação interativa gerada pelo FastAPI fica em **http://127.0.0.1:8000/docs**.

### 7. Iniciar o frontend

```bash
cd frontend
# [PREENCHER] comandos do frontend, por exemplo:
# npm install
# npm run dev
```

## Testes

Instale as dependências de teste e rode o pytest:

```bash
pip install -r requirements-test.txt
pytest
```

## Equipe

**[PREENCHER]** Liste os integrantes da equipe C317-MENTISTECH.

| Nome | GitHub |
|---|---|
| | |
