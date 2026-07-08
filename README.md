# Live Multi-Market Dashboard: Pipeline de Ingestão de Ativos em Tempo Real 📈🔄

Este repositório armazena o desenvolvimento de um **Dashboard Multi-Mercado Interativo**, estruturado como um pipeline de dados de alta frequência (Streaming) rodando em Python. O sistema realiza chamadas assíncronas consecutivas para extrair cotações ao vivo de diferentes classes de ativos (Ações Nacionais da B3, Fundos Imobiliários, Ações Americanas da NASDAQ/NYSE e Criptoativos), realiza a conversão cambial dinâmica para o Par USD/BRL, trata dados ausentes (*NaN*) causados pelo fechamento de mercados tradicionais e unifica a visualização do patrimônio em Reais (R$) sob o Horário Oficial de Brasília (BRT).

---

## 🎯 Abordagem de Negócio e Gestão de Portfólio Quantitativo

O gerenciamento de portfólios globais exige o monitoramento unificado e em tempo real de ativos que operam sob diferentes regras de liquidez, fusos horários e moedas. Enquanto as bolsas de valores tradicionais (B3, NYSE) possuem janelas de negociação rígidas e sofrem com o fator *lag* informacional de fechamento, o mercado de criptoativos opera em regime ininterrupto (24/7).

Este projeto resolve essa fricção ao atuar como uma **Esteira de Dados Resiliente**, garantindo que:
1. **Conversão Cambial Dinâmica:** Ativos cotados em Dólar (como `AAPL`, `BTC-USD` e `ETH-USD`) sejam convertidos instantaneamente para a moeda local através do ticker de câmbio em tempo real `BRL=X`.
2. **Continuidade de Negócio (Tratamento de *NaN*):** Caso o script seja executado fora do horário do pregão regular das ações, os mecanismos de preenchimento vetorial impedem a quebra do gráfico, utilizando fallbacks matemáticos e dados históricos imediatos.

---

## 🧠 Engenharia de Dados: Arquitetura e Fluxo do Código

O pipeline opera em um ciclo contínuo de loops assíncronos programados para rodar a cada 10 segundos, estruturado em quatro camadas fundamentais:

1. **Camada de Ingestão e Granularidade (ETL):** O motor consome a biblioteca `yfinance` buscando dados na menor granularidade pública disponível (`interval="1m"`), capturando as micro-oscilações *intraday* em tempo real.
2. **Tratamento de Matrizes e Vetores (Pandas Bias-Correction):** Utiliza as funções `.ffill()` (*forward fill*) e `.bfill()` (*backward fill*) sequencialmente no DataFrame para propagar a última cotação válida observada nas linhas passadas sobre as células vazias geradas por mercados fechados.
3. **Mecanismo de Segurança (*Safe Fallback*):** A função interna `obter_preco_safe()` valida individualmente se o ticker resultou em um valor do tipo *Not a Number* (`pd.isna`). Se verdadeiro, ela injeta um preço de balizamento padrão, impedindo falhas críticas de renderização matemática.
4. **Alinhamento de Fuso Horário Local:** Força a conversão das strings de tempo a partir de um deslocamento explícito de fuso horário de -3 horas (`timezone(timedelta(hours=-3))`), anulando o horário UTC nativo dos servidores em nuvem.

---

## 📉 Resultados e Comportamento Esperado do Sistema

Ao executar o script, o sistema limpa o terminal a cada ciclo utilizando o `clear_output(wait=True)` e atualiza o estado do patrimônio consolidado.

### 🚀 Link de Acesso à Versão Interativa
O arquivo HTML foi estruturado com scripts vetoriais adaptáveis. Pode abrir o painel em ecrã cheio, passar o cursor do rato sobre as fatias do gráfico para auditar os valores exatos em Reais (R$) e isolar ativos clicando na legenda lateral:

👉 **[ABRIR DASHBOARD INTERATIVO EM TELA CHEIA](https://henriquecrispim.github.io/live-finance-dashboard/dashboard_financas_reais(1).html)**

### 📊 Comportamento Operacional das Classes de Ativos:
* **Segmento de Criptoativos (`BTC` e `ETH`):** Apresentam volatilidade contínua e atualização real dos valores a cada pulso de 10 segundos, independentemente do dia ou horário da execução.
* **Segmento de Equities (`PETR4`, `VALE3`, `AAPL`):** Apresentam flutuações dinâmicas de preços estritamente durante o horário regular de funcionamento das respectivas bolsas. Se o script for rodado à noite ou fins de semana, o gráfico de rosca manterá a alocação de forma estável, preservando a última linha de fechamento válida.

---

## 🚀 Como Executar o Projeto

Para inicializar a esteira de dados de mercado, execute os passos abaixo:

1. **Abertura:** Abra o arquivo `.ipynb` deste repositório diretamente na sua conta do **Google Colab**.
2. **Execução:** Execute todas as células pressionando o atalho de teclado `Ctrl + F9`.
3. **Manipulação de Artefatos:** O pipeline gerará e atualizará continuamente na barra lateral de arquivos do ambiente o seguinte arquivo:
   * `dashboard_financas_reais.html`: O front-end gráfico da carteira, totalmente interativo e encapsulado em HTML5.

---

## 📚 Referências de Mercado e Manuais Técnicos

A engenharia estrutural de tratamento de dados e modelagem de ativos foi baseada na documentação de governança das entidades reguladoras de difusão de dados de mercado:

* **YAHOO FINANCE API CORE.** *Official Developer Documentation and Ticker Rules.* (Manual técnico de parametrização de intervalos de tempo *intraday*).
* **B3 (BOLSA, BALCÃO, BRASIL).** *Manual de Procedimentos Operacionais de Negociação da B3.* (Regras de horários de funcionamento do pregão regular de ações e fundos imobiliários no Brasil).
* **PLOTLY OPEN SOURCE GRAPHING LIBRARIES.** *Interactive Pie and Donut Charts in Python.* (Referência para customização de layouts vetoriais interativos e injeção de templates de texto em porcentagem).
