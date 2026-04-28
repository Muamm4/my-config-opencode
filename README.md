# OpenCode Personal Config

Repositório de configuração pessoal do OpenCode com oh-my-openagent e skills customizadas para o agente Sisyphus.

## O Que Está Incluído

- **Plugins**: oh-my-openagent + magic-context (instalados globalmente)
- **Skills**: Conjunto abrangente de habilidades especializadas organizadas por domínio:
  - **Laravel & Backend**: `laravel-saas`, `laravel-inertia-react`, `api-rest`, `eloquent-queries`, `filament-admin`, `pest-testing`, `redis-queue`, `reverb-websocket`, `security-audit`, `perf-optimizer`, `api-documentation`
  - **Browser & Automação**: `agent-browser`, `agentcore`, `dogfood`, `electron`, `slack`, `vercel-sandbox`
  - **Frontend & UI**: `modern-ui-ux`, `css-master`
  - **Infra & DB**: `docker-dev`, `database-pgsql`
  - **Workflow & Agentes**: `context-master`, `memory-helper`, `review-work`, `skill-creator`, `git-ops`, `stack-advisor`
- **Configuração de Agentes**: Sisyphus (orquestrador principal), Oracle, Prometheus, Metis, Momus

## Estrutura Real

```
my-config-opencode/
├── .gitignore                    # Ignora node_modules, temporários
├── README.md                     # Este arquivo
└── .opencode/                    # Diretório de configuração do OpenCode
    ├── opencode.json             # Configuração principal (plugins, MCPs)
    ├── oh-my-openagent.json      # Configuração de agentes e skills
    ├── magic-context.jsonc       # Configuração do magic-context
    ├── .gitignore                 # Ignora package.json, node_modules
    └── skills/                    # Habilidades especializadas
        ├── security-audit/           # Auditoria de segurança e OWASP
        ├── perf-optimizer/            # Otimização de performance Laravel/Postgres
        ├── api-documentation/         # Documentação OpenAPI/Swagger
        ├── agent-browser/            # Automação de navegador via CDP
        ├── agentcore/                # Browser em nuvem (AWS Bedrock)
        ├── api-rest/                 # Especialista em APIs REST/Laravel
        ├── context-master/           # Gestão de contexto avançada
        ├── css-master/               # Especialista em CSS/Styling
        ├── database-pgsql/           # Otimização e queries PostgreSQL
        ├── docker-dev/               # Orquestração e Docker
        ├── dogfood/                  # QA e testes exploratórios
        ├── electron/                 # Automação de apps Electron
        ├── eloquent-queries/         # Otimização de queries Eloquent
        ├── filament-admin/           # Especialista em FilamentPHP
        ├── git-ops/                  # Fluxos avançados de Git
        ├── laravel-inertia-react/    # Stack TALL/Inertia
        ├── laravel-saas/             # Padrões de arquitetura SaaS
        ├── memory-helper/            # Gestão de memória de longo prazo
        ├── modern-ui-ux/             # Design e UX moderno
        ├── pest-testing/             # Testes com Pest PHP
        ├── redis-queue/              # Filas e Cache com Redis
        ├── reverb-websocket/         # WebSockets com Laravel Reverb
        ├── review-work/              # Revisão técnica de código
        ├── skill-creator/            # Criação de novas skills
        ├── slack/                    # Automação de Slack
        ├── stack-advisor/            # Consultoria de stack tecnológica
        └── vercel-sandbox/           # Automação em Vercel Sandbox
```

## Como Instalar e Configurar

Siga estes passos para configurar o ambiente do Sisyphus no seu OpenCode.

### 1. Instalação do OpenCode
Baixe e instale a versão mais recente do OpenCode:
👉 [opencode.ai](https://opencode.ai)

### 2. Instalação do Bun (Runtime)
Os plugins do OpenCode são executados via Bun. Instale-o com o comando:
```bash
curl -fsSL https://bun.sh/install | bash
```

### 3. Instalação dos Plugins Globais
Instale os plugins essenciais para a orquestração e contexto:
```bash
# Plugin de orquestração de agentes
bun install -g oh-my-openagent
bunx oh-my-openagent install

# Plugin de contexto inteligente
bun install -g opencode-magic-context
bunx --bun @cortexkit/opencode-magic-context@latest setup

# Automação de navegador (Skill agent-browser)
npm i -g agent-browser && agent-browser install --with-deps
```

### 4. Aplicação da Configuração Customizada
Para aplicar as skills e configurações do Sisyphus:

**Via Clone (Recomendado):**
```bash
git clone https://github.com/Muamm4/my-config-opencode.git ~/my-config-opencode
cp -r ~/my-config-opencode/.opencode/* ~/.config/opencode/
```

**Via Cópia Manual:**
Copie todo o conteúdo da pasta `.opencode/` deste repositório para o seu diretório de configuração global:
`~/.config/opencode/`

### 5. Reiniciar o OpenCode
Feche e abra novamente o OpenCode para que todas as skills e configurações de agentes sejam carregadas.


## Pré-requisitos

- **OpenCode**: https://opencode.ai
- **Bun** ≥ 1.0 (para oh-my-openagent): https://bun.sh
- **Plugins globais**:
  - `oh-my-openagent`
  - `opencode-magic-context`


## Personalização

Edite os arquivos conforme necessário:

- `opencode.json` - Plugins e MCPs
- `oh-my-openagent.json` - Agentes e allocation de skills

## Referências

- OpenCode: https://opencode.ai
- Oh-My-OpenAgent: https://ohmyopenagent.com/
- Magic Context: https://github.com/cortexkit/opencode-magic-context
