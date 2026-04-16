# OpenCode Personal Config

Repositório de configuração pessoal do OpenCode com plugins, MCPs e skills customizadas.

## O Que Está Incluído

- **Plugins**: oh-my-openagent + magic-context
- **MCPs**: context7, grep_app, websearch, playwright
- **Skills**: 17 habilidades customizadas para Laravel, Docker, PostgreSQL, etc.
- **Configuração de Agentes**: Sisyphus, Hephaestus, Oracle, Atlas, Prometheus

## Estrutura

```
.my-config-opencode/
├── .opencode/
│   ├── opencode.json          # Configuração principal
│   ├── oh-my-openagent.json  # Configuração de agentes
│   ├── mcp-playwright.json   # Configuração MCP Playwright
│   ├── agent-browser.json    # Configuração browser
│   ├── skills/               # 17 skills customizadas
│   └── package.json          # Dependências do projeto
├── .playwright-mcp/          # Arquivos temporários do Playwright
├── example-com.png           # Arquivo de teste
├── httpbin-org.png           # Arquivo de teste
└── README.md                 # Este arquivo
```

## Como Usar

### 1. Copiar para Configuração Global

```bash
# Copiar configurações para o diretório global do OpenCode
cp -r .opencode/* ~/.config/opencode/

# Ou clonar este repo diretamente no diretório de config
git clone https://github.com/seu-user/my-config-opencode.git ~/.config/opencode
```

### 2. Instalar Dependências

```bash
cd ~/.config/opencode
bun install
```

### 3. Reiniciar o OpenCode

Feche e abra novamente o OpenCode para carregar as configurações.

## Pré-requisitos

- **Bun** ≥ 1.0: https://bun.sh
- **OpenCode**: https://opencode.ai
- **Node.js** ≥ 18 (para MCPs)
- **Playwright** (para automação de browser)

## Quick Install

```bash
# Clone e configure automaticamente
git clone https://github.com/seu-user/my-config-opencode.git ~/.config/opencode
cd ~/.config/opencode
bun install
```

## Personalização

Edite os arquivos de configuração conforme necessário:

- `opencode.json` - Plugins e MCPs
- `oh-my-openagent.json` - Agentes e skills

## Troubleshooting

```bash
# Verificar instalação
bunx oh-my-openagent@latest doctor
bunx @cortexkit/opencode-magic-context@latest doctor
```

## Referências

- OpenCode: https://opencode.ai
- Oh-My-OpenAgent: https://github.com/oh-my-openagent/oh-my-openagent
- Magic Context: https://github.com/cortexkit/opencode-magic-context