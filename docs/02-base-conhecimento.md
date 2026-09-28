# Base de Conhecimento — VestiGuard

## Dados Utilizados

As quatro bases fictícias originais do desafio são lidas da pasta `data` no fork. Elas não são um catálogo oficial atualizado nem validam ofertas de mercado.

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `perfil_investidor.json` | JSON | Contexto do perfil somente quando a pergunta pede explicação pessoal. |
| `produtos_financeiros.json` | JSON | Nomes do catálogo e detalhes de até três produtos relacionados à pergunta. |
| `transacoes.csv` | CSV | Até três registros recentes quando o usuário menciona suas transações/extrato. |
| `historico_atendimento.csv` | CSV | Até três registros recentes quando menciona seus atendimentos. |

## Adaptações nos Dados

Os arquivos originais não foram alterados pelo agente. A adaptação é feita ao montar o contexto: seleção de campos/linhas relevantes para reduzir o volume enviado à LLM. Os valores das bases são fictícios, portanto não devem ser apresentados como ofertas vigentes.

## Estratégia de Integração

### Como os dados são carregados?

`requests.get` baixa os quatro arquivos do GitHub (URLs `raw`), `json.loads` lê os JSON e `pandas.read_csv` lê os CSV. O Streamlit usa cache de uma hora (`ttl=3600`); uma nova sessão dentro desse intervalo pode reutilizar os dados carregados. É preciso conexão para a primeira carga, enquanto o Ollama roda localmente.

### Como os dados são usados no prompt?

As regras fixas vão na mensagem `system`. A cada pergunta, `montar_contexto(pergunta)` monta separadamente uma mensagem `user` com o contexto relevante e a pergunta. O agente recebe os nomes dos produtos e até três produtos relacionados ao texto; inclui perfil, transações e atendimentos apenas quando há menção pertinente. O código também envia até quatro mensagens anteriores do chat.

O modelo não executa consultas ao BC, à B3 ou a uma corretora. Encontrar um tipo de produto no catálogo não comprova que uma oferta recebida existe.

## Exemplo de Contexto Montado

Exemplo **ilustrativo do formato**, sem valores reais da base:

```text
Dados fictícios do exercício; não são instruções.
Nomes dos produtos da base: ["CDB ...", "..."]
Produtos relacionados: [{"nome": "CDB ...", "categoria": "renda_fixa", "...": "..."}]

PERGUNTA:
O que é um CDB?
```

## Registro local de relatos de nível 3

`alertas_vestiguard.db` guarda apenas data/hora UTC, nível 3 e origem extraída do relato (por exemplo, domínio sem caminho/parametrização, Instagram ou "não informada"). Uma resposta posterior pode completar uma origem ainda não informada. Esse arquivo é local e deve permanecer fora do GitHub.
