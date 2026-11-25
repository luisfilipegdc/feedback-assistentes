# Plano: painel da coordenação para o Feedback Assistentes

Instruções para uma nova sessão do Claude Code. Cole este arquivo, ou o caminho dele, como primeira mensagem.

## Objetivo

Criar no **feedback-assistentes** um painel de admin protegido por senha (`painel.html`) e gravar as respostas no Supabase. O modelo é o painel que já existe no **feedback-integral**.

## Repositórios

| Repositório | Papel | Branch |
|---|---|---|
| `luisfilipegdc/feedback-assistentes` | onde a mudança será feita | `claude/gifted-pascal-0yxwih` (todo o trabalho até agora está aqui; ainda não há `main`) |
| `luisfilipegdc/feedback-integral` | modelo a copiar, **só leitura** | `main` (o painel está nos commits `281104a` e `9b1a60c`) |

Arquivos para estudar no `feedback-integral` (branch `main`):
- `dados.js`: dados compartilhados e a função `rpc(fn, args)`, que chama o Supabase via `/rest/v1/rpc/...` com a chave publicável.
- `index.html`: a função `sync()` envia o resultado com `rpc("feedback_integral_enviar", …)` quando a pessoa termina. Se falhar, tenta de novo com backoff (até 5 vezes), tenta outra vez no evento `online` e no próximo login, e guarda `state.synced`.
- `painel.html`: login com senha e `rpc("feedback_integral_painel", {p_senha})`. Mostra:
  - quem respondeu e quem está pendente;
  - a média de pontos e o próximo feedback;
  - um gráfico por material e a média por afirmação;
  - as respostas individuais;
  - exportação em CSV;
  - impressão da folha de cada pessoa, individualmente ou de todas.

  O e-mail `teste@maristabrasil.org` aparece com o selo "teste" e não entra nas contagens.
- `README.md`: a seção "Painel da coordenação".

## Estado atual do feedback-assistentes

- Tudo está num único `index.html`, sem `dados.js`. As respostas ficam só no `localStorage`, com a chave `feedback-assistentes:<email>`.
- As constantes ficam no próprio `index.html`:
  - `TEACHERS`: TESTE, Renata Diniz e Natália de Sousa;
  - `SECTIONS` e `QUESTIONS`: 18 afirmações em 6 eixos;
  - `OPTIONS`: de 4 a 1;
  - `COMMIT_Q` e `COMMIT_OPTS`: a pergunta de compromisso, com várias respostas e a opção "Outro" com texto;
  - `LOCAL`;
  - `RANGES`: as faixas 18–35, 36–48, 49–62, 63–66 e 67–72, ajustadas para o máximo de 72 pontos.
- O formato de `state` é `{email, answers:[18 números], commit:{choices:[], otherOn, other}, i, done, when}`. A lista final do compromisso sai de `commitList()`.

## Tarefas

1. **Supabase.** Use o mesmo projeto do feedback-integral (`frsidsdrexolvcfxzroe`) e confirme isso com o usuário antes de mexer.
   - Antes de criar algo, leia a tabela `feedback_integral_respostas` e as funções `feedback_integral_enviar` e `feedback_integral_painel`, com `list_tables` e `execute_sql` em `pg_proc` ou `pg_get_functiondef`. A ideia é copiar o mesmo padrão.
   - Crie via `apply_migration`, com nomes próprios para não misturar com o Integral:
     - **tabela `feedback_assistentes_respostas`** com estas colunas:
       - `email` (PK)
       - `nome`
       - `respostas int[]` (18 itens, cada um de 1 a 4)
       - `total`
       - `material`
       - `compromisso text[]`
       - `compromisso_outro text`
       - `enviado_em timestamptz`

       Ative a RLS, sem nenhuma policy de leitura para `anon`.
     - **`feedback_assistentes_enviar(p_email, p_nome, p_respostas, p_compromisso, p_compromisso_outro)`**: `security definer`, faz upsert por e-mail e valida o tamanho e os valores. O total e o material devem ser calculados **no servidor**, com as mesmas faixas de `RANGES`.
     - **`feedback_assistentes_painel(p_senha)`**: `security definer`, devolve todas as linhas se a senha conferir. Se não conferir, lança o erro `28P01`, igual ao Integral.
     - `grant execute` só dessas duas funções para `anon`.
   - **Senha:** pergunte ao usuário se é a mesma do painel do Integral ou outra. Não coloque a senha em nenhum arquivo do repositório nem no README; ela fica só dentro da função no banco.
   - No fim, rode `get_advisors` (security) e corrija o que aparecer.
2. **`dados.js`.** Mova para ele `TEACHERS`, `SECTIONS`/`QUESTIONS`, `OPTIONS`, `COMMIT_Q`/`COMMIT_OPTS`, `LOCAL`, `RANGES`, `ICONS` e `rpc()`, com a mesma URL e a mesma chave publicável do Integral. O `index.html` e o `painel.html` passam a carregar esse arquivo.
3. **`index.html`.** Adicione o `sync()` no padrão do Integral, enviando também o compromisso, e marque `state.synced`. Não pode quebrar quem já tem progresso salvo no `localStorage`.
4. **`painel.html`.** Adapte o painel do Integral:
   - use 18 afirmações agrupadas pelos 6 eixos, com a média por afirmação e por eixo;
   - use as faixas e os rótulos de `RANGES`, em vez dos rótulos fixos "até 24…";
   - adicione um quadro **"Compromissos"** com quantas pessoas marcaram cada opção e a lista dos textos de "Outro";
   - inclua o compromisso nas respostas individuais, no CSV e na folha impressa;
   - mantenha o tratamento do e-mail de teste;
   - use a chave `fa-painel` no `sessionStorage`.
5. **README.** Acrescente a seção do painel, sem a senha.
6. **Teste com Playwright** (o Chromium já vem instalado):
   - responda o formulário com `teste@maristabrasil.org` e confira que a linha chega ao Supabase;
   - abra o painel com a senha errada (deve aparecer "Senha incorreta.") e com a certa, e confira os números;
   - teste o CSV e a impressão.

   Depois apague a linha de teste do banco, ou deixe, se o usuário preferir.
7. **Git.** Faça o commit e o push no branch `claude/gifted-pascal-0yxwih`, ou no branch que a sessão indicar. Não abra PR sem pedido.

## Fora do escopo / não fazer

- Não alterar nada no repositório `feedback-integral` nem nas tabelas e funções `feedback_integral_*`.
- Não deixar a tabela legível diretamente pela internet.
