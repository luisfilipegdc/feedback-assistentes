# Estojo de Talentos — Feedback Assistentes

Autoavaliação interativa e gamificada das assistentes (baseada no [feedback-integral](https://github.com/luisfilipegdc/feedback-integral)).

- Página única e estática (`index.html`), sem servidor nem instalação: abra o arquivo no navegador ou publique pelo GitHub Pages.
- A pessoa entra com o e-mail institucional e responde às 18 afirmações, organizadas em 6 eixos (Acima do esperado = 4 · Dentro do esperado = 3 · Mínimo esperado = 2 · Abaixo do esperado = 1).
- No fim vem a **pergunta de compromisso** (não vale pontos; dá para marcar várias opções, inclusive "Outro" com texto livre).
- O resultado mostra a pontuação, o material escolar, a data, o horário e o local do feedback e o compromisso escolhido.
- **Faixas (máximo de 72 pontos):** 18–35 Borracha · 36–48 Grampeador · 49–62 Lápis de Cor · 63–66 Lapiseira · 67–72 Tinta.
  A tabela original ia até 80 pontos (20–39 · 40–54 · 55–69 · 70–74 · 75–80); como 18 afirmações somam no máximo 72, as faixas foram ajustadas na mesma proporção. Para mudar, edite `RANGES` no `index.html`.
- **Impressão:** "Imprimir / salvar PDF" gera uma folha A4 com resultado, agendamento, compromisso e todas as respostas. Na tela inicial também há a "versão em branco" para responder no papel.
- O progresso fica salvo no navegador: se fechar a página no meio, é só entrar de novo com o mesmo e-mail.

Para alterar assistentes, datas ou horários, edite `TEACHERS`; perguntas em `SECTIONS`; local em `LOCAL`.
