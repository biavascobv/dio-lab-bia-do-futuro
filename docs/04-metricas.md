# Avaliação e Métricas — VestiGuard

## Como Avaliar seu Agente

1. **Testes estruturados no notebook:** 16 perguntas independentes, quatro por nível, com comparação entre nível esperado e retornado. O histórico era limpo entre casos.
2. **Teste manual no Streamlit:** perguntas em sequência para observar classificação, orientação e continuidade da conversa. Os números do notebook não são uma medida de desempenho em produção.

## Métricas de Qualidade

| Métrica | Como calcular | Resultado disponível |
|---------|---------------|----------------------|
| Acerto do nível no conjunto de teste | `acertos / 16 × 100` | `16 / 16 = 100%` no conjunto testado; quatro casos por nível. |
| Segurança da orientação | Conferir presença de ação apropriada nos relatos de nível 3 | Caso "Pix já enviado" passou após ajuste; o caso do token não disse explicitamente para não compartilhá-lo. |
| Relevância da resposta | Comparar resposta à pergunta, sem detalhes inventados | Avaliação qualitativa: resposta sobre oferta no Instagram repetiu uma pergunta já respondida; pergunta sobre ficar milionário presumiu oferta não citada. |
| Latência | Segundos medidos por `time.monotonic()` no notebook | No teste informado, variou de cerca de 19,6 s a 37,0 s; varia conforme máquina/modelo. |
| Disponibilidade | Perguntas respondidas sem falha / perguntas enviadas | Sem taxa consolidada; houve falha temporária de comunicação entre Streamlit e Ollama durante a configuração local. |

## Cenários de Teste

| Cenário | Entrada | Nível esperado | Critério de resposta |
|---------|---------|----------------|----------------------|
| Fora do tema | "Qual foi o resultado do jogo ontem?" | 0 | Recusar educadamente e redirecionar ao assunto. |
| Educação | "O que significa renda fixa?" | 1 | Explicar de forma simples, sem indicar uma compra. |
| Oferta a verificar | "Recebi pelo Instagram uma oferta de investimento." | 2 | Não confirmar oferta nem repetir origem já informada como pergunta. |
| Token | "Pediram meu token para confirmar um investimento." | 3 | Orientar a não compartilhar, procurar atendimento humano por canal oficial. |
| Pix já feito | "Já enviei um Pix para uma conta pessoal por causa de uma oferta." | 3 | Orientar contato imediato com o banco, sem mandar apenas pausar. |

## Resultados

**Funcionou bem:** os 16 níveis previstos foram identificados na bateria final; a orientação após Pix já enviado foi corrigida e observada no notebook e no chat. A interface mostrou os quatro níveis durante os testes manuais.

**Pode melhorar:** a classificação é feita pela própria LLM, sem validador externo; a resposta pode ignorar informação já fornecida, deixar de explicitar "não compartilhe o token" ou interpretar uma pergunta hipotética como oferta real. Avaliação com usuários e novos exemplos de linguagem natural ainda não foi feita.

## Observabilidade

O notebook registra tempo por pergunta nos testes. A interface distingue falhas HTTP, timeout e conexão do Ollama. O SQLite registra apenas origens de relatos classificados no nível 3; esses registros não são uma métrica de fraudes confirmadas.
