<properties
	pageTitle="Estender seu experimento com R | Microsoft Azure"
	description="Como estender a funcionalidade do Estúdio de Aprendizado de Máquina do Azure por meio da linguagem R usando o módulo Executar o Script R."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="garye" />


#Estender seu experimento com R 

Você pode estender a funcionalidade do Estúdio AM por meio da linguagem R usando o módulo [Executar o Script R][execute-r-script].

Esse módulo aceita vários conjuntos de dados de entrada e produz um único conjunto de dados como saída. Você pode digitar um script R no parâmetro **Script R** do módulo [Executar Script R][execute-r-script].

Você pode acessar cada porta de entrada do módulo usando código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Listando todos os pacotes instalados no momento

A lista de pacotes instalados pode mudar. Para obter a lista completa, inclua as seguintes linhas no módulo [Executar Script R][execute-r-script] para enviar a lista para o conjunto de dados de saída:

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

Para exibir a lista de pacotes, conecte um módulo de conversão como [Converter para CSV][convert-to-csv] à saída do módulo [Executar Script R][execute-r-script], execute o experimento, clique na saída do módulo de conversão e selecione **Baixar**.

##Importando pacotes

Você também pode importar pacotes que ainda não estiverem instalados em um repositório do Estúdio AM preparado usando os seguintes comandos no módulo [Executar Script R][execute-r-script] e no arquivo de pacote compactado:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

em que `my_favorite_package.zip` contém o .zip do seu pacote.

##Lista de pacotes instalados

Para sua conveniência, a lista de pacotes incluídos na versão atual é fornecida na tabela a seguir.

Para obter a lista completa dos pacotes que estão disponíveis atualmente, consulte a seção intitulada **Listando todos os pacotes instalados atualmente** acima. A documentação atual sobre o R está disponível [aqui](http://cran.r-project.org/manuals.html).

- [Módulos R iniciados por A a E]
- [Módulos R iniciados por F a L]
- [Módulos R iniciados por M a R]
- [Módulos R iniciados por S a Z]

[Módulos R iniciados por A a E]: #r-modules-beginning-with-a-through-e
[Módulos R iniciados por F a L]: #r-modules-beginning-with-f-through-l
[Módulos R iniciados por M a R]: #r-modules-beginning-with-m-through-r
[Módulos R iniciados por S a Z]: #r-modules-beginning-with-s-through-z


###Módulos R iniciados por A a E

| Nome do pacote | Descrição do pacote |
| ------------ | ------------------- |
| abc | Ferramentas para ABC (Computação Bayesiana Aproximada) |
| abind | Combinar matrizes multidimensionais |
| actuar | Funções atuariais |
| ade4 | Análise de dados ecológicos: métodos exploratórios e Euclidianos nas ciências ambientais |
| AdMit | Mistura adaptável de distribuições do Student-t |
| aod | Análise de dados superdispersos |
| ape | Análises de filogenia e evolução |
| approximator | Previsão Bayesiana dos códigos de computador complexos |
| arm | Análise de dados usando modelos de regressão e hierárquicos/de vários níveis |
| arules | Conjuntos de itens frequentes e regras de associação de mineração |
| arulesViz | Conjuntos de itens frequentes e regras de associação de visualização |
| ASH | Rotinas de ASH de David Scott |
| assertthat | Pré- e pós-asserções fáceis |
| AtelieR | Uma GUI GTK para ensinar conceitos básicos de inferência estatística e fazer testes Bayesianos elementares |
| BaBooN | Correspondência de média preditiva de Bootstrap Bayesiano - Imputação múltipla e única para dados discretos |
| BACCO | Análise Bayesiana da Saída de Código de Computador (BACCO) |
| BaM | Funções e conjuntos de dados para livros de Jeff Gill |
| bark | Kernels de regressão aditiva Bayesiana |
| BAS | Média de modelo Bayesiano usando Amostragem Adaptável Bayesiana |
| base | O Pacote Base de R |
| BayesDA | Funções e conjuntos de dados para o livro Análise de Dados Bayesiana |
| bayesGARCH | Estimativa Bayesiana do modelo GARCH (1,1) com inovações de Student-t |
| bayesm | Inferência Bayesiana para marketing/microeconomia |
| bayesmix | Modelos de mistura Bayesiana com JAGS |
| bayesQR | Regressão de quantil Bayesiana |
| bayesSurv | Regressão de sobrevivência Bayesiana com distribuições flexíveis de erros e efeitos aleatórios |
| Bayesthresh | Modelos de efeito misto de limites Bayesianos para dados categóricos |
| BayesTree | Modelos Bayesianos para modelos baseados em árvore |
| BayesValidate | Pacote BayesValidate |
| BayesX | Utilitários R que acompanham o pacote de software BayesX |
| BayHaz | Funções R para estimativa da taxa de risco Bayesiana |
| bbemkr | Estimativa de largura de banda Bayesiana para regressão de kernel de múltiplas variáveis com erro Gaussiano |
| BCBCSF | Classificação Bayesiana com correção de tendência e com recursos selecionados |
| BCE | Avaliador de composição Bayesiana: estimativa de composição de amostra (taxonômica) por meio de dados de marcadores biológicos |
| bclust | Cluster Bayesiano usando o modelo hierárquico de pico e sessão, adequado para a criação de clusters de dados altamente dimensionáveis |
| bcp | Um pacote para executar uma análise Bayesiana dos problemas de ponto de alteração |
| BenfordTests | Testes estatísticos para avaliar a conformidade com a Lei de Benford |
| bfp | Polinômios fracionários Bayesianos |
| BH | Arquivos de cabeçalho Boost C++ |
| bisoreg | Regressão isotônica Bayesiana com polinômios de Bernstein |
| bit | Uma classe de vetores de booleanos de 1 bit |
| bitops | Operadores bit a bit |
| BLR | Regressão Linear Bayesiana |
| BMA | Média de modelo Bayesiano |
| Bmix | Amostragem Bayesiana para misturas do tipo “stick-breaking” |
| BMS | Biblioteca de médias de modelo Bayesiano |
| bnlearn | Aprendizado de estrutura, aprendizado de parâmetros e inferência de rede Bayesiana |
| boa | Programa de análise de saída Bayesiana (BOA) para MCMC |
| Bolstad | Funções de Bolstad |
| boot | Funções de Bootstrap (originalmente por Angelo Canty para S) |
| bootstrap | Funções do livro An Introduction to the Bootstrap |
| bqtl | Kit de ferramentas de mapeamento QTL Bayesiano |
| BradleyTerry2 | Modelos de Bradley-Terry |
| brew | Estrutura de modelagem para geração de relatórios |
| brglm | Redução de tendência em modelos lineares generalizados de resposta binomial |
| bspec | Inferência de espectro Bayesiana |
| bspmma | bspmma: modelos semiparamétricos Bayesianos para meta-análise |
| BVS | Seleção de variante Bayesiana: técnicas de incerteza do modelo Bayesiano para estudos de associação genética |
| cairoDevice | Driver de dispositivo de gráficos sem alias, de plataforma cruzada, baseado em Cairo |
| calibrator | Calibração Bayesiana dos códigos de computador complexos |
| car | Acompanha a regressão aplicada |
| caret | Treinamento de regressão e classificação |
| catnet | Inferência de rede Bayesiana categórica |
| caTools | Ferramentas: estatísticas de janela em movimento, GIF, Base64, ROC AUC, etc. |
| chron | Objetos cronológicos que podem lidar com datas e horas |
| class | Funções de classificação |
| cluster | Análise de cluster estendida Rousseeuw et al. |
| clusterSim | Busca pelo procedimento ideal de cluster para um conjunto de dados |
| coda | Análise e diagnóstico de saída para MCMC |
| codetools | Ferramentas de análise de código para R |
| coin | Procedimentos de inferência condicional em uma estrutura de teste de permuta |
| colorspace | Manipulação de espaço de cor |
| combinat | utilitários de matemática combinatória |
| compiler | O pacote Compilador de R |
| corpcor | Estimativa eficiente da covariância e correlação (parcial) |
| cslogistic | Regressão logística especificada condicionalmente |
| ctv | Modos de exibição de tarefas CRAN |
| cubature | Integração de variação múltipla adaptável em hipercubos |
| data.table | Extensão de data.frame |
| datasets | O pacote de conjuntos de dados de R |
| data | Funções para manipular datas |
| dclone | Clonagem de dados e ferramentas de MCMC para métodos de probabilidade máxima |
| deal | Aprendendo redes Bayesianas com variáveis mistas |
| Deducer | Deducer: um GUI de análise de dados para R |
| DeducerExtras | Caixas de diálogo e funções adicionais para Deducer |
| deldir | Triangulação de Delaunay e Mosaico de Dirichlet (Voronoi). |
| DEoptimR | Otimização de evolução diferencial em R puro |
| deSolve | Solvers gerais para problemas de valor inicial de equações diferenciais comuns (ODE), equações diferenciais parciais (PDE), equações algébricas diferenciais (DAE) e equações diferenciais de atraso (DDE) |
| devtools | Ferramentas para facilitar o desenvolvimento de código em R |
| dichromat | Esquemas de cores para dicromatas |
| digest | Crie resumos de hash criptográfico de objetos em R |
| distrom | Regressão multinomial distribuída |
| dlm | Análise de probabilidade e Bayesiana de modelos lineares dinâmicos |
| doSNOW | Adaptador paralelo de foreach para o pacote snow |
| dplyr | dplyr: uma gramática de manipulação de dados |
| DPpackage | Modelagem não paramétrica Bayesiana em R |
| dse | Estimativa de sistemas dinâmicos (pacote de série de tempo) |
| e1071 | Funções diversas do Departamento de Estatística (e1071), TU Wien |
| EbayesThresh | Estabelecimento de limites Bayesiano empírico e métodos relacionados |
| ebdbNet | Estimativa Bayesiana empírica de redes Bayesianas dinâmicas |
| effects | Exibições de efeito para modelos lineares, lineares generalizados, logit multinomiais, logit de chance proporcional e modelos de efeitos misto |
| emulator | Emulação Bayesiana de programas de computador |
| ensembleBMA | Previsão probabilística usando ensembles e média de modelo Bayesiano |
| entropy | Estimativa de entropia, informações mútuas e quantidades relacionadas |
| EvalEst | Estimativa de sistemas dinâmica - extensões |
| evaluate | Ferramentas de análise e avaliação que fornecem mais detalhes do que o padrão |
| evdbayes | Análise Bayesiana da Teoria do Valor Extremo |
| evora | Exceções de variáveis epigenéticas para análise de previsão de risco |
| exactLoglinTest | Testes de Exatidão de Monte Carlo para modelos lineares de log |
| expm | Matriz exponencial |
| extremevalues | Detecção de exceções monovariáveis |


###Módulos R iniciados por F a L

| Nome do pacote | Descrição do pacote |
| ------------ | ------------------- |
| factorQR | Modelos de fator de regressão de quantil Bayesiana |
| faoutlier | Métodos de detecção de casos influentes para análise de fatores e SEM |
| fitdistrplus | Ajudar para ajustar uma distribuição paramétrica para dados censurados ou não censurados |
| FME | Um ambiente de modelagem flexível para modelagem inversa, sensibilidade, identificabilidade e Análise de Monte Carlo |
| foreach | Construção de loop foreach para R |
| forecast | Funções de previsão de série temporal e modelos lineares |
| foreign | Ler dados armazenados por Minitab, S, SAS, SPSS, Stata, Systat, Weka, dBase... |
| formatR | Formatar automaticamente código em R |
| Formula | Fórmulas de modelo estendido |
| fracdiff | Modelos ARIMA, também conhecidos como ARFIMA (p,d,q), diferenciados de modo fracionado |
| gam | Modelos aditivos generalizados |
| gamlr | Regressão Gamma Lasso |
| gbm | Modelos de regressão aprimorados generalizados |
| gclus | Gráficos de cluster |
| gdata | Várias ferramentas de programação de R para manipulação de dados |
| gee | Solucionador de equações de estimativa generalizada |
| genetics | Genética populacional |
| geoR | Análise de dados geoestatísticos |
| geoRglm | geoRglm - um pacote para modelos espaciais lineares generalizados |
| geosphere | Trigonométrica esférica |
| ggmcmc | Ferramentas gráficas para analisar simulações de Cadeia de Markov Monte Carlo por meio da inferência Bayesiana |
| ggplot2 | Uma implementação da Gramática de Gráficos |
| glmmBUGS | Modelos mistos lineares generalizados e modelos espaciais com WinBUGS, BRugs ou OpenBUGS |
| glmnet | Modelos lineares generalizados regularizados de rede elástica e Lasso |
| gmodels | Várias ferramentas de programação de R para ajuste de modelo |
| gmp | Aritmética de precisão múltipla |
| gnm | Modelos não lineares generalizados |
| googlePublicData | Uma biblioteca de R para compilar arquivos de metadados de DSPL do Public Data Explorer do Google |
| googleVis | Interface entre R e Google Charts |
| GPArotation | Rotação de fator de GPA |
| gplots | Várias ferramentas de programação de R para plotagem de dados |
| graphics | O pacote de gráficos de R |
| grDevices | Os dispositivos para gráficos em R e suporte para fontes e cores |
| gregmisc | Diversas funções de Greg |
| grid | O pacote de gráficos de grade de R |
| gridExtra | Funções em gráficos de grade |
| growcurves | Modelos de curva de crescimento não paramétrico e semiparamétrico que incluem também vários efeitos aleatórios de associação |
| grpreg | Caminhos de regularização para modelos de regressão com covariáveis agrupados |
| gsubfn | Utilitários para argumentos de cadeias de caracteres e função |
| gtable | Organizar grobs em tabelas |
| gtools | Várias ferramentas de programação em R |
| gWidgets | API gWidgets para a criação de GUIs interativas, independentes de kits de ferramentas |
| gWidgetsRGtk2 | Implementação do kit de ferramentas de gWidgets para RGtk2 |
| haplo.Stats | Análise estatística de haplótipos com traços e covariáveis quando a fase de vinculação é ambígua |
| hbsae | Estimativa Bayesiana hierárquica de área pequena |
| hdrcde | Estimativa de densidade condicional e regiões de maior densidade |
| heavy | Pacote para acomodação de exceções usando distribuições de cauda longa |
| hflights | Voos que partiram de Houston em 2011 |
| HH | Exibição de dados e análise estatística: Heiberger e Holland |
| HI | Simulação de distribuições com suporte por hiperplanos aninhados |
| highr | Realce de sintaxe para R |
| Hmisc | Recursos diversos de Harrell |
| htmltools | Ferramentas para HTML |
| httpuv | Biblioteca de servidores HTTP e WebSocket |
| httr | Ferramentas para trabalhar com URLs e HTTP |
| IBrokers | API de R para Interactive Brokers Trader Workstation |
| igraph | Análise e visualização de rede |
| intervals | Ferramentas para trabalhar com pontos e intervalos |
| iplots | iPlots - gráficos interativos para R |
| ipred | Previsão aprimorada |
| irr | Vários coeficientes de confiabilidade e concordância entre pontuadores |
| iterators | Construção de iterador para R |
| JavaGD | Dispositivo gráfico de Java |
| JGR | JGR - GUI Java para R |
| kernlab | Laboratório de aprendizado de máquina baseado em kernel |
| KernSmooth | Funções para suavização de kernel de Wand e Jones (1995) |
| KFKSDS | Filtro, suavizador e suavizador de perturbação de Kalman |
| kinship2 | Funções de pedigree |
| kknn | Vizinhos ponderados mais próximos de k |
| klaR | Classificação e visualização |
| knitr | Um pacote de finalidade geral para geração de relatórios dinâmicos em R |
| ks | Suavização de kernel |
| labeling | Rotulação de eixo |
| Lahman | Banco de dados de beisebol de Sean Lahman |
| lars | Ângulo mínimo de regressão, Lasso e avanço em etapas |
| lattice | Gráficos de Lattice |
| latticeExtra | Utilitários gráficos extras com base em Lattice |
| lava | Modelos de variáveis latentes lineares |
| lavaan | Análise de variável latente |
| leaps | Seleção de subconjunto de regressão |
| LearnBayes | Funções de aprendizado de inferência Bayesiana |
| limSolve | Solução de modelos lineares inversos |
| lme4 | Modelos lineares de efeito misto usando Eigen e S4 |
| lmm | Modelos mistos lineares |
| lmPerm | Testes de permuta para modelos lineares |
| lmtest | Teste de modelos de regressão linear |
| locfit | Estimativa de densidade, regressão local e probabilidade |
| lpSolve | Interface pata Lp_solve v. 5.5 para resolver programas lineares/inteiros |


###Módulos R iniciados por M a R

| Nome do pacote | Descrição do pacote |
| ------------ | ------------------- |
| magic | criar e investigar quadrados mágicos |
| magrittr | magrittr - um operador de pipe de avanço para R |
| mapdata | Bancos de dados de mapa extra |
| mapproj | Projeções de mapa |
| maps | Desenhar mapas geográficos |
| maptools | Ferramentas para ler e manipular objetos espaciais |
| maptree | Mapeamento, remoção e criação de gráficos de modelos de árvore |
| markdown | Renderização de redução para R |
| MASS | Suporte a funções e conjuntos de dados para o MASS de Venables e Ripley |
| MasterBayes | Métodos ML e MCMC para análise e reconstrução de pedigree |
| Matrix | Métodos e classes de matriz densa e esparsa |
| matrixcalc | Coleção de funções para cálculos de matriz |
| MatrixModels | Modelagem com matrizes esparsas e densas |
| maxent | Regressão logística multinomial de pouca memória com suporte para classificação de texto |
| maxLik | Estimativa de probabilidade máxima |
| mcmc | Cadeia de Markov Monte Carlo |
| MCMCglmm | Modelos mistos lineares generalizados de MCMC |
| MCMCpack | Pacote de Cadeia de Markov Monte Carlo (MCMC) |
| memoise | Funções de Memoise |
| methods | Classes e métodos formais |
| mgcv | Veículo de computação GAM mista com previsão de suavização de AIC/GCV/REML |
| mice | Imputação de variação múltipla de equações em cadeia |
| microbenchmark | Funções de temporização com precisão de microssegundos |
| MIME | Mapear nomes de arquivo para tipos MIME |
| minpack.LM | Interface R para o algoritmo de quadrados mínimos não linear de Levenberg-Marquardt encontrado no MINPACK, além do suporte para limites |
| minqa | Algoritmos de otimização livres de derivativo por aproximação quadrática |
| misc3d | Gráficos de plotagem 3D variados |
| miscF | Funções variadas |
| miscTools | Ferramentas e utilitários diversas |
| mixtools | Ferramentas para análise de modelos de mistura finitos |
| mlbench | Problemas de benchmarking do Aprendizado de Máquina |
| mlogitBMA | Média do modelo Bayesiano para modelos de logit Multinomiais |
| mnormt | Distribuições de t e normais com variações múltiplas |
| MNP | Pacote R para ajuste do modelo Probit multinomial |
| modeltools | Ferramentas e classes para modelos estatísticos |
| mombf | Fatores Bayesianos de momento e momento inverso |
| monomvn | Estimativa para dados normais com variáveis múltiplas e de Student-t com ausência monótona |
| monreg | Regressão monótona não paramétrica |
| mosaic | Utilitários para ensino de estatística e matemática do projeto MOSAIC (mosaic-web.org) |
| MSBVAR | Modelos de regressão automática de vetor, Bayesiano, de troca de Markov |
| MSM | Modelos Markov ocultos e de vários estados no tempo contínuo |
| multcomp | Inferência simultânea em modelos paramétricos gerais |
| multicool | Permutações de conjuntos múltiplos na ordem cool-lex. |
| munsell | Sistema de cores Munsell |
| mvoutlier | Detecção de exceções com variações múltiplas com base em métodos robustos |
| mvtnorm | Distribuições de t e normal com variações múltiplas |
| ncvreg | Caminhos de regularização para modelos de regressão com penalização SCAD e MCP |
| nlme | Modelos lineares e não lineares de efeitos mistos |
| NLP | Infraestrutura de processamento de linguagem natural |
| nnet | Modelos de log linear multinomial e redes neurais de realimentação |
| numbers | Funções teóricas de números |
| numDeriv | Derivados numéricos precisos |
| openNLP | Interface de ferramentas de OpenNLP do Apache |
| openNLPdata | Modelos de língua inglesa básicos e Jars OpenNLP Apache |
| OutlierDC | Detecção de exceções usando regressão de quantil para dados censurados |
| OutlierDM | Detecção de exceções para dados replicados de alta taxa de transferência |
| outliers | Testes de exceções |
| pacbpred | Estimativa e previsão PAC-Bayesiana e em modelos aditivos esparsos |
| parallel | Suporte para computação paralela em R |
| partitions | Partições de adição de inteiros |
| party | Um laboratório para particionamento recursivo |
| PAWL | Implementação do algoritmo PAWL |
| pbivnorm | CDF normal bivariado vetorizado |
| pcaPP | PCA robusto de Projection Pursuit |
| permute | Funções para gerar permutações restritas de dados |
| pls | Quadrantes mínimos parciais e o regressão de componente principal |
| plyr | Ferramentas de divisão, aplicação e combinação de dados |
| png | Ler e gravar imagens PNG |
| polynom | Uma coleção de funções para implementar uma classe de manipulações polinomiais monovariáveis |
| PottsUtils | Funções de utilitário dos modelos de Potts |
| predmixcor | Regra de classificação com base em modelos de mistura Bayesianos com tendência de seleção de recurso corrigida |
| PresenceAbsence | Avaliação do modelo de presença-ausência |
| prodlim | Estimativa de limite de produto. Método Kaplan-Meier e Aalen-Johansson para análise de evento histórico censurado (sobrevivência) |
| profdpm | Misturas de processo de perfil de Dirichlet |
| profileModel | Ferramentas para criação de perfil de funções de inferência para várias classes de modelo |
| proto | Programação baseada em objeto de protótipo |
| pscl | Laboratório Computacional de Ciência Política, da Universidade de Stanford |
| psych | Procedimentos para pesquisa psicológica, psicometria e de personalidade |
| quadprog | Funções para solucionar problemas de programação quadrática |
| quantreg | Regressão de quantil |
| qvcalc | Variações aparentes para efeitos de fator em modelos estatísticos |
| R.matlab | Leitura e gravação de arquivos MAT, com conectividade de R para MATLAB |
| R.methodsS3 | Função de utilitário para definir métodos S3 |
| R.OO | Programação orientada a objeto R com ou sem referências |
| R.utils | Vários utilitários de programação |
| R2HTML | Exportação HTML para objetos R |
| R2jags | Um pacote para executar jags no R |
| R2OpenBUGS | Executar OpenBUGS no R |
| R2WinBUGS | Executar WinBUGS e OpenBUGS no R / S-PLUS |
| ramps | Modelagem geoestatística Bayesiana com RAMPS |
| RandomFields | Simulação e análise de campos aleatórios |
| randomForest | Florestas aleatórias de Breiman e Cutler para classificação e regressão |
| RArcInfo | Funções para importar dados de coberturas binárias Arc/Info V7.x |
| raster | raster: análise e modelagem de dados geográficos |
| rbugs | Fusão de R e OpenBugs, entre outros |
| RColorBrewer | Paletas do ColorBrewer |
| Rcpp | Integração perfeita entre R e C++ |
| RcppArmadillo | Integração de Rcpp para a biblioteca de álgebra de modelos lineares do Armadillo |
| rcppbugs | Associação de R para cppbugs |
| RcppEigen | Integração de Rcpp para a biblioteca de álgebra de modelos lineares do Eigen |
| RcppExamples | Exemplos de uso de Rcpp para interface de R e C++ |
| RCurl | Interface de cliente de rede geral (HTTP/FTP /...) para R |
| relimp | Contribuição relativa dos efeitos em um modelo de regressão |
| reshape | Remodelagem flexível de dados |
| reshape2 | Remodelagem flexível de dados: uma reinicialização do pacote de remodelagem |
| rgdal | Associações para a Biblioteca de abstração de dados geoespaciais |
| rgeos | Interface para o mecanismo de geometria - software livre (GEOS) |
| rgl | Sistema de dispositivo de visualização 3D (OpenGL) |
| RGraphics | Dados e funções do livro R Graphics, segunda edição |
| RGtk2 | Vinculações de R para Gtk 2.8.0 e acima |
| RJaCGH | MCMC com saltos reversíveis para análise de matrizes CGH |
| rjags | Modelos de gráficos Bayesianos usando MCMC |
| rJava | R de nível mais baixo para interface Java |
| RJSONIO | Serializar objetos R para JSON, JavaScript Object Notation |
| robCompositions | Estimativa robusta para dados composicionais |
| robustbase | Estatísticas robustas básicas |
| RODBC | Acesso a bancos de dados ODBC |
| rootSolve | Análise de estado estacionário, equilíbrio e localização de raiz não linear de equações diferenciais comuns |
| roxygen | Programação literária em R |
| roxygen2 | Documentação de origem para R |
| rpart | Particionamento recursivo e árvores de regressão |
| rrcov | Estimadores robustos escalonáveis com alto ponto de interrupção |
| rscproxy | statconn: fornece a interface C-style portátil para R (StatConnector) |
| RSGHB | Funções para estimativa Bayesiana hierárquica: uma abordagem flexível |
| RSNNS | Redes neurais no R usando o Simulador de Rede Neural de Stuttgart (SNNS) |
| RTextTools | Classificação automática de texto por meio do aprendizado supervisionado |
| RUnit | Estrutura de teste R Unit |
| runjags | Utilitários de interface, métodos de computação paralela e distribuições adicionais para modelos MCMC em JAGS |
| Runuran | Interface R para geradores de variação aleatória UNU.RAN |
| rworldmap | Mapeamento de varredura, vetor e dados globais |
| rworldxtra | Fronteiras nacionais em alta resolução |


###Módulos R iniciados por S a Z

| Nome do pacote | Descrição do pacote |
| ------------ | ------------------- |
| SampleSizeMeans | Cálculos de tamanho de amostra para médias normais |
| SampleSizeProportions | Cálculo dos requisitos de tamanho de amostra ao estimar a diferença entre duas proporções binomiais |
| sandwich | Avaliadores de matriz de covariância robusta |
| sbgcop | Imputação e estimativa de cópula Gaussiana Bayesiana semiparamétrica |
| scales | Funções de escala para gráficos |
| scapeMCMC | Plotagens de diagnóstico de MCMC |
| scatterplot3d | Plotagem de dispersão em 3D |
| sciplot | Funções gráficas científicas para design fatorial |
| segmented | Relações segmentadas em modelos de regressão com pontos de interrupção/alteração |
| sem | Modelos de equação estrutural |
| seriation | Infra-estrutura para serialização |
| setRNG | Semente e gerador de número aleatório (normal) |
| sgeostat | Uma estrutura voltada a objetos para modelagem geoestatística em S+ |
| shapefiles | Leitura e gravação de arquivos de forma ESRI |
| shiny | A estrutura de aplicativo Web ASP.NET. |
| SimpleTable | Análise de sensibilidade e inferência Bayesiana para efeitos causais de tabelas 2 x 2 e 2 x 2 x K na presença de confusão sem medida |
| slam | Matrizes e leves esparsas |
| smoothSurv | Regressão de sobrevivência com distribuição de erro suave |
| sna | Ferramentas para análise de rede social |
| snow | Rede simples de estações de trabalho |
| SnowballC | Redutores do tipo snowball com ase na biblioteca C libstemmer UTF-8 |
| snowFT | Rede de simples de estações de trabalho com tolerância a erros |
| sp | Classes e métodos para dados espaciais |
| spacetime | Classes e métodos para dados espaço-temporais |
| SparseM | Álgebra linear dispersa |
| spatial | Funções de Kriging e análise de padrão de ponto |
| spBayes | Modelagem espaço-temporal monovariável e multivariável |
| spdep | Dependência espacial: avaliando esquemas, estatísticas e modelos |
| spikeslab | Seleção de previsão e variável usando regressão de pico e sessão |
| splancs | Análise de padrão de ponto de espaço-tempo e espacial |
| splines | Classes e funções de spline de regressão |
| spTimer | Modelagem Bayesiana espaço-temporal usando R |
| stats | O pacote de estatísticas de R |
| stats4 | Funções estatísticas usando Classes S4 |
| stochvol | Inferência Bayesiana eficiente para modelos de volatilidade estocástica (SV) |
| stringr | Facilita trabalhar com cadeias de caracteres |
| strucchange | Teste, monitoramento e atribuição de datas a alterações estruturais |
| stsm | Modelos de série temporal estrutural |
| stsm.class | Classe e métodos para modelos de série temporal estrutural |
| SuppDists | Distribuições suplementares |
| survival | Análise de sobrevivência |
| svmpath | svmpath: o algoritmo de caminho de SVM |
| tau | Utilitários de análise de texto |
| tcltk | Interface Tcl/Tk. |
| tcltk2 | Adições de Tcl/Tk. |
| TeachingDemos | Demonstrações para ensino e aprendizado |
| tensorA | Aritmética tensorial avançada com índices nomeados |
| testthat | Código Testthat. Ferramentas para tornar os testes divertidos |
| textcat | Categorização de texto baseada em N-Gram |
| textir | Regressão inversa para análise de texto |
| tfplot | Utilitários de usuário de intervalo de tempo |
| tframe | Kernel de codificação de intervalo de tempo |
| tgp | Modelos de processo Gaussiano de árvore Bayesiana |
| TH.data | Arquivo de dados de TH |
| timeDate | Rmetrics - objetos de calendário e cronológicos |
| tm | Pacote de mineração de texto |
| ferramentas | Ferramentas para desenvolvimento de pacotes |
| traduções | Pacote de traduções de R |
| tree | Árvores de regressão e classificação |
| tseries | Análise de série temporal e finanças computacionais |
| tsfa | Análise de fator de série temporal |
| tsoutliers | Detecção automática de exceções de série temporal |
| TSP | Problema do caixeiro-viajante (TSP) |
| UsingR | Conjuntos de dados para o texto Usando R para Introdução à Estatística |
| utils | O pacote de utilitários R |
| varSelectIP | Seleção de modelos Bayesiana objetiva |
| vcd | Visualizando dados categóricos |
| vegan | Pacote ecológico para a comunidade |
| VGAM | Modelos aditivos e lineares generalizados de vetores |
| VIF | Regressão VIF: Um algoritmo de regressão rápida para dados grandes |
| whisker | {{mustache}} para R, modelagem sem lógica |
| wordcloud | Nuvens de palavras |
| XLConnect | Conector Excel para R |
| XML | Ferramentas de análise e geração de XML em R e S-Plus |
| xtable | Exportar tabelas para LaTeX ou HTML |
| XTS | Série de tempo extensível |
| yaml | Métodos para converter dados R para YAML e vice-versa |
| zic | Inferência Bayesiana para modelos de contagem do tipo zero inflado |
| zipfR | Modelos estatísticos para distribuições de frequência de palavras |
| zoo | Infra-estrutura S3 para séries de tempo regular e irregular (observações ordenadas de Z) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
 

<!---HONumber=July15_HO2-->