# Claude Code — Skills, Agentes e Configuração

Repositório pessoal com as skills, subagentes e arquivo de setup que uso com o [Claude Code](https://docs.claude.com/en/docs/claude-code/overview).

## Estrutura

```
claude/
├── SETUP_CLAUDE_PADRAO.md            # Padrão de configuração para uma máquina/projeto novo
├── skills/                           # Skills do Claude Code (.claude/skills/)
│   ├── arquitetura-projeto/
│   │   └── SKILL.md
│   ├── frontend-design/
│   │   ├── SKILL.md
│   │   └── LICENSE.txt
│   ├── otimizacao-codigo/
│   │   └── SKILL.md
│   └── revisao-final/
│       └── SKILL.md
└── agents/                           # Subagentes do Claude Code (.claude/agents/)
    ├── depurador.md
    ├── descritor-pr.md
    ├── documentador.md
    ├── especialista-banco.md
    ├── revisor-codigo.md
    └── testador.md
```

## Como instalar

### Skills

Copie a pasta da skill desejada para dentro de `.claude/skills/` do seu projeto, ou para `~/.claude/skills/` para valer em todos os projetos:

```bash
cp -r skills/arquitetura-projeto ~/meu-projeto/.claude/skills/
cp -r skills/otimizacao-codigo ~/meu-projeto/.claude/skills/
cp -r skills/frontend-design ~/meu-projeto/.claude/skills/
cp -r skills/revisao-final ~/meu-projeto/.claude/skills/
```

- **`arquitetura-projeto`** — mapeia a estrutura do projeto, avalia o desenho (camadas, acoplamento, coesão, direção de dependências) e propõe mudanças arquiteturais quando fizer sentido, priorizadas por impacto, risco e esforço. Também atende projeto novo ou decisão de design: levanta requisitos e registra a decisão em ADR.
- **`otimizacao-codigo`** — analisa código em busca de oportunidades de otimização: complexidade algorítmica, uso de memória, leitura e uso de recursos, acesso a banco, duplicação, complexidade desnecessária e boas práticas da linguagem/framework. Classifica cada achado como confirmado ou provável e propõe um teste que fixa o comportamento antes de refatorar código sem testes.
- **`revisao-final`** — revisão de fechamento de uma mudança (Regra 08 do `SETUP_CLAUDE_PADRAO.md`): dispara o `revisor-codigo` duas vezes em paralelo, uma delas cega, junta os achados, corrige tudo numa passada só e fecha cada achado com evidência (corrigido, não aplicável ou escalado). Depende dos agentes `revisor-codigo` e, quando houver banco ou testes envolvidos, `especialista-banco` e `testador`.
- **`frontend-design`** — orienta o design visual ao criar ou refazer uma interface: direção estética, tipografia e escolhas que não pareçam template padrão. Tem licença própria (Apache 2.0, em `LICENSE.txt`).

### Agentes

Copie os arquivos `.md` direto para `.claude/agents/` (projeto) ou `~/.claude/agents/` (todos os projetos):

```bash
cp agents/*.md ~/.claude/agents/
```

- **`revisor-codigo`** — revisa PRs, branches, mudanças recentes ou diffs específicos, sem modificar código. Confere `CLAUDE.md` e histórico, dá nota de confiança a cada achado para cortar falsos positivos e fecha com um veredito.
- **`depurador`** — investiga erros, exceptions e testes falhando até a causa raiz antes de corrigir, e escreve um teste que reproduz o bug.
- **`documentador`** — gera e atualiza documentação a partir do código real: README, Javadoc/docstrings, documentação de API, runbooks, documentação de arquitetura e guias de onboarding.
- **`especialista-banco`** — queries SQL, migrations, stored procedures/functions e otimização de schema e índices.
- **`testador`** — escreve e roda testes seguindo o que o projeto já usa: teste que reproduz um bug (e confirma que ele falha antes da correção), testes de código novo e levantamento do que falta cobrir. Sempre confere na saída quantos testes realmente executaram.
- **`descritor-pr`** — escreve a descrição de uma Pull Request a partir do diff: contexto, o que mudou e como testar.

### Configuração de máquina nova

O arquivo `SETUP_CLAUDE_PADRAO.md` reúne tudo para reproduzir o padrão de trabalho em outra máquina ou projeto:

- o `CLAUDE.md` global, que vai em `~/.claude/CLAUDE.md`;
- o template do `CLAUDE.md` de cada projeto;
- blocos técnicos por stack (banco, ORM, contrato com o frontend, testes, deploy, rede);
- a estrutura de memória e history;
- um checklist de validação.

As regras cobrem idioma, escolha de modelo, economia de tokens, autorização para commit/deploy, segredos e revisão final.

## Licença

Uso pessoal — sinta-se à vontade para adaptar para o seu próprio fluxo de trabalho. A skill `frontend-design` segue a licença do seu próprio `LICENSE.txt`.
