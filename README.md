# OpenCode Setup - Plugins de Contexto e Memória

Documentação de instalação e configuração dos plugins para contexto infinito e memória cross-session.

## Pré-requisitos

- **Bun** ≥ 1.0: https://bun.sh
- **OpenCode**: https://opencode.ai

## Instalação dos Plugins

### 1. Oh-My-OpenAgent (OMO)

Plugin principal com agentes especializados (Sisyphus, Metis, Momus, etc.)

```bash
# Instalação via npm global
npm install -g oh-my-openagent@latest

# Ou via bunx
bunx oh-my-openagent@latest setup
```

### 2. Magic Context (@cortexkit/opencode-magic-context)

Plugin de gerenciamento de contexto com cache-aware infinite context e cross-session memory.

```bash
# Instalação via bunx
bunx @cortexkit/opencode-magic-context@latest setup
```

## Configuração

### Configuração Global (~/.config/opencode/opencode.json)

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "oh-my-openagent@latest",
    "@cortexkit/opencode-magic-context@latest"
  ],
  "compaction": {
    "auto": false,
    "prune": false
  }
}
```

### Configuração do Magic Context (~/.config/opencode/magic-context.jsonc)

```json
{
  "$schema": "https://raw.githubusercontent.com/cortexkit/opencode-magic-context/master/assets/magic-context.schema.json",
  "enabled": true,
  "historian": {
    "model": "opencode/minimax-m2.5-free"
  },
  "dreamer": {
    "enabled": true
  }
}
```

## Ferramentas Disponíveis

### Context Management (Magic Context)

| Ferramenta | Descrição |
|-----------|----------|
| `ctx_note` | Notas duráveis que persistem cross-session |
| `ctx_search` | Busca em memórias e conversas |
| `ctx_reduce` | Redução de contexto por tags §N§ |
| `ctx_memory` | Memória cross-session |
| `ctx_expand` | Expandir compartment comprimido |
| `session_list` | Listar todas as sessões |
| `session_read` | Ler mensagens de uma sessão |
| `session_info` | Ver metadados da sessão |
| `session_search` | Buscar em mensagens |

### Background Tasks

| Ferramenta | Descrição |
|-----------|----------|
| `background_output` | Coletar resultado de tarefa background |
| `background_cancel` | Cancelar tarefa background |

### Oh-My-OpenAgent

| Comando | Descrição |
|---------|----------|
| `/start-work` | Iniciar sessão Sisyphus |
| `/handoff` | Criar contexto para nova sessão |
| `/init-deep` | Inicializar base AGENTS.md |
| `/refactor` | Refatoração inteligente |
| `/remove-ai-slops` | Remover code smells de AI |

## Troubleshooting

### Doctor - Verificar Instalação

```bash
# Verificar Magic Context
bunx @cortexkit/opencode-magic-context@latest doctor

# Verificar Oh-My-OpenAgent
bunx oh-my-openagent@latest doctor
```

### Database

O Magic Context armazena dados em:
```
~/.local/share/opencode/storage/plugin/magic-context/context.db
```

### Logs

```
/tmp/magic-context.log
```

## MCPs Configurados

O Oh-My-OpenAgent já inclui MCPs built-in. Ative via configuração:

```json
{
  "mcps": {
    "context7": { "enabled": true },
    "grep_app": { "enabled": true },
    "websearch": { "enabled": true }
  }
}
```

### Browser Automation (Playwright MCP)

```json
{
  "browser_automation_engine": {
    "provider": "playwright"
  }
}
```

### MCPs Disponíveis

| MCP | Função |
|-----|--------|
| `websearch` | Buscas na web (Exa) |
| `context7` | Documentação |
| `grep_app` | Código no GitHub |
| `playwright` | Automação de.browser |

## Quick Setup (Recomendado)

Execute o wizard interativo que detecta modelos e configura tudo automaticamente:

```bash
# Linux/macOS
curl -fsSL https://raw.githubusercontent.com/cortexkit/opencode-magic-context/master/scripts/install.sh | bash

# Windows (PowerShell)
irm https://raw.githubusercontent.com/cortexkit/opencode-magic-context/master/scripts/install.ps1 | iex
```

## Skills - Habilidades Customizadas

### Diretório de Skills

Crie skills em:
- **Global**: `~/.config/opencode/skills/<nome-da-skill>/SKILL.md`
- **Projeto**: `.opencode/skills/<nome-da-skill>/SKILL.md`

### Estrutura de uma Skill

```
.opencode/skills/
└── memory-helper/
    └── SKILL.md
```

### Conteúdo do SKILL.md

```markdown
# memory-helper

## Identity
You are a memory specialist that helps manage cross-session context.

## Instructions
- Use ctx_note to persist important information
- Use ctx_search to find previous context before answering
- Use ctx_memory to write project-scoped memories
- Always save useful findings to memory for future sessions

## Tools Available
- ctx_note: persist session notes
- ctx_search: search memories and history
- ctx_memory: write/read cross-session memories
- session_list: list all sessions
- session_read: read session messages
```

### Configuração de Skills por Agente

No arquivo `oh-my-openagent.json`:

```json
{
  "agents": {
    "sisyphus": {
      "skills": ["memory-helper", "git-master", "frontend-ui-ux"]
    },
    "hephaestus": {
      "skills": ["frontend-ui-ux"]
    }
  },
  "disabled_skills": ["playwright"]
}
```

### Skills Disponíveis (Built-in)

| Skill | Descrição |
|-------|-----------|
| `playwright` | Browser automation |
| `git-master` | Commits atomic, rebase, blame |
| `frontend-ui-ux` | Design-first UI/UX |
| `review-work` | Post-implementation review |

### Skills Customizadas Incluídas

Este projeto já inclui 4 skills customizadas configuradas:

| Skill | Descrição |
|-------|-----------|
| `skill-creator` | Criar novas skills seguindo best practices |
| `stack-advisor` | Escolher stack/ferramentas |
| `laravel-inertia-react` | Laravel 13 + Inertia + React |
| `laravel-saas` | Seu projeto ~/saas com Octane/Swoole |
| `docker-dev` | Docker e containerização |
| `pest-testing` | Testes com Pest PHP |
| `database-pgsql` | PostgreSQL e pgvector |
| `redis-queue` | Redis para cache e filas |
| `api-rest` | REST APIs com Laravel + Sanctum |
| `reverb-websocket` | Laravel Reverb + WebSocket |
| `eloquent-queries` | Queries Eloquent avançadas |
| `filament-admin` | Admin panel com Filament |
| `playwright-automation` | Automação de browser com Playwright MCP |
| `memory-helper` | Gerenciamento de memória cross-session |
| `context-master` | Otimização de uso de contexto |
| `git-ops` | Operações git limpas e atômicas |
| `review-work` | Review pós-implementação |

## Configuração do Projeto

O arquivo `.opencode/oh-my-openagent.json` configura as skills por agente:

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-openagent/dev/assets/oh-my-opencode.schema.json",
  "agents": {
    "sisyphus": {
      "skills": ["memory-helper", "context-master", "git-ops"]
    },
    "hephaestus": {
      "skills": ["memory-helper", "git-ops"]
    }
  },
  "disabled_hooks": ["startup-toast"]
}
```

## Referências

- Magic Context: https://github.com/cortexkit/opencode-magic-context
- Oh-My-OpenAgent: https://github.com/oh-my-openagent/oh-my-openagent
- OpenCode: https://opencode.ai