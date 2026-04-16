# OpenCode Personal Config

Repositório de configuração pessoal do OpenCode com oh-my-openagent e skills customizadas para o agente Sisyphus.

## O Que Está Incluído

- **Plugins**: oh-my-openagent + magic-context (instalados globalmente)
- **Skills**: 17 habilidades customizadas para Laravel, Docker, PostgreSQL, etc.
- **Configuração de Agentes**: Sisyphus (orquestrador principal), Oracle, Prometheus, Metis, Momus

## Estrutura Real

```
my-config-opencode/
├── .gitignore                    # Ignora node_modules, temporários
├── README.md                     # Este arquivo
├── .opencode/                    # Diretório de configuração do OpenCode
│   ├── opencode.json             # Configuração principal (plugins, MCPs)
│   ├── oh-my-openagent.json      # Configuração de agentes e skills
│   ├── magic-context.jsonc       # Configuração do magic-context
│   ├── .gitignore                 # Ignora package.json, node_modules
│   └── skills/                    # 17 skills customizadas
│       ├── api-rest/
│       ├── context-master/
│       ├── css-master/
│       ├── database-pgsql/
│       ├── docker-dev/
│       ├── eloquent-queries/
│       ├── filament-admin/
│       ├── git-ops/
│       ├── laravel-inertia-react/
│       ├── laravel-saas/
│       ├── memory-helper/
│       ├── modern-ui-ux/
│       ├── pest-testing/
│       ├── playwright-automation/
│       ├── redis-queue/
│       ├── reverb-websocket/
│       ├── review-work/
│       └── skill-creator/
└── scripts/
    └── install.sh                # Script de instalação com smart merge
```

## Como Usar

### Instalação Rápida

```bash
# Clone e use o script de instalação
git clone https://github.com/Muamm4/my-config-opencode.git ~/.config/opencode
cd ~/.config/opencode
./scripts/install.sh
```

### Instalação Manual

```bash
# Copiar configuração para diretório global do OpenCode
cp -r .opencode/* ~/.config/opencode/
```

### Reiniciar o OpenCode

Feche e abra novamente o OpenCode para carregar as configurações.

## Pré-requisitos

- **OpenCode**: https://opencode.ai
- **Bun** ≥ 1.0 (para oh-my-openagent): https://bun.sh
- **Plugins globais**:
  - `oh-my-openagent`
  - `opencode-magic-context`

## Scripts

### install.sh

Script de instalação inteligente que:
- Faz backup da config existente
- Mantém arquivos protegidos (opencode.json, oh-my-openagent.json, magic-context.jsonc)
- Copia novas skills sem sobrescrever existentes
- Resume o que foi instalado

## Personalização

Edite os arquivos conforme necessário:

- `opencode.json` - Plugins e MCPs
- `oh-my-openagent.json` - Agentes e allocation de skills

## Referências

- OpenCode: https://opencode.ai
- Oh-My-OpenAgent: https://github.com/oh-my-openagent/oh-my-openagent
- Magic Context: https://github.com/cortexkit/opencode-magic-context