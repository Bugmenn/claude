# Prompt de Setup — Nova Máquina Claude Code

Cole este prompt inteiro no Claude Code da nova máquina para replicar toda a estrutura de memória, diretrizes e premissas.

---

## PROMPT PARA COLAR

```
Preciso que você configure nesta máquina a mesma estrutura de memória, diretrizes e premissas de trabalho que uso em outra máquina. Execute cada passo abaixo na ordem, confirmando cada criação de arquivo.

---

## PASSO 1 — CLAUDE.md Global

Crie o arquivo `C:\Users\<SEU_USUARIO>\CLAUDE.md` (substitua <SEU_USUARIO> pelo usuário atual do sistema) com o seguinte conteúdo exato:

---INICIO DO ARQUIVO CLAUDE.md GLOBAL---
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Regra 00 — Memória entre Sessões (executar ANTES de qualquer coisa)

1. Verificar se existe arquivo `.memory`, `MEMORY.md` ou pasta `memory/` no projeto
2. **Ler obrigatoriamente** antes de iniciar qualquer tarefa
3. Para projetos com pasta `Memory/` no workspace, ler `diretrizes.md` + o `.memory` do projeto envolvido

### Fim de cada atividade — preenchimento obrigatório
Registrar no arquivo de memória do projeto:

```
### [YYYY-MM-DD] Título da Atividade
**O que foi feito:** descrição concisa
**Arquivos alterados:**
- `Caminho/Arquivo.ext` — o que mudou
- `Caminho/Novo.ext` *(novo)*
- Removido: `Caminho/Removido.ext`
```

### Compactação obrigatória — 800 linhas
Antes de adicionar qualquer registro, verificar o número de linhas do arquivo de memória.
Se tiver **mais de 800 linhas**, compactar primeiro:
- Manter intactas as seções de contexto fixo (stack, arquitetura, padrões, pitfalls)
- Fundir registros antigos em `### [período] Histórico Compactado`, preservando apenas decisões arquiteturais, padrões novos e pitfalls com valor duradouro
- Descartar registros rotineiros sem impacto arquitetural

---

## Regra 01 — Informar Modelo Obrigatoriamente

**Toda resposta deve começar** com a linha indicando qual modelo está sendo usado:

```
**Modelo: [Haiku | Sonnet | Opus]** — [motivo em uma linha]
```

Exemplos:
- `**Modelo: Haiku** — busca de arquivos e leitura de contexto`
- `**Modelo: Sonnet** — implementação de nova funcionalidade`
- `**Modelo: Opus** — Sonnet falhou 2x, escalando`

---

## Regra 02 — Escolha do Modelo

| Situação | Modelo |
|---|---|
| Buscas, leituras, grep, exploração de código | **Haiku** |
| Implementação, refactoring, análise lógica, geração de código | **Sonnet** |
| Sonnet falhou 2 vezes na mesma tarefa | **Opus** |

Nunca usar Opus como primeira escolha. Escalar apenas após 2 falhas do Sonnet.

---

## Regra 03 — Economia de Tokens

- Ler **apenas o trecho relevante** — usar `offset` + `limit`; nunca ler arquivos grandes inteiros
- Usar `Grep` antes de `Read` para localizar o trecho exato
- Paralelizar chamadas de ferramentas independentes no mesmo turno
- **Nunca** repetir código já mostrado; referenciar por `arquivo:linha`
- Respostas curtas e diretas; sem resumo do que acabou de fazer
- Não re-explorar contexto já conhecido na sessão

---

## Regra 04 — Idioma

Responder **sempre em Português Brasil (pt-BR)**, independente do idioma da pergunta ou do código.

---

## Regra 05 — Diretrizes do Projeto

Sempre seguir as diretrizes específicas do projeto quando existirem. Procurar por:
- `<WORKSPACE>\Memory\diretrizes.md`
- `<WORKSPACE>\Memory\*.memory`
---FIM DO ARQUIVO CLAUDE.md GLOBAL---


---

## PASSO 2 — Pasta Memory

Crie a pasta `<CAMINHO_DO_WORKSPACE>\Memory\` onde <CAMINHO_DO_WORKSPACE> é o diretório raiz dos projetos nesta máquina (ex: `C:\workspace\` ou `~/workspace/`).


---

## PASSO 3 — Arquivo diretrizes.md

Dentro da pasta Memory criada, crie o arquivo `diretrizes.md` com o seguinte conteúdo (substituindo os caminhos pelo workspace desta máquina):

---INICIO DO ARQUIVO diretrizes.md---
# Diretrizes de Execução - Claude Code

## Idioma
- **SEMPRE** responder em Português Brasil (pt-BR), independente do idioma da pergunta

---

## Início de Cada Atividade — Obrigatório
1. **Ler** este arquivo `diretrizes.md`
2. **Ler** o `.memory` do projeto envolvido
3. Só então iniciar a tarefa com o contexto carregado

---

## Escolha do Modelo — Regra de Uso
| Situação | Modelo |
|---|---|
| Buscas, leituras, grep, exploração de código | **Haiku** (mais rápido e barato) |
| Implementação, refactoring, análise lógica | **Sonnet** |
| Sonnet falhou 2 vezes na mesma tarefa | **Opus** (escalada automática) |

> Usar Haiku sempre que a tarefa for investigativa/leitura. Só escalar para Sonnet quando for gerar ou modificar código. Opus apenas como último recurso.

---

## Informar Modelo — Obrigatório
Toda resposta deve começar com: `**Modelo: [Haiku | Sonnet | Opus]** — [motivo]`

---

## Economia de Tokens
- Ler **apenas o trecho relevante** (use `offset` + `limit`) — nunca ler arquivos grandes inteiros
- **NUNCA** repetir código já mostrado; referenciar por `arquivo:linha`
- Respostas curtas e diretas; sem resumo do que acabou de fazer
- Usar `Grep` antes de `Read` para localizar exatamente onde está o código
- Paralelizar chamadas de ferramentas independentes no mesmo turno
- Evitar re-explorar contexto já conhecido na conversa

---

## Fim de Cada Atividade — Preenchimento Obrigatório
Ao concluir qualquer atividade, **registrar no `.memory` do projeto** na seção `## Registro de Atividades Recentes`:

```markdown
### [YYYY-MM-DD] Título da Atividade
**O que foi feito:** Descrição concisa do que foi implementado/corrigido
**Arquivos alterados:**
- `Caminho/Do/Arquivo.cs` — o que foi alterado
- `Outro/Arquivo.cs` *(novo)* — se criado do zero
- Removido: `Arquivo/Removido.cs` — se deletado
```

Também atualizar `diretrizes.md` se houver nova regra ou padrão descoberto.

### Compactação Obrigatória — Regra de 800 Linhas
**Antes de adicionar qualquer registro**, verificar o número de linhas do `.memory`:
- Se o arquivo tiver **mais de 800 linhas**: compactar primeiro, depois adicionar o novo registro.
- **Como compactar:**
  1. Manter intactas as seções de contexto fixo (stack, arquitetura, padrões, pitfalls)
  2. Fundir registros antigos em `### [período] Histórico Compactado`, preservando apenas o que tem valor arquitetural
  3. Remover registros rotineiros que não acrescentam contexto duradouro

---

## Projetos nesta Máquina
<!-- PREENCHER: listar projetos, caminhos e links para os .memory -->
| Projeto | Caminho | Memory |
|---|---|---|
| [NomeProjeto] | [caminho] | [NomeProjeto].memory |
---FIM DO ARQUIVO diretrizes.md---


---

## PASSO 4 — Criar .memory para cada projeto

Para cada projeto existente nesta máquina, crie um arquivo `<NomeProjeto>.memory` dentro da pasta Memory.
Use o template abaixo como base e preencha com o contexto real de cada projeto:

---INICIO DO TEMPLATE .memory---
# [NomeProjeto] — Contexto do Projeto

## Identificação
- **Caminho:** `[caminho completo do projeto]`
- **Stack:** [tecnologias principais]
- **Tipo:** [Backend / Frontend / Worker / etc]

---

## Arquitetura

[Descreva as camadas, projetos ou módulos principais e como se relacionam]

---

## Padrões de Desenvolvimento

[Padrões específicos do projeto: nomenclatura, estrutura de pastas, convenções]

---

## Pitfalls / Cuidados

[Armadilhas conhecidas, comportamentos nɻo óbvios, decisões técnicas importantes]

---

## Registro de Atividades Recentes
<!-- PREENCHER AO FINAL DE CADA ATIVIDADE -->
---FIM DO TEMPLATE .memory---


---

## PASSO 5 — CLAUDE.md do Projeto

Para cada projeto, crie um `CLAUDE.md` na raiz do repositório com:
- Comandos de build, test, lint e run
- Arquitetura de alto nível (o que não é óbvio lendo os arquivos)
- Padrões críticos que afetam toda a codebase

Use o CLAUDE.md do projeto existente como referência de qualidade e profundidade.


---

## PASSO 6 — Validação

Após criar todos os arquivos, confirme:
1. `<USER_HOME>\CLAUDE.md` existe e contém as 5 regras (00 a 05)
2. `<WORKSPACE>\Memory\diretrizes.md` existe
3. Existe pelo menos um `.memory` para cada projeto ativo
4. Cada projeto ativo tem `CLAUDE.md` na raiz

Se algum estiver faltando, crie agora antes de continuar.
```

---

## Observações para adaptar na nova máquina

- Substituir `C:\workspace\` pelo caminho real do workspace na máquina destino
- Substituir `C:\Users\<SEU_USUARIO>\` pelo home do usuário na máquina destino
- No Linux/Mac o CLAUDE.md global fica em `~/.claude/CLAUDE.md` ou `~/CLAUDE.md`
- Os arquivos `.memory` devem ser preenchidos com o contexto real dos projetos existentes naquela máquina — não copiar cegamente os `.memory` desta máquina se os projetos forem diferentes
- Se os projetos forem os mesmos (MonitorContabil, MonitorFrontEnd), copiar os arquivos `MonitorContabil.memory` e `MonitorFrontEnd.memory` desta máquina diretamente para a pasta Memory da nova máquina
