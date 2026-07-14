# Job Detective 🕵️‍♂️💼

**Job Detective** é uma aplicação web que analisa o mercado de trabalho em tempo real para um determinado cargo e gera um relatório inteligente com as habilidades mais exigidas, tendências e dicas de estudo — tudo a partir de vagas reais coletadas automaticamente na internet.

> Diferente de uma busca no Google ou de uma pergunta ao ChatGPT, o Job Detective raspa dezenas de vagas **abertas hoje**, cruza os dados e entrega um panorama real e atualizado do mercado.

---

## 🎯 O Problema

Candidatos e estudantes que querem entrar ou se reposicionar no mercado de trabalho enfrentam um problema: **não sabem o que o mercado realmente está pedindo agora**.

Perguntas como *"Preciso aprender TypeScript ou Vue?"*, *"Quanto ganha um Dev Backend Pleno em São Paulo?"* ou *"Remoto ainda é comum para esse cargo?"* não têm respostas fáceis ou centralizadas. O Job Detective responde tudo isso automaticamente, com base em dados reais e atuais.

---

## 🚀 Funcionalidades do MVP

O usuário preenche três campos simples:
- **Cargo:** Ex: `Desenvolvedor Frontend`, `Analista de Dados`
- **Cidade:** Ex: `São Paulo`, `Fortaleza`, `Remoto`
- **Modalidade:** `Remoto`, `Presencial` ou `Híbrido`

E recebe um **Dossiê de Mercado** contendo:

| # | Funcionalidade | Descrição |
|---|----------------|-----------|
| RF01 | 🔍 **Busca de Vagas** | Localiza vagas abertas hoje para o cargo informado |
| RF02 | 🤖 **Coleta Automatizada** | Acessa cada vaga encontrada e extrai o conteúdo completo |
| RF03 | 🛠️ **Top Tecnologias** | Lista as habilidades técnicas mais exigidas com frequência |
| RF04 | 🧠 **Habilidades Comportamentais** | Identifica as soft skills mais pedidas nas descrições |
| RF05 | 📊 **Ranking de Requisitos** | Ordena os requisitos por importância e frequência |
| RF06 | 💰 **Faixa Salarial** | Apresenta a faixa salarial média para o cargo e região |
| RF07 | 📚 **Tópicos de Estudo** | Sugere o que estudar para se destacar no mercado atual |
| RF08 | 🏢 **Perfil das Empresas** | Mostra quais tipos de empresa mais contratam para esse cargo |
| RF09 | 🔄 **Filtros** | Permite refinar a busca por nível (Júnior/Pleno/Sênior) |

---

## 🛠️ Stack Tecnológica

A aplicação é dividida em dois projetos independentes (monorepo), cada um com sua responsabilidade clara.

### Frontend
| Tecnologia | Por quê? |
|------------|---------|
| **Next.js (React + TypeScript)** | Interface responsiva com suporte a estados assíncronos. Hospedado no **Vercel**. |
| **TailwindCSS + shadcn/ui** | Design moderno sem overhead de configuração. |

### Backend
| Tecnologia | Por quê? |
|------------|---------|
| **Python + FastAPI** | Linguagem nativa para IA e scraping. API assíncrona de alta performance. Hospedado no **Render.com**. |
| **Playwright** | Abre páginas renderizadas em JavaScript (SPAs como LinkedIn, Gupy) como um navegador real, contornando bloqueios básicos. |
| **BeautifulSoup** | Remove o "lixo" do HTML (scripts, menus, rodapés), deixando apenas o texto útil para a IA. |
| **DuckDuckGo Search** | Localiza vagas abertas e dados salariais sem custo e sem chave de API. |
| **Google Gemini API** | Lê os textos das vagas raspadas e extrai habilidades, tendências e insights em formato JSON estruturado. |

---

## 🏗️ Arquitetura e Design Patterns

A aplicação adota uma **Arquitetura em Camadas (Layered Architecture)** com separação clara de responsabilidades.

### Fluxo de uma Requisição

```
[Usuário digita cargo + cidade]
         ↓
[Next.js] → POST /api/analyze-market → [FastAPI]
                                            ↓
                                   [routes.py] (Controller)
                                            ↓
                                [report_service.py] (Service Layer)
                                    ↙        ↘          ↘
                           [job_search.py] [browser.py] [analyzer.py]
                            (DuckDuckGo)   (Playwright)   (Gemini AI)
                                            ↓
                              [cleaner.py] (BeautifulSoup)
                                            ↓
                              JSON estruturado → Frontend
```

### Design Patterns Aplicados

#### 1. Service Layer Pattern
O arquivo `routes.py` (controlador) **não contém lógica de negócio**. Ele apenas recebe a requisição HTTP e delega para `report_service.py`, que orquestra todo o fluxo.

#### 2. Adapter / Wrapper Pattern
Cada ferramenta externa vive isolada no seu próprio módulo:
- `scraping/browser.py` → encapsula o Playwright
- `scraping/job_search.py` → encapsula o DuckDuckGo
- `ai/analyzer.py` → encapsula o Gemini

Se o Google descontinuar a API Gemini, apenas `analyzer.py` precisa ser alterado. O restante do sistema permanece intacto.

#### 3. Data Transfer Objects (DTO) com Pydantic
Todas as entradas e saídas são validadas por schemas Pydantic. O frontend sempre recebe um contrato de dados previsível e tipado.

### Estrutura de Pastas

```
job-detective/
├── frontend/                     # Next.js (React + TypeScript)
│   ├── src/
│   │   ├── app/                  # Roteamento (App Router)
│   │   ├── components/           # Componentes visuais (Cards, Tags, Gráficos)
│   │   ├── hooks/                # Lógicas de estado reutilizáveis
│   │   ├── services/             # Chamadas HTTP ao Backend
│   │   └── types/                # Interfaces TypeScript
│   └── package.json
│
└── backend/                      # Python (FastAPI)
    ├── app/
    │   ├── api/                  # Rotas HTTP (Controllers)
    │   ├── models/               # Schemas Pydantic (DTOs)
    │   ├── services/             # Regras de negócio (Maestro)
    │   ├── scraping/             # Playwright + DuckDuckGo + BeautifulSoup
    │   └── ai/                   # Integração com Gemini e Prompts
    ├── requirements.txt
    └── main.py                   # Ponto de entrada
```

---

## 📦 Escopo do MVP

- ✅ Aplicação **100% gratuita** (deploy + APIs sem custo)
- ✅ Suporte a vagas públicas (sem login nas plataformas)
- ✅ Tempo de resposta alvo: **inferior a 30 segundos**
- ✅ Stateless — sem banco de dados nesta versão
- ✅ Busca focada em LinkedIn e Gupy; fallback para Catho se necessário

---

## 🔮 Melhorias Futuras

| Feature | Descrição |
|---------|-----------|
| 💾 **Histórico de Relatórios** | Banco de dados (PostgreSQL/Supabase) para salvar buscas anteriores |
| 📄 **Match de Currículo** | Upload de PDF do currículo e cálculo de % de aderência ao perfil ideal do mercado |
| 🎤 **Simulador de Entrevista** | Chat interativo com a IA fazendo perguntas técnicas baseadas no cargo pesquisado |
| 🔔 **Alertas de Mercado** | Monitoramento contínuo e notificação quando o cenário do mercado mudar |
| 🌎 **Suporte a Regiões Internacionais** | Análise de mercado para vagas nos EUA, Europa e outros países |
| 📈 **Gráficos e Visualizações** | Dashboard interativo com gráficos de tendência ao longo do tempo |
