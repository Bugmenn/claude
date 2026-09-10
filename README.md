# Claude Code — Skills, Agentes e Configuração

Repositório pessoal com as skills, subagentes e arquivo de setup que uso com o [Claude Code](https://docs.claude.com/en/docs/claude-code/overview).

## Estrutura

```
claude-code/
├── config claude.md          # Prompt de setup para configurar uma máquina/projeto novo
├── skills/                   # Skills do Claude Code (.claude/skills/)
│   ├── arquitetura-projeto/
│   │   └── SKILL.md
│   └── otimizacao-codigo/
│       └── SKILL.md
└── agents/                   # Subagentes do Claude Code (.claude/agents/)
    ├── depurador.md
    └── revisor-codigo.md
```

## Como instalar

### Skills

Copie a pasta da skill desejada para dentro de `.claude/skills/` do seu projeto:

```bash
cp -r skills/arquitetura-projeto ~/meu-projeto/.claude/skills/
cp -r skills/otimizacao-codigo ~/meu-projeto/.claude/skills/
```

- **`arquitetura-projeto`** — mapeia a estrutura do projeto, avalia o desenho (camadas, acoplamento, coesão, direção de dependências) e propõe mudanças arquiteturais quando fizer sentido.
- **`otimizacao-codigo`** — analisa código em busca de oportunidades de otimização: complexidade algorítmica, uso de memória, leitura e uso de recursos, duplicação, complexidade desnecessária e boas práticas da linguagem/framework.

### Agentes

Copie os arquivos `.md` direto para `.claude/agents/` (projeto) ou `~/.claude/agents/` (todos os projetos):

```bash
cp agents/*.md ~/.claude/agents/
```

- **`depurador`** — investiga erros, exceptions e testes falhando até a causa raiz antes de corrigir.
- **`revisor-codigo`** — revisa PRs, branches, mudanças recentes ou diffs específicos, sem modificar código.

### Configuração de máquina nova

O arquivo `config claude.md` contém um prompt para colar no Claude Code de uma máquina nova, replicando a estrutura de memória, diretrizes e premissas de trabalho (idioma, regras de escolha de modelo, economia de tokens, etc.).

## Licença

Uso pessoal — sinta-se à vontade para adaptar para o seu próprio fluxo de trabalho.
