# MentisTech

Plataforma web de **acompanhamento de bem-estar emocional** para pessoas e equipes. Colaboradores respondem a check-ins mensais rápidos, o sistema calcula uma pontuação de bem-estar com apoio de um modelo de machine learning e mostra a evolução em painéis. Psicólogos têm acesso a visões agregadas por time e pela empresa.

---

## Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Funcionalidades](#funcionalidades)
- [Como funciona](#como-funciona)
- [Tecnologias](#tecnologias)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como rodar](#como-rodar)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Scripts úteis](#scripts-úteis)
- [Testes](#testes)
- [Principais rotas](#principais-rotas)
- [Equipe](#equipe)

---

## Sobre o projeto

A MentisTech transforma respostas de check-ins em indicadores visuais de **humor, estresse e evolução emocional** ao longo do tempo. A proposta é apoiar uma rotina de autocuidado mais leve e dar a lideranças e psicólogos uma visão mais clara de como as equipes estão, para conversas melhores sobre cuidado e prevenção.

## Funcionalidades

- **Cadastro e login** com e-mail corporativo, autenticação por token JWT e senha com hash bcrypt.
- **Check-in mensal** com 10 perguntas em escala (sobrecarga, apoio, vida pessoal, cultura inclusiva, liderança, trabalho em equipe, desempenho, reconhecimento, crescimento e satisfação geral). Novo check-in liberado a cada 30 dias.
- **Pontuação de bem-estar (0–100)**, humor geral e um texto de *insights* gerados pela IA a cada envio.
- **Tendência** (melhora, estabilidade ou piora) comparando o check-in atual com o histórico da pessoa.
- **Painel do usuário** com resumo rápido: nível atual, total de check-ins, tendência e próximo check-in.
- **Dashboards** com histórico individual e, para psicólogos, visão por time e visão geral da empresa.
- **Contato com especialistas:** envio de e-mail de suporte pela própria plataforma.

## Como funciona

1. A pessoa se cadastra em `/autheticate` informando nome, e-mail, cargo, time e senha. Se o time ainda não existir, ele é criado. Todos os usuários ficam, por enquanto, em uma empresa padrão ("Empresa MVP").
2. Após o login, o frontend guarda o token JWT e o usa nas chamadas à API.
3. No painel (`/initial`), a pessoa inicia o check-in (`/questions`) e responde às 10 perguntas, carregadas de `app/db/questions.json`.
4. Ao enviar (`POST /forms/submit`), o `TrendAIService` calcula a pontuação de bem-estar a partir das respostas, considerando o histórico da pessoa, e classifica a tendência com um **RandomForest** (scikit-learn). O modelo treinado fica em `app/ml/trend_model.joblib` e, se necessário, é treinado automaticamente.
5. O check-in é salvo no banco com humor, pontuação e texto de insights.
6. Os dashboards (`/dashboards`) consultam as rotas de `/analytics` para exibir evolução individual, por time e da empresa. As visões de time e de empresa são restritas a usuários cujo cargo é de psicólogo.

## Tecnologias

| Camada | Tecnologia |
|---|---|
| Backend / API | Python, [FastAPI](https://fastapi.tiangolo.com/), [Uvicorn](https://www.uvicorn.org/) |
| Banco de dados | SQLite (`app.db`) com [SQLAlchemy 2](https://www.sqlalchemy.org/) |
| Migrações | [Alembic](https://alembic.sqlalchemy.org/) |
| Autenticação | JWT (`python-jose`) e `passlib` + `bcrypt` |
| IA / ML | scikit-learn (RandomForest), NumPy, SciPy, joblib |
| Frontend | HTML (templates Jinja2), CSS e JavaScript puro, servidos pelo próprio FastAPI |
| E-mail | SMTP (`smtplib`) com template HTML em Jinja2 |
| Testes | pytest + Playwright (E2E) |

## Estrutura do repositório

```
project/
├── app/
│   ├── api/            # Rotas (auth, forms, analytics, email, páginas HTML)
│   ├── core/           # Configuração, banco, segurança e dependências
│   ├── db/             # Modelos SQLAlchemy e questions.json
│   ├── ml/             # Modelo treinado (trend_model.joblib)
│   ├── repositories/   # Acesso a dados (usuário, time, empresa)
│   ├── schemas/        # Schemas Pydantic
│   ├── services/       # Regras de negócio (auth, IA de tendência, e-mail)
│   └── templates/      # Template do e-mail de suporte
├── frontend/
│   ├── static/         # CSS, JS e imagens
│   └── templates/      # Páginas: home, register, hero (painel), questions, dashboard
├── migrations/         # Migrações do Alembic
├── scripts/            # Seed, backfill e treino do modelo
├── tests/              # Testes E2E (Playwright)
├── main.py             # Ponto de entrada (Uvicorn)
├── alembic.ini
├── requirements.txt
└── requirements-test.txt
```

## Como rodar

### Pré-requisitos

- Python 3.11 ou superior (recomendado)
- Git

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

### 4. Criar o arquivo `.env`

Crie um arquivo `.env` na raiz do projeto. Só a `SECRET_KEY` é obrigatória; sem ela a aplicação não inicia.

```env
SECRET_KEY=troque-por-uma-chave-secreta-longa
```

Veja todas as opções em [Variáveis de ambiente](#variáveis-de-ambiente).

### 5. Aplicar as migrações do banco

```bash
alembic upgrade head
```

### 6. (Opcional) Popular com dados de demonstração

```bash
python scripts/seed_trend_data.py
```

Cria contas e check-ins de exemplo. A senha de todas as contas demo é `123456` (use apenas em ambiente local). A conta `helena.psicologa@mentistech.com.br` tem perfil de psicóloga e acessa as visões de time e de empresa.

### 7. Iniciar a aplicação

```bash
python main.py
```

ou:

```bash
uvicorn main:app --reload
```

Acesse **http://127.0.0.1:8000**. O frontend é servido pela própria aplicação, então não há um segundo servidor para subir. A documentação interativa da API fica em **http://127.0.0.1:8000/docs**.

> Rode os comandos sempre a partir da raiz do projeto: os caminhos de `frontend/` e `app/` são relativos a ela.

## Variáveis de ambiente

| Variável | Obrigatória | Padrão | Uso |
|---|---|---|---|
| `SECRET_KEY` | Sim | – | Chave para assinar os tokens JWT |
| `ALGORITHM` | Não | `HS256` | Algoritmo do JWT |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Não | `60` | Validade do token |
| `DATABASE_URL` | Não | `sqlite:///./app.db` | String de conexão do banco |
| `SMTP_SERVER` | Só para e-mail | – | Servidor SMTP (ex.: `smtp.gmail.com`) |
| `SMTP_PORT` | Só para e-mail | – | Porta SMTP (ex.: `587`) |
| `EMAIL_PASSWORD` | Só para e-mail | – | Senha do e-mail remetente (no Gmail, use uma senha de app) |

As três últimas só são necessárias para o botão de contato com especialistas (`POST /email/send`).

## Scripts úteis

| Script | O que faz |
|---|---|
| `python scripts/seed_trend_data.py` | Cria usuários, times e check-ins de demonstração |
| `python scripts/train_trend_model.py` | Treina novamente o modelo de tendência e salva em `app/ml/trend_model.joblib` |
| `python scripts/backfill_ai_insights.py` | Recalcula humor, pontuação e insights dos check-ins já salvos |
| `python scripts/backfill_teams.py` | Atribui o time "Sem time" a usuários antigos sem time |

## Testes

Os testes são E2E, com Playwright e Chromium. O servidor FastAPI sobe sozinho na porta `8765` durante a execução.

```bash
pip install -r requirements-test.txt
python -m playwright install chromium
pytest -q
```

Para rodar um único fluxo:

```bash
pytest -q tests/e2e/test_questions.py
```

Em alguns cenários as chamadas à API são interceptadas, então os testes não alteram o banco local.

## Principais rotas

**Páginas**

| Rota | Página |
|---|---|
| `/` | Home |
| `/autheticate` | Login e cadastro |
| `/initial` | Painel do usuário |
| `/questions` | Check-in |
| `/dashboards` | Dashboards |

**API**

| Método e rota | Descrição |
|---|---|
| `POST /auth/register` | Cria usuário |
| `POST /auth/login` | Retorna o token JWT |
| `GET /usuarios/me` | Dados do usuário logado |
| `GET /forms/questions` | Lista as perguntas do check-in |
| `POST /forms/submit` | Envia um check-in e gera a análise de IA |
| `GET /forms/history/{user_id}` | Histórico de check-ins |
| `GET /forms/next-checkin/{user_id}` | Quando o próximo check-in estará disponível |
| `GET /analytics/company-overview` | Visão geral da empresa (psicólogos) |
| `GET /analytics/team-history/{team_id}` | Histórico de um time (psicólogos) |
| `POST /email/send` | Envia e-mail de suporte |

Há outras rotas de `/analytics` (média de estresse por usuário, time, cargo etc.). Consulte `/docs` para a lista completa.

## Equipe

Equipe **C317-MENTISTECH**.

| Nome | GitHub |
|---|---|
| | |
