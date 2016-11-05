---
title: 'Etapa 2: carregar dados em um experimento de Aprendizado de Máquina | Microsoft Docs'
description: 'Etapa 2 - desenvolver um passo a passo de solução de previsão: carregamento armazenado de dados públicos no Estúdio de Aprendizado de Máquina do Azure.'
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: garye

---
# Etapa 2 do passo a passo: carregar dados existentes no experimento de Aprendizado de Máquina do Azure
Esta é a segunda etapa do passo a passo, [Desenvolver uma solução de análise preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Criar um espaço de trabalho do Aprendizado de Máquina](machine-learning-walkthrough-1-create-ml-workspace.md)
2. **Carregar dados existentes**
3. [Criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Treinar e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implantar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Acessar o serviço Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
Para desenvolver um modelo preditivo para risco de crédito, precisamos de dados que podemos usar para treinar e testar o modelo. Para este passo a passo, usaremos o “Conjunto de Dados Statlog (Dados de Crédito Alemão) UCI” do repositório de Aprendizado de Máquina UCI. Você pode encontrá-lo aqui: <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos o arquivo chamado **german.data**. Baixe esse arquivo em sua unidade de disco rígido local.

Esse conjunto de dados contém linhas de 20 variáveis para 1000 candidatos antigos de crédito. Essas 20 variáveis representam o vetor de recurso do conjunto de dados que fornece características de identificação para cada candidato de crédito. Uma coluna adicional em cada linha representa o risco de crédito calculado do candidato, com 700 candidatos identificados como um risco de crédito baixo e 300 como um alto risco.

O website UCI fornece uma descrição dos atributos do vetor de recurso que inclui informações financeiras, histórico de crédito, status de emprego e informações pessoais. Para cada candidato, foi dada uma classificação binária indicando se são um risco baixo ou alto de crédito.

Usaremos estes dados para treinar um modelo analítico preditivo. Quando tivermos concluído, nosso modelo deverá ser capaz de aceitar informações para novas pessoas e prever se são um risco de crédito alto ou baixo.

Aqui está uma mudança interessante. A descrição do conjunto de dados explica que a classificação incorreta de uma pessoa como um risco de crédito baixo quando, na verdade, são um risco de crédito alto, é 5 vezes mais onerosa à instituição financeira do que a classificação incorreta de um risco de crédito baixo como alto. Uma forma simples de considerar isto em nosso experimento é duplicando (5 vezes) essas entradas que representam alguém com um risco de crédito alto. Então, se o modelo classificar incorretamente um risco de crédito alto como baixo, ele fará essa classificação incorreta 5 vezes, uma para cada duplicado. Isso aumentará o custo deste erro nos resultados de treinamento.

## Converter o formato do conjunto de dados
O conjunto de dados original usa um formato separado por espaço em branco. O Estúdio de Aprendizado de Máquina trabalha melhor com um arquivo CSV (de valores separados por vírgula). Então, converteremos o conjunto de dados substituindo espaços por vírgulas.

Há muitas maneiras de converter esses dados. Uma maneira é usar o seguinte comando do Windows PowerShell:

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Outra maneira é usar o comando Unix sed:

    sed 's/ /,/g' german.data > german.csv  

Em ambos os casos, criamos uma versão separada por vírgulas dos dados em um arquivo chamado **german.csv** que usaremos em nosso teste.

## Carregar o conjunto de dados para o Estúdio de Aprendizado de Máquina
Depois que os dados tiverem sido convertidos no formato CSV, precisaremos carregá-los no Estúdio de Aprendizado de Máquina. Para obter mais informações sobre os primeiros passos no Estúdio de Aprendizado de Máquina, consulte [Página Inicial do Estúdio de Aprendizado de Máquina do Microsoft Azure](https://studio.azureml.net/).

1. Abra o Estúdio de Aprendizado de Máquina: [https://studio.azureml.net](https://studio.azureml.net). Quando solicitado a entrar, use a conta que você especificou como proprietário do espaço de trabalho.
2. Clique na guia **Estúdio** na parte superior da janela.
3. Clique em **+NOVO** na parte inferior da janela.
4. Selecione **CONJUNTO DE DADOS**.
5. Selecione **DO ARQUIVO LOCAL**.
6. No diálogo **Fazer upload de um novo conjunto de dados**, clique em **Pesquisar** e localize o arquivo **german.csv** criado.
7. Insira um nome para o conjunto de dados. Para este passo a passo, vamos chamá-lo de "Dados do cartão de crédito alemão UCI".
8. Para tipo de dados, selecione **Arquivo CSV genérico sem cabeçalho (.nh.csv)**.
9. Inclua uma descrição se desejar.
10. Clique em **OK**.

![Carregar o conjunto de dados][1]

Isso carrega os dados em um módulo de conjunto de dados que podemos usar em um experimento.

> [!TIP]
> Para gerenciar conjuntos de dados que você carregou no Estúdio, clique na guia **CONJUNTOS DE DADOS** à esquerda da janela do Estúdio.
> 
> 

Para obter mais informações sobre como importar vários tipos de dados para um experimento, confira [Importar seus dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure](machine-learning-data-science-import-data.md).

**A seguir: [criar um novo teste](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!---HONumber=AcomDC_0921_2016-->