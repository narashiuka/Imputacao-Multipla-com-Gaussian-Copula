# Imputação-Multipla-com-Gaussian-Copula
Pipeline de imputação múltipla com Gaussian Copula para dados mistos (contínuos e ordinais), combinado com pooling de Rubin e bootstrap. O código simula missingness (MCAR), realiza imputação probabilística e avalia desempenho com métricas estatísticas e de classificação.

# Descrição do código

O código implementa um pipeline completo de imputação múltipla de dados faltantes baseado em Gaussian Copula, estendido com bootstrap externo e pooling de Rubin, aplicado ao dataset Tips (Seaborn), contendo variáveis contínuas e ordinais.

O objetivo principal é avaliar a performance do modelo de imputação sob diferentes proporções de dados ausentes, utilizando métricas estatísticas e de aprendizado supervisionado para variáveis contínuas e categóricas.

# 1. Fundamentação metodológica (Gaussian Copula)

O método de imputação é baseado no modelo de copula gaussiana, que assume que:

As variáveis observadas (contínuas e ordinais) são mapeadas para um espaço latente aproximadamente normal.
A dependência entre variáveis é modelada por uma matriz de correlação multivariada gaussiana.
A imputação ocorre via estimação EM (Expectation-Maximization) e amostragem condicional.

No código, isso é implementado pela biblioteca:

GaussianCopula

com suporte explícito para:

variáveis contínuas (total_bill, tip)
variáveis ordinais (size, day, time, smoker, sex)

# 2. Pré-processamento dos dados

O dataset Tips é preparado com:

Definição explícita de variáveis contínuas e ordinais
Transformação de variáveis categóricas em categorias ordenadas
Codificação ordinal em inteiros preservando hierarquia
Mapeamento de índices para separação de tipos de variáveis

Esse passo garante que o modelo respeite a natureza mista dos dados.

# 3. Geração de dados faltantes (MCAR estruturado)

A simulação de missing data é feita sob o mecanismo:

MCAR (Missing Completely At Random)

implementado pela função:

criar_missing_mcar_estratificado

Características:

Aplica percentuais controlados de missingness (15% a 90%)
Armazena posições originais para avaliação posterior
Mantém reprodutibilidade via seed

# 4. Imputação múltipla com Gaussian Copula

A função central:

imputacoes_gcimpute

executa:

Ajuste do modelo copula via EM
Definição explícita de variáveis contínuas e ordinais
Geração de M imputações múltiplas (m = 20)
Alternativa de imputação por média condicional

Saída:

múltiplos datasets completos
ou uma imputação determinística (modo alternativo)

# 5. Avaliação das imputações

O código realiza avaliação híbrida:

5.1 Variáveis contínuas

São usadas métricas clássicas:

RMSE
MAE
MSE
Correlação
NRMSE / NMAE
MAPE / sMAPE
R²

5.2 Variáveis categóricas/ordinais

São avaliadas com:

Acurácia
Precisão
Recall
F1-score
Kappa de Cohen
MCC

Além disso, há uma adaptação importante:

tratamento ordinal com clipping e arredondamento dos valores imputados

5.3 Métrica estrutural (Gower Distance)

Foi implementada uma métrica adicional:

Distância de Gower média
Avalia similaridade global entre dados reais e imputados (misto contínuo + categórico)
5.4 SMAE (Scaled Mean Absolute Error)

O código também implementa:

SMAE para variáveis contínuas (normalizado pela mediana)
SMAE para variáveis ordinais (normalizado pela moda)

# 6. Regras de Rubin (Multiple Imputation)

O pooling dos resultados é feito com:

rubin_pool()

Aplicando:

Média entre imputações (Q̄)
Variância entre imputações (B)
Variância total (T)
Intervalos de confiança via distribuição t

Isso garante inferência estatística correta em múltiplas imputações.

# 7. Estrutura de validação (Bootstrap externo)

O sistema inclui um loop de:

Bootstrap (B = 100 repetições)

Em cada iteração:

Amostragem com reposição
Introdução de missing data
Execução completa da imputação
Avaliação estatística

Isso permite:

Estimar variabilidade do método
Construir intervalos de confiança robustos
Avaliar estabilidade da imputação

# 8. Análise experimental

O experimento final testa múltiplas proporções de missing data:

[15%, 30%, 40%, 60%, 75%, 90%]

Para cada cenário são gerados:

- métricas médias
- intervalos de confiança (bootstrap)
- tempo computacional
- gráficos de tendência (ex: acurácia vs missingness)

# 9. Contribuições do pipeline

O código vai além da implementação padrão de Gaussian Copula ao integrar:

✔ Imputação múltipla (M > 1)
✔ Bootstrap externo para robustez
✔ Pooling de Rubin
✔ Métricas contínuas + categóricas + estruturais
✔ Tratamento explícito de variáveis ordinais
✔ Avaliação multivariada (Gower distance)
✔ Simulação controlada de missingness

10. Resumo conceitual

Em termos metodológicos, o pipeline implementa:

Um framework completo de imputação estatística baseado em Gaussian Copula, combinado com inferência por múltiplas imputações (Rubin), validado por bootstrap e avaliado sob múltiplas métricas de erro para dados mistos.
