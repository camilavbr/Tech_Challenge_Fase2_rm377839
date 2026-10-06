# 💳 Modelo de Previsão de Risco de Crédito

Este repositório contém o desenvolvimento de um sistema preditivo para classificação de risco de crédito, focado em discriminar **bons** e **maus pagadores** para concessão de crédito. O projeto foi estruturado em **4 Notebooks interconectados**, simulando o fluxo de trabalho de um Cientista de Dados no mercado financeiro.


## 🎯 O Problema de Negócio e o Desafio Técnico

O objetivo comercial é **expandir a carteira de clientes de cartões de crédito** da instituição financeira, minimizando o prejuízo causado pela inadimplência. 

Ao analisar o histórico de comportamento financeiro, deparamo-nos com um cenário padrão de mercado: a base de dados é **altamente desbalanceada**, contendo apenas **~1,45% de Maus Pagadores (Classe 1)**. 

### A Armadilha da Acurácia
Em bases com esse nível de desbalanceamento, a métrica de Acurácia global torna-se inútil e enganosa. Um modelo ingênuo que aprovasse 100% dos clientes teria **98,3% de acurácia**, mas levaria a instituição à falência por não reter nenhum mau pagador. Por isso, a governança do projeto estabeleceu o **AUC-ROC** (poder de separação de classes) e o **Recall da Classe 1** (taxa de captura de maus pagadores) como as métricas norteadoras da solução.

---

## 🚀 Resumo da Jornada de Desenvolvimento

### 📜 01 - Análise Exploratória de Dados (EDA)
*   **Descoberta do Alvo:** A base original não possuía uma variável alvo pronta. Através do histórico de atrasos, aplicou-se a regra de negócio de mercado: clientes com atrasos iguais ou superiores a 60 dias foram mapeados como **Maus Pagadores (1)**, enquanto atrasos curtos e voláteis foram classificados como normais.
*   **O Enigma dos 1.000 Anos:** Identificou-se um outlier crítico na variável `DAYS_EMPLOYED` com o valor fixo de `365243` (equivalente a 1.000 anos). Investigou-se que se tratava de um "valor sentinela" do sistema de origem para marcar indivíduos **Desempregados**, permitindo o isolamento correto desse perfil de risco.
*   **Narrativa de Dados:** O cruzamento das faixas etárias e tempo de emprego revelou que jovens em início de carreira e desempregados disparam as taxas de inadimplência, enquanto a maturidade profissional estabiliza o risco.

### 📜 02 - Processamento e Engenharia de Recursos
*   **Saneamento de Dados:** Descartou-se a variável complexa de cargos (`OCCUPATION_TYPE`) em detrimento da coluna unificada de categorias de renda (`NAME_INCOME_TYPE`), otimizando a dimensionalidade do modelo.
*   **Feature Engineering:** Realizou-se a conversão de todas as strings em dados numéricos. Variáveis categóricas sem ordem natural passaram por **One-Hot Encoding** (geração de variáveis dummy), e os dados contínuos de Idade e Renda foram testados em duas abordagens: escalas contínuas puras versus faixas ordinais de comportamento econômico.
*   **Blindagem contra Data Leakage:** A decisão de escala (`StandardScaler`) foi declarada conceitualmente nesta fase, mas travada para execução apenas dentro de Pipelines no treino, impedindo o vazamento de estatísticas globais para o conjunto de testes.

### 📜 03 - Modelagem e Validação Cruzada
Realizou-se um experimento matricial comparando dois algoritmos (**Regressão Logística** e **Random Forest**) contra os dois cenários de dados, sob uma **Validação Cruzada Estratificada de 5 dobras**.

*   **Diagnóstico:** A Regressão Logística (modelo linear) falhou no cenário severamente desbalanceado, classificando toda a base como classe majoritária (Recall 0%). O **Random Forest na Base Original consagrou-se o campeão absoluto (AUC-ROC de 0.794)**, provando sua capacidade superior de encontrar cortes decimais exatos e capturar relações não-lineares nos dados.

### 📜 04 - Avaliação e Implicações de Negócio
*   **Métricas de Teste Inédito:** O modelo campeão manteve estabilidade ao ser submetido a dados que nunca tinha visto, validando o poder de generalização do algoritmo.
*   **Feature Importance:** O tempo de emprego (`ANOS_EMPREGADO`), a idade e a renda anual surgiram como as três variáveis mais decisivas. Isso faz total sentido no domínio financeiro, visto que a previsibilidade do fluxo de caixa e a maturidade são os pilares da solidez de crédito.
*   **O Impacto do Trade-off:** Na matriz de confusão, detalhou-se que o **Falso Negativo** (aprovar um mau pagador) é drasticamente mais caro para o banco do que o Falso Positivo (recusar um bom cliente), pois o primeiro gera perda patrimonial direta do cofre, enquanto o segundo é custo de oportunidade de receita.

---

## 📈 Conclusões Operacionais

A implementação deste modelo provê à instituição financeira uma ferramenta de **decisão automatizada ajustável**. Em cenários econômicos de recessão, a mesa de risco pode elevar a sensibilidade do modelo para blindar o patrimônio contra Falsos Negativos. Em cenários de expansão econômica, o filtro pode ser flexibilizado para maximizar a conversão comercial de novos cartões, operando como um motor de crescimento seguro para o negócio.

---
