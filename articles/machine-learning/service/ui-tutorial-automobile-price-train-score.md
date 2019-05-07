---
title: 'Tutorial: Prever o preço de automóveis com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como treinar, pontuar e implantar um modelo de machine learning usando uma interface visual do tipo "arrastar e soltar". Este tutorial é a primeira parte de uma série de duas partes sobre a previsão de preços de automóveis usando a regressão linear.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 1dc905b2ac89667dcb5800b0f539cb3e9f3acdcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027582"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prever o preço de automóveis com a interface visual

Neste tutorial, você analisará de maneira aprofundada o desenvolvimento de uma solução preditiva na interface visual do Serviço do Azure Machine Learning. Ao final deste tutorial, você terá uma solução que pode prever o preço de qualquer carro com base nas especificações técnicas enviadas a ela.

Este tutorial [é uma continuação do Início Rápido](ui-quickstart-run-experiment.md) e é a **primeira parte de uma série de tutoriais de duas partes**. No entanto, você não precisa concluir o Início Rápido antes de começar.

Na primeira parte da série de tutoriais, você aprenderá a:

> [!div class="checklist"]
> * Importar e limpar os dados (as mesmas etapas do Início Rápido)
> * Treinar um modelo de machine learning
> * Pontuar e avaliar um modelo

Na [segunda parte](ui-tutorial-automobile-price-deploy.md) da série de tutoriais, você aprenderá a implantar seu modelo preditivo como um serviço Web do Azure.

> [!NOTE]
> Uma versão concluída deste tutorial está disponível como um teste de exemplo.
> Na página Testes, acesse **Adicionar Novo** > **Amostra 1 – Regressão: Previsão de Preços de Automóveis (Básica)**

> [!VIDEO https://www.youtube.com/embed/VDKpDNX96aA]


## <a name="create-a-workspace"></a>Criar um workspace

Se você tiver um workspace do Serviço do Azure Machine Learning, passe para a [próxima seção](#open-the-visual-interface-webpage). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Abrir a página da Web da interface visual

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  

1. No workspace, selecione **Interface visual**.  Em seguida, selecione **Iniciar interface visual**.  

    ![Captura de tela do portal do Azure mostrando como acessar a Interface visual em um workspace do serviço do Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    A página da Web da interface será aberta em uma nova página do navegador.  

## <a name="import-and-clean-your-data"></a>Importar e limpar os dados

A primeira coisa que você precisa fazer é limpar os dados. Se você concluiu o Início Rápido, reutilize seu teste de preparação de dados aqui. Se você não concluiu o Início Rápido, vá para a próxima seção e [comece com um novo teste](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Reutilizar o teste do Início Rápido

1. Abra o teste do Início Rápido.

1. Selecione **Salvar como** na parte inferior da janela.

1. Forneça a ele um novo nome na caixa de diálogo pop-up exibida.

    ![Captura de tela mostrando como renomear um teste para "Tutorial – Prever o Preço de Automóveis"](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. O teste deve se parecer como o seguinte:

    ![Captura de tela mostrando o estado esperado do teste. O conjunto de dados de automóveis se conecta ao módulo Selecionar Colunas, que, por sua vez, se conecta ao módulo Limpar Dados Ausentes](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Se você reutilizou com êxito seu teste do Início Rápido, vá para a próxima seção para começar a [treinar seu modelo](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Começar com um novo teste

Se você não concluiu o Início Rápido, siga estas etapas para criar rapidamente um teste que importa e limpa o conjunto de dados de automóveis.

1. Crie um teste selecionando **+NOVO** na parte inferior da janela da interface visual.

1. Selecione **EXPERIMENTO** >  **Experimento em Branco**.

1. Selecione o nome do teste padrão **"Teste Criado em...**" na parte superior da tela e renomeie-o para algo significativo. Por exemplo, **Previsão de preços de automóveis**. O nome não precisa ser exclusivo.

1. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Para encontrar os módulos, use a caixa de pesquisa na parte superior da paleta de módulos. Digite **automóvel** na caixa de pesquisa para encontrar o conjunto de dados rotulado **Dados de preços de automóveis (Brutos)**. Arraste este conjunto de dados até a tela do experimento.

    ![Captura de tela de como encontrar o conjunto de dados de preços de automóveis](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Agora que você tem seus dados, adicione um módulo que remove por completo a coluna **normalized-losses**. Em seguida, adicione outro módulo que remova qualquer linha que tenha dados ausentes.

1. Digite **selecionar colunas** na caixa de pesquisa para encontrar o módulo **Selecionar Colunas no Conjunto de Dados**. Em seguida, arraste-o para a tela do experimento. Esse módulo permite selecionar quais colunas de dados você deseja incluir ou excluir no modelo.

1. Conecte a porta de saída do conjunto de dados **Dados de preço de automóveis (Brutos)** à porta de entrada de Selecionar Colunas no Conjunto de Dados.

    ![GIF animado mostrando como conectar o módulo Dados de Preços de Automóveis ao módulo Selecionar Colunas](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecione o módulo Selecionar Colunas no Conjunto de Dados e selecione **Iniciar seletor de colunas** no painel **Propriedades**.

   1. À esquerda, selecione **Com regras**

   1. Ao lado de **Começar com**, selecione **Todas as colunas**. Essas regras instruem o módulo **Selecionar Colunas no Conjunto de Dados** a passar por todas as colunas (exceto aquelas que estamos prestes a excluir).

   1. Nos menus suspensos, selecione **Excluir** e **nomes de coluna** e, em seguida, digite **normalized-losses** na caixa de texto.

   1. Selecione o botão OK para fechar o seletor de colunas (no canto inferior direito).

     Agora, o painel de propriedades de **Selecionar Colunas no Conjunto de Dados** indica que ele passará por todas as colunas do conjunto de dados exceto por **normalized-losses**.

1. Adicione um comentário ao módulo **Selecionar Colunas no Conjunto de Dados** clicando duas vezes no módulo e inserindo "Excluir perdas normalizadas". Isso pode ajudar você a ver rapidamente o que o módulo está fazendo no teste.

    ![Captura de tela mostrando a configuração correta do módulo Selecionar Colunas](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Digite **Limpar** na caixa de pesquisa para encontrar o módulo **Limpar Dados Ausentes**. Arraste o módulo **Limpar Dados Ausentes** para a tela do teste e conecte-o ao módulo **Selecionar Colunas no Conjunto de Dados**.

1. No painel **Propriedades** selecione **Remover linha inteira** em **Modo de limpeza**. Essas opções instruem o módulo **Limpar Dados Ausentes** a limpar os dados removendo as linhas que têm valores ausentes. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".

![Captura de tela mostrando a configuração correta do módulo Limpar Dados Ausentes](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>Treinar o modelo

Agora que os dados estão prontos, é possível construir um modelo preditivo. Você usará seus dados para treinar o modelo. Em seguida, você testará o modelo para ver o nível de precisão com o qual ele prevê os preços.

**Classificação** e **regressão** são dois tipos de técnicas de algoritmo de machine learning supervisionado. A **classificação** prevê uma resposta com base em um conjunto definido de categorias, como uma cor (vermelho, azul ou verde). A **regressão** é usada para prever um número.

Como você deseja prever o preço, que é um número, use um algoritmo de regressão. Para este exemplo, você usará um modelo de regressão linear.

Treine o modelo fornecendo a ele um conjunto de dados que inclua o preço. O modelo examina os dados e procura correlações entre os recursos de um carro e seu preço. Em seguida, teste o modelo fornecendo a ele um conjunto de recursos para automóveis com os quais ele está familiarizado e veja qual a proximidade da previsão feita pelo modelo com o preço conhecido.

Use seus dados para treinar o modelo e testá-lo, dividindo os dados em conjuntos de dados separados de treinamento e teste.

1. Digite **dividir dados** na caixa de pesquisa para encontrar o módulo **Dividir Dados** e conectá-lo à porta esquerda do módulo **Limpar Dados Ausentes**.

1. Selecione o módulo **Dividir Dados** ao qual você acabou de se conectar para selecioná-lo. No painel Propriedades, defina a Fração de linhas no primeiro conjunto de dados de saída como 0,7. Dessa forma, usaremos 70% dos dados para treinar o modelo e manteremos 30% para o teste.

    ![Captura de tela mostrando a configuração correta do painel Propriedades. Os valores de "Dividir Dados" devem ser "Dividir Linhas", 0,7, Divisão aleatória, 0 e Falso.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Clique duas vezes no módulo **Dividir Dados** e digite o comentário "Dividir o conjunto de dados em um conjunto de treinamento(0,7) e um conjunto de teste(0,3)"

1. Para selecionar o algoritmo de aprendizado, limpe a caixa de pesquisa da paleta de módulos.

1. Expanda o **Aprendizado de Máquina** e, em seguida, expanda **Inicializar Modelo**. Isso exibe várias categorias de módulos que podem ser usados para inicializar os algoritmos de Aprendizado de Máquina.

1. Para este teste, selecione **Regressão** > **Regressão Linear** e arraste-a para a tela do teste.

    ![Captura de tela mostrando a configuração correta do painel Propriedades. Os valores de "Dividir Dados" devem ser "Dividir Linhas", 0,7, Divisão aleatória, 0 e Falso.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Encontre e arraste o módulo **Treinar Modelo** para a tela do teste. Conecte a saída do módulo Regressão Linear à entrada esquerda do módulo Treinar Modelo e conecte a saída de dados de treinamento (porta esquerda) do módulo **Dividir Dados** à entrada direita do módulo **Treinar Modelo**.

    ![Captura de tela mostrando a configuração correta do módulo Treinar Modelo. O módulo Regressão Linear se conecta à porta esquerda do módulo Treinar Modelo e o módulo Dividir Dados se conecta à porta direita do módulo Treinar Modelo](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selecione o módulo **Treinar Modelo**. No painel Propriedades, selecione Iniciar seletor de colunas e, em seguida, digite **preço** ao lado de **Incluir nomes de coluna**. O preço é o valor que o modelo vai prever

    ![Captura de tela mostrando a configuração correta do módulo do seletor de colunas. Com regras > Incluir nomes de coluna > "preço"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Agora, o teste deverá ser semelhante ao mostrado a seguir.
    ![Captura de tela mostrando a configuração correta do teste após a adição do módulo Treinar Modelo.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Executar o teste de treinamento

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Pontuar e avaliar o modelo

Agora que você treinou o modelo usando 70% de seus dados, use-o para pontuar os outros 30% dos dados e ver se o modelo funciona bem.

1. Digite **pontuar modelo** na caixa de pesquisa para encontrar o módulo **Pontuar Modelo** e arraste o módulo para a tela do teste. Conecte a saída do módulo **Treinar Modelo** à porta de entrada esquerda do módulo **Pontuar Modelo**. Conecte a saída de dados de teste (porta direita) do módulo **Dividir Dados** à porta de entrada direita do módulo **Pontuar Modelo**.

1. Digite **avaliar** na caixa de pesquisa para encontrar o módulo **Avaliar Modelo** e arraste o módulo para a tela do teste. Conecte a saída do módulo **Pontuar Modelo** à entrada esquerda do módulo **Avaliar Modelo**. O experimento final deve se parecer como o seguinte:

    ![Captura de tela mostrando a configuração correta final do teste.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Execute o teste usando o mesmo destino de computação usado anteriormente.

1. Exiba a saída do módulo **Pontuar Modelo** selecionando a porta de saída do módulo **Pontuar Modelo** e selecione **Visualizar**. A saída mostra os valores previstos para o preço e os valores conhecidos dos dados de teste.

    ![Captura de tela da visualização de saída realçando a coluna "Rótulo Pontuado"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Para exibir a saída do módulo Avaliar Modelo, selecione a porta de saída e, em seguida, selecione Visualizar.

    ![Captura de tela mostrando os resultados da avaliação para o teste final.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

As seguintes estatísticas são mostradas para o modelo:

* **MAE (Média de Erros Absolutos)**: A média de erros absolutos (um erro é a diferença entre o valor previsto e o valor real).
* **RMSE (Raiz Quadrada da Média de Erros Quadrados)**: a raiz quadrada da média de erros quadrados de previsões feitas no conjunto de dados de teste.
* **Erro absoluto relativo**: a média de erros absolutos relativos à diferença absoluta entre os valores reais e a média de todos os valores reais.
* **Erro ao quadrado relativo**: a média de erros quadrados relativos à diferença quadrada entre os valores reais e a média de todos os valores reais.
* **Coeficiente de determinação**: Também conhecido como o valor de R-quadrado, essa é uma métrica estatística que indica se o modelo se encaixa bem nos dados.

Para cada estatística de erro, menos é melhor. Um valor menor indica que as previsões se aproximam mais dos valores reais. Para Coeficiente de Determinação, quanto mais próximo o valor estiver de um (1,0), melhores as previsões.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Gerenciar testes no workspace do Serviço do Azure Machine Learning

Os testes criados na interface visual podem ser gerenciados no workspace do Serviço do Azure Machine Learning. Use o workspace para ver informações mais detalhadas, como execuções de teste de indivíduos, logs de diagnóstico, grafos de execução, entre outros.

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  

1. No workspace, selecione **Testes**. Em seguida, selecione o teste que você criou.

    ![Captura de tela mostrando como navegar para os testes no portal do Azure](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Nessa página, você terá uma visão geral do teste e de suas execuções mais recentes.

    ![Captura de tela mostrando uma visão geral das estatísticas do teste no portal do Azure](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Selecione um número de execução para ver mais detalhes sobre uma execução específica.

    ![Captura de tela do relatório de execução detalhado](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    O relatório de execução é atualizado em tempo real. Se você usou um módulo **Executar Script do Python** no teste, especifique os logs de script a serem gerados na guia **Logs**.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Na primeira parte deste tutorial, você concluiu estas etapas:

* Reutilizar o teste criado no Início Rápido
* Preparar os dados
* Treinar o modelo
* Pontuar e avaliar o modelo

Na segunda parte, você aprenderá a implantar seu modelo como um serviço Web do Azure.

> [!div class="nextstepaction"]
> [Continuar a implantação de modelos](ui-tutorial-automobile-price-deploy.md)
