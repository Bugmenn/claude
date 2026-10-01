# Setup Padrão Claude Code — Global + Template de Projeto

Documento transferível. Contém **tudo** que é preciso para reproduzir o padrão de trabalho em outra máquina ou em um projeto novo:

- **Parte 1** — o `CLAUDE.md` **global**, que vale para todo projeto.
- **Parte 2** — o **template** do `CLAUDE.md` de cada projeto.
- **Parte 3** — blocos técnicos genéricos, para copiar no template conforme a stack.
- **Parte 4** — estrutura de memória e history.
- **Parte 5** — checklist de validação.

---

## Parte 0 — Como usar

1. Copiar este arquivo para a máquina/projeto destino.
2. Substituir os placeholders em todo o conteúdo colado:
   - `<USUARIO>` — usuário do sistema operacional (ex.: o dono do home `C:\Users\...` ou `~`).
   - `<WORKSPACE>` — diretório raiz onde ficam os projetos (ex.: `C:\workspace`, `~/dev`).
   - `<PROJETO>` — nome do repositório sendo configurado.
3. Criar a **Parte 1** em `<USUARIO_HOME>/.claude/CLAUDE.md`.
   ⚠️ **É `~/.claude/CLAUDE.md`, não `~/CLAUDE.md`.** O Claude Code só carrega o primeiro; um arquivo no home fica órfão e dá a falsa impressão de estar valendo.
4. Para cada projeto, criar a **Parte 2** em `<WORKSPACE>/<PROJETO>/CLAUDE.md`, preenchendo as seções e colando da **Parte 3** só os blocos que a stack daquele projeto usa.
5. Rodar o checklist da **Parte 5**.

> Nada aqui contém credencial, host, nome de cliente ou dado de ambiente — e nada aqui deve passar a conter.

---

# Parte 1 — `~/.claude/CLAUDE.md` (global)

Conteúdo agnóstico de stack e de projeto. Vale em **toda** sessão, em **todo** repositório.

---INICIO DO ARQUIVO ~/.claude/CLAUDE.md---

# CLAUDE.md — Regras globais de trabalho

Estas regras valem para todo projeto nesta máquina. Regras específicas de um repositório ficam no `CLAUDE.md` daquele repositório e, em caso de conflito, **a regra do projeto vence**.

---

## Regra 00 — Ler antes de agir (executar ANTES de qualquer coisa)

1. A **primeira ação de toda sessão** é carregar o contexto do projeto: `CLAUDE.md` do repositório, arquivo/pasta de memória (`.memory`, `MEMORY.md`, `memory/`, `Memory/`) e as diretrizes do projeto, se existirem.
2. **Ler não basta — aplicar.** Se a memória registra uma restrição ou proibição, ela vale para a sessão inteira, não só para a tarefa que a originou.
3. **Nunca afirmar que algo "não existe"** (arquivo, endpoint, serviço, migration, teste, configuração) sem antes rodar `Grep`/`Glob`. Ordem obrigatória: grep no projeto → memória → `CLAUDE.md` → só então concluir.
4. Antes de supor que trabalho está perdido, não commitado ou isolado em outra branch: `git log --oneline`, `git status -sb`, `git worktree list`.

---

## Regra 01 — Informar o modelo em execução

Toda resposta começa com:

`**Modelo: [Haiku | Sonnet | Opus]** — [motivo em uma linha]`

Exemplos:
- `**Modelo: Haiku** — busca de arquivos e leitura de contexto`
- `**Modelo: Sonnet** — implementação de nova funcionalidade`
- `**Modelo: Opus** — Sonnet falhou 2x, escalando`

Nunca inventar o nome: anunciar o modelo que está de fato rodando.

---

## Regra 02 — Escolha do modelo (automática, sem perguntar)

| Situação | Modelo |
|---|---|
| Busca, leitura, grep, exploração de código | **Haiku** |
| Implementação, refactoring, análise lógica, geração de código | **Sonnet** |
| Sonnet falhou 2 vezes na mesma tarefa | **Opus** |

Nunca Opus como primeira escolha. A troca é automática — não pedir autorização, só anunciar (Regra 01).

**Como isso se executa na prática:** o agente não troca o modelo da sessão principal sozinho; o que ele controla é o modelo dos **subagentes** que dispara (parâmetro `model` da ferramenta `Agent`). Então a regra se cumpre delegando busca/leitura/exploração a subagente **Haiku**, mantendo a implementação no **Sonnet**, e escalando a subagente **Opus** depois de duas falhas. Quando a troca precisar valer para a sessão inteira, dizer isso ao usuário em uma linha (`/model`) em vez de seguir em silêncio no modelo errado.

---

## Regra 03 — Idioma

- Responder **sempre em pt-BR**, independente do idioma da pergunta ou do código.
- **Texto visível ao usuário final** (labels, mensagens de erro exibidas, e-mails, notificações, tooltips) em **pt-BR com acentuação correta** e UTF-8.
- Código, nomes de arquivo, commits e comentários técnicos seguem **o padrão já existente no arquivo tocado** — não introduzir um idioma novo no meio de um arquivo que já usa outro.

---

## Regra 04 — Leitura cirúrgica e economia de token

- `Grep`/`Glob` **antes** de `Read`, para localizar o trecho exato.
- Ler com `offset`/`limit` em volta do ponto de interesse. Arquivo grande não se lê inteiro sem necessidade explícita.
- Paralelizar chamadas de ferramentas independentes no mesmo turno.
- **Nunca repetir código já mostrado** — referenciar por `arquivo:linha`.
- Respostas curtas e diretas, sem preâmbulo e sem recapitular o que acabou de ser feito. Fala completa só em: relatório de entrega, documentação gerada, resposta a pergunta aberta e bloqueio real que exige decisão humana.
- Não re-explorar contexto já conhecido na sessão.

---

## Regra 05 — Autorização (projeto de cliente / código de terceiros)

**Nunca** executar sem autorização explícita do usuário **na conversa atual**:

- `git commit`, `git push`, merge, rebase, `cherry-pick` ou qualquer reescrita de histórico;
- deploy, publicação, release;
- alteração de dados em massa em banco;
- `DELETE`/`UPDATE` sem chave primária explícita;
- reinício de serviço compartilhado, abertura de porta/firewall, rotação de chave.

Autorização de uma sessão **não vale** para a próxima.

Tarefa concluída com arquivos alterados e sem autorização de commit: entregar o relatório, **listar os arquivos pendentes e parar**. Não "resolver" commitando por conta própria.

> "Posso prosseguir?" é barato; dado corrompido ou histórico reescrito não.

---

## Regra 06 — Nunca descartar trabalho não commitado

Antes de qualquer comando que possa descartar mudanças (`git checkout -- .`, `git reset --hard`, `git clean`), rodar **só** `git status --short` / `git diff` primeiro. Nunca encadear um comando destrutivo "de passagem" junto com uma checagem de rotina — isso apaga na hora todas as mudanças não commitadas em arquivos rastreados, sem aviso.

Quando uma chamada de ferramenta for **rejeitada pelo usuário**, nunca rodar um comando destrutivo em seguida para "limpar" ou "conferir o estado". Só leitura, e esperar instrução.

---

## Regra 07 — Segredos

- **Nunca imprimir valor** de senha, token, chave ou connection string em resposta, log de ferramenta, documentação, PR ou arquivo de memória. Referenciar a origem ("a senha da conexão X"), nunca o valor.
- Credenciais vivem fora do código versionado (arquivo gitignored ou pasta de secrets), nunca dentro da pasta de código enviada em deploy.
- Configuração de desenvolvimento **nunca** vai para produção.
- Se o código passar a ler uma configuração nova, ela precisa existir no ambiente-alvo **antes** do deploy. "Credencial inválida" depois de um deploy é, antes de tudo, suspeita de configuração ausente ou desatualizada — diagnosticar configuração e log de inicialização antes de mexer em código.

---

## Regra 08 — Revisão final da entrega

Ao terminar qualquer mudança de código — mesmo sem o usuário pedir — rodar a revisão **uma vez só**, antes de reportar conclusão:

1. Rodar as revisões **em paralelo** (skill de code review + subagente revisor, quando existirem no projeto), esperar todas, juntar os achados numa lista só e **então** corrigir tudo de uma vez.
2. **Nunca** rodar o review de novo logo após corrigir um achado — isso vira loop (rodar → corrigir 1 → rodar → corrigir 1 → …) revisando o mesmo diff em fatias. Segunda rodada completa só se a correção mudou o código de forma substancial.
3. **Pelo menos uma das revisões é cega:** sem receber o enquadramento do que foi corrigido, instruída a descobrir pelo código o que a mudança se propõe a fazer e a não tratar revisões anteriores como aprovação prévia. Revisor que recebe a narrativa tende a validar a narrativa.
4. **Nenhum achado é fechado por raciocínio.** Cada um sai com uma destas três saídas, sempre com evidência:
   - **Corrigido** — mostrar o que mudou;
   - **Validado como não aplicável** — com a medição que prova (query, plano de execução, leitura do código que contradiz a premissa, teste rodado). Citar o número, não a conclusão;
   - **Escalado ao usuário** — quando for decisão de produto/negócio, não técnica.
   Se não der para validar no ambiente local, dizer explicitamente "não verificável aqui porque X".
5. Achado que reaponta uma decisão de design já discutida e aprovada não é bug: registrar como "já decidido" e ainda assim comunicar que o review resurfaceou o ponto.

---

## Regra 09 — Definição de entrega

- Entrega completa = sem TODO/stub/placeholder, build com 0 erros, testes e revisão (Regra 08) executados, memória atualizada, arquivos temporários removidos.
- **Build limpo é necessário, não suficiente** — validar o comportamento real (rodar a rotina, a tela, o endpoint) antes de reportar concluído.
- Antes de fechar, revisar brechas: fluxos não cobertos, edge cases, validação ausente, uso inesperado. Listar e corrigir.
- O **módulo central** é implementado primeiro; "em breve" não substitui a funcionalidade principal.
- **Relatar com fidelidade:** teste falhou → mostrar a saída; passo pulado → dizer; entrega parcial → **PARCIAL**, nunca "concluído".
- Relatório final autônomo: causa, o que foi feito, o que falta, o que o usuário precisa fazer.

---

## Regra 10 — Onde registrar cada coisa

| Tipo de informação | Destino |
|---|---|
| **Pitfall técnico** (comportamento não óbvio do código/API, causa raiz de bug que pode repetir) | seção "Pitfalls" do `CLAUDE.md` **do projeto** |
| **Decisão arquitetural, padrão novo, restrição de infra** | `CLAUDE.md` do projeto (ou `.claude/docs/<dominio>.md` se for detalhe de um domínio só) |
| **Preferência / processo de trabalho do usuário** | memória do agente (genérica, sem dado do projeto) |
| **Histórico do que foi feito** (commit, arquivo, atividade) | arquivo de memória/history do projeto |
| **Procedimento repetível com armadilhas próprias** | skill |

Regras de higiene:

- **Não duplicar.** Se já está documentado, não repetir em outro lugar.
- **Skill vence memória.** A memória do projeto é registro histórico do que foi feito — inclusive de práticas já corrigidas depois. Antes de executar um fluxo que tem skill, abrir a skill, mesmo achando que já sabe o procedimento. Se a memória contradisser a skill, corrigir a memória na hora.
- **Criar/atualizar skill ou agente é automático**, sem pedir autorização — mas nunca silencioso: mencionar no relatório final o que foi criado/alterado. Justifica skill nova: o fluxo se repete no projeto **e** tem armadilha que já causou problema real. Caso isolado vira pitfall, não skill. Agente novo é exceção — preferir estender skill.
- Instrução **errada** numa skill é pior que instrução ausente, porque é seguida com confiança: ao descobrir que uma afirmação da skill é falsa, **corrigir a afirmação**, não só acrescentar ao lado.

---

## Regra 11 — Autoaperfeiçoamento, sem esperar ser cobrado

Ao fechar qualquer tarefa, fazer a checagem: *"o que aprendi aqui que a próxima sessão vai ter de redescobrir?"*

- Procedimento → vira/atualiza skill.
- Fato sobre o código → `CLAUDE.md` do projeto.
- Preferência do usuário → memória do agente.

Isso é parte do encerramento da tarefa, não uma auditoria posterior e não depende de o usuário pedir.

---

## Regra 12 — Registro de fim de atividade

Ao concluir qualquer atividade, registrar no arquivo de memória do projeto:

---INICIO DO FORMATO---
### [YYYY-MM-DD] Título da Atividade
**O que foi feito:** descrição concisa
**Arquivos alterados:**
- `Caminho/Arquivo.ext` — o que mudou
- `Caminho/Novo.ext` *(novo)*
- Removido: `Caminho/Removido.ext`
---FIM DO FORMATO---

**Compactação obrigatória.** Antes de adicionar qualquer registro, verificar o tamanho do arquivo de memória. Passando do teto definido para o projeto (ex.: 800 linhas):

- manter intactas as seções de contexto fixo (stack, arquitetura, padrões, pitfalls);
- fundir registros antigos em `### [período] Histórico Compactado`, preservando só decisão arquitetural, padrão novo e pitfall com valor duradouro;
- descartar registro rotineiro sem impacto arquitetural.

Sessão que termina antes do fim: registrar o pendente, com o que falta e por quê.

---FIM DO ARQUIVO ~/.claude/CLAUDE.md---

---

# Parte 2 — Template do `CLAUDE.md` de projeto

Criar em `<WORKSPACE>/<PROJETO>/CLAUDE.md`.

> **Regra de higiene do arquivo:** o `CLAUDE.md` é carregado **inteiro em toda conversa**. Só entra aqui o que muda decisão em **qualquer** tarefa. Detalhe de um domínio só vai para `.claude/docs/<dominio>.md`, lido sob demanda. Quando um pitfall tem a causa raiz resolvida, ele encolhe para uma linha — não vira histórico da investigação.

---INICIO DO TEMPLATE `<PROJETO>/CLAUDE.md`---

# CLAUDE.md — <PROJETO>

Guia para o Claude Code neste repositório.

## Leitura obrigatória ao iniciar

Antes de qualquer tarefa, ler nesta ordem:
1. <caminho da memória do agente, se houver>
2. <caminho do arquivo de memória do projeto>
3. <outras diretrizes do projeto>

Regras gerais de processo estão no `CLAUDE.md` global — não duplicar aqui. O que segue é específico deste repositório.

## Autorização específica deste projeto

<O que, além da Regra 05 global, exige autorização explícita aqui — ex.: alteração em base compartilhada, reinício de worker, publicação de pacote. E a convenção de commit do projeto: o que entra e o que nunca entra no commit.>

## Comandos essenciais

---INICIO DOS COMANDOS---
# build
# testes (e como rodar um teste específico)
# lint
# subir a aplicação / cada host
# migrations / tarefas de manutenção
---FIM DOS COMANDOS---

<Armadilhas de nomenclatura: pasta ≠ nome do projeto/pacote, host com argumento obrigatório, versão de runtime fixada etc.>

## Arquitetura

<Só o que **não** se descobre lendo os arquivos: camadas e a direção das dependências; o que cada projeto/módulo responde; o fluxo difícil de reconstruir arquivo por arquivo (ex.: como um job agendado chega a executar); projetos órfãos que existem no disco mas não compilam.>

**Stack:** <runtime> · <banco> · <ORM> · <fila/real-time> · <cache> · <storage>

## Padrões críticos

<Padrões que valem para a codebase inteira. Copiar da Parte 3 os blocos que se aplicam e adaptar ao projeto.>

## Pitfalls conhecidos

- **<Título curto>:** <o que engana + como evitar>

## Skills e agentes deste projeto

| Skill / Agente | Quando usar |
|---|---|
| <nome> | <gatilho> |

<Quando duas tiverem escopo parecido, dizer em uma linha o que separa uma da outra.>

## Documentação por domínio (`.claude/docs/`)

| Arquivo | Quando abrir |
|---|---|
| `.claude/docs/<dominio>.md` | <quando a tarefa entrar nesse assunto> |

## Credenciais locais

<Nunca o valor — só a origem e onde ele vive. Se este arquivo for gitignored e guardar valor, dizer isso explicitamente aqui.>

---FIM DO TEMPLATE `<PROJETO>/CLAUDE.md`---

---

# Parte 3 — Blocos técnicos genéricos

Copiar para a seção "Padrões críticos" do projeto **apenas os blocos que a stack usa**, adaptando nomes.

## 3.1 — Alteração manual de dados (regra canário)

Vale para qualquer `UPDATE`/`DELETE` rodado à mão em qualquer banco, **inclusive limpeza de teste**:

1. **Backup antes** — `SELECT` dos registros afetados salvo, ou cópia da tabela/coleção (`<nome>_bkp_<desc>_<data>`).
2. **Um registro primeiro**, por chave primária confirmada por consulta prévia. Validar o efeito na tela/rotina, esperando o ciclo mais lento de quem lê aquele dado.
3. **Só então aplicar no conjunto.** Se houver job/cron/worker lendo a coluna: parar → migrar → validar → religar.
4. **Filtro amplo** (sem chave primária, ou por tipo/categoria) exige **confirmação explícita** do usuário — valor de enum/flag errado apaga o registro de outro tipo sem aviso.
5. Correção aplicada no **caminho de escrita** cobre só registros **futuros**. Consultar o banco pelos legados e planejar o backfill — preferir comando de manutenção idempotente a migration.

## 3.2 — ORM, migrations e SQL manual

- Com mais de um contexto/conexão na solução, **sempre nomear o contexto** explicitamente no comando de migration.
- **Nunca** marcar uma migration como aplicada sem executar a DDL correspondente: histórico "sincronizado" com schema faltando vira erro de coluna inexistente em runtime.
- **Nome de coluna real pode divergir da propriedade** do modelo. Antes de escrever SQL manual, conferir no catálogo do banco (ou grepar a configuração da entidade) em vez de assumir.
- Banco restaurado de backup antigo: rodar migrations **antes** de liberar login — schema defasado quebra a autenticação.
- Em arquitetura multi-base, **nunca** aplicar migration direto numa base; usar o mecanismo da aplicação que percorre todas.
- Teste **nunca** reaproveita conta de uso geral (`dev@`, `admin@`, `teste@`) — criar usuário dedicado; conta genérica pode estar em uso real.
- Diferença fixa de N horas entre dois timestamps costuma ser fuso de apresentação, não duplicidade — comparar os valores antes de concluir que há registro duplicado.
- Repositório/cliente de banco com ciclo de vida errado (singleton × escopo) é causa clássica de erro intermitente de concorrência.

## 3.3 — Contrato controller ↔ camadas ↔ frontend

- Regra de negócio **não fica em controller**: Controller → Application → Domain → Infra. O controller valida entrada, chama o caso de uso e devolve.
- DTO de entrada com validação de obrigatoriedade e tamanho; **coleções sempre inicializadas**, nunca nulas.
- A **rota declarada é a fonte de verdade** do nome do endpoint — o front se adapta a ela, não o contrário.
- **Campo novo num DTO precisa ser mapeado explicitamente nos dois lados** (mapeador do backend e cliente HTTP do front). Campo não mapeado é descartado **sem erro** — falha silenciosa, a pior classe.
- Nome de query param no front **idêntico** ao do backend; parâmetro extra é ignorado em silêncio.
- Data vinda de campo local chega **sem timezone** — converter explicitamente do fuso do usuário para UTC no backend, nunca assumir que já é UTC.
- Documentação interativa de API e qualquer bypass de validação para teste só sob verificação de ambiente de desenvolvimento **no código** — configuração sozinha nunca habilita comportamento simulado.
- Falha de notificação (e-mail, mensagem) **depois** de persistir a alteração é **logada, não relançada** — a operação principal já aconteceu.
- Mutação de crédito/saldo/acesso: transição **atômica e idempotente**, com o registro de histórico no mesmo fluxo. Teste negativo não substitui teste de repetição/concorrência.
- Um "fast path" que duplica decisão de domínio é testado e corrigido **junto** com o caminho canônico.

## 3.4 — Frontend e UX

- **Respeitar a versão de runtime fixada pelo projeto** (`.nvmrc`/`package.json`). Build com versão errada gera bundle que quebra no boot e parece outro problema. Não commitar lockfile reescrito por versão errada.
- **Rodar o build de produção antes de fechar tarefa de front** — o lint sozinho não basta: há regra que só é erro em produção.
- Em produção: sem source map servido; o servidor nunca entrega artefato de depuração nem metadados de repositório.
- Depois de publicar, validar em aba nova com recarga forçada que o bundle carregado é o do build novo (cache/CDN servem o índice antigo).
- Modal fecha por botão explícito, nunca por clique no fundo. Nunca diálogo nativo do navegador — componente próprio, acessível e automatizável.
- Armazenamento local sempre por um serviço central; token conforme a opção "lembrar-me".
- Login: tentativas sucessivas → desafio → bloqueio temporário com contagem; mensagem **genérica**, sem revelar se o usuário existe; erro de autenticação mostra mensagem, nunca redireciona em silêncio.
- Mostrar/ocultar senha em todo campo de senha; medidor de força na criação/troca.
- Máscara e validação de documento/telefone/CEP; todo campo de texto com limite, espelhado pela validação do backend.
- Guard valida a **expiração real** do token; interceptor trata expiração em rota protegida com renovação ou logout.
- Auditoria de login nunca armazena senha tentada, foto ou captura de câmera.

## 3.5 — Testes

- Bug corrigido → **teste que reproduz o bug** e verifica a correção. Sem "depois".
- Tela/fluxo novo → validação E2E real **na mesma sessão** (render, campos, eventos, validação, fluxo crítico).
- Caso que depende de fixture controlada falha/pula explicitamente quando a fixture não existe — suíte verde não pode mascarar fluxo não testado.
- Conferir **na saída** que os testes executaram: projeto mal configurado ou filtro que não casa dá build verde com zero testes rodados.

## 3.6 — Deploy

**Antes:** build em pasta temporária **fora** da árvore de código; mapear artefato → unidade de serviço → pasta de destino e conferir no servidor/proxy qual pasta recebe o quê (componentes distintos nunca compartilham destino); excluir do pacote configuração, fonte, símbolos de depuração e metadados de repositório.

**Durante:** guardar toda variável de destino antes de usá-la em comando destrutivo (`[ -n "$VAR" ] && [ -d "$VAR" ] || exit 1`, sempre entre aspas); limpeza do destino preservando o que é do servidor (configuração, logs); reiniciar a unidade exata e aguardar o cold start antes de julgar um erro.

**Depois (obrigatório):** listar o destino e procurar conteúdo errado; comparar hash do artefato local × servidor; **HTTP 200 na URL real**, não só "serviço ativo"; log de inicialização sem erro de configuração; remover temporários; relatar em tabela `componente | URL | pasta | HTTP | status`.

## 3.7 — Rede

- **Nunca abrir porta** (firewall, painel, publicação de porta de container, bind em `0.0.0.0`) sem autorização explícita na conversa atual. A resposta padrão é o proxy já existente.
- Serviço escuta em loopback e é exposto só via proxy. Túnel reverso com destino explícito em loopback.
- Nunca desativar proteção (rate limit, CORS, CSP, banimento por tentativa) para "destravar" — investigar e configurar.
- Depois de mudar regra de rede, **testar a conectividade antes de encerrar a sessão remota**.
- Acesso não deve depender de lista branca de IP dinâmico de cliente.

---

# Parte 4 — Estrutura de memória

| Arquivo | Papel |
|---|---|
| `~/.claude/CLAUDE.md` | regras globais de processo (Parte 1) — **carregado em todo projeto** |
| `<WORKSPACE>/<PROJETO>/CLAUDE.md` | contexto fixo do repositório (Parte 2) — carregado inteiro em toda conversa daquele projeto |
| `.claude/docs/<dominio>.md` | detalhe por domínio — lido **sob demanda** |
| memória do projeto (`.memory` / `memory/`) | o que guia a próxima sessão: padrão de arquitetura fixo, regra de negócio que afeta implementação, restrição, causa raiz de bug que pode repetir |
| `history/<projeto>.history` | log permanente append-only do que foi feito |

Bloco do history:

---INICIO DO FORMATO HISTORY---
## [DD/MM/AAAA HH:MM] — <título>
- O que foi feito: ...
- Commits: <hashes ou "nenhum (aguardando autorização)">
- Arquivos alterados: ...
- Decisões: ...
- Resultado: COMPLETED | PARTIAL | BLOCKED
- Lição (se houver): ...
---FIM DO FORMATO HISTORY---

**Checklist de encerramento de sessão:**
1. Memória e history atualizados (Regras 10–12).
2. Build limpo e testes executados, com a saída conferida.
3. `git status` revisado; commit **somente com autorização**; arquivos pendentes listados no relatório.
4. Deploy validado, se autorizado, com a auditoria pós-deploy.
5. Nenhum arquivo temporário local ou remoto.
6. Relatório final autônomo.

---

# Parte 5 — Validação

Na máquina destino, conferir:

1. `~/.claude/CLAUDE.md` existe, **não está vazio**, e contém as Regras 00–12.
   ⚠️ Se existir também um `~/CLAUDE.md`, ele **não é lido** — ou apagar, ou deixar nele só uma linha apontando para `~/.claude/CLAUDE.md`, para ninguém editar o arquivo errado.
2. Cada projeto ativo tem `CLAUDE.md` na raiz, preenchido (não o template cru).
3. Cada projeto ativo tem arquivo de memória, e o `CLAUDE.md` do projeto diz onde ele fica.
4. Nenhum placeholder `<USUARIO>` / `<WORKSPACE>` / `<PROJETO>` sobrou nos arquivos criados.
5. Nenhum valor de credencial foi copiado para dentro de arquivo versionado.
6. Abrir uma sessão nova e confirmar que o agente anuncia o modelo (Regra 01) e lê a memória antes de agir (Regra 00).
