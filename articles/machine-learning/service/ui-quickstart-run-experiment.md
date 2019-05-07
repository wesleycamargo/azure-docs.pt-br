---
title: 'Início Rápido: Preparar e visualizar dados sem escrever código'
titleSuffix: Azure Machine Learning service
description: Crie um teste do aprendizado de máquina para preparar e visualizar seus dados com uma interface do usuário do tipo "arrastar e soltar".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 985ad59a1ffa5bd68f97824f7f50af604174b543
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030054"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code"></a>Início Rápido: Preparar e visualizar dados sem escrever código

Preparar e visualizar seus dados com uma interface do usuário do tipo "arrastar e soltar". Os dados que você usará incluem entradas para vários automóveis individuais, incluindo informações como marca, modelo, especificações técnicas e preço.  

Neste início rápido, você explorará e preparará dados:

- Crie seu primeiro teste para adicionar e visualizar dados
- Preparar os dados removendo os valores ausentes
- Execute o experimento
- Visualizar os dados resultantes

Se você é novato em aprendizado de máquina, a série de vídeos [Ciência de Dados para Iniciantes](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) é uma excelente introdução ao aprendizado de máquina.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

### <a name="create-a-workspace"></a>Criar um workspace

Se você tiver um workspace do Serviço do Azure Machine Learning, passe para a [próxima seção](#start). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Abra a página da Web de interface visual

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  

1. No workspace, selecione **Interface visual**.  Em seguida, selecione **Iniciar interface visual**.  
 
    ![Iniciar interface visual](./media/ui-quickstart-run-experiment/launch-ui.png)

    A página da Web da interface será aberta em uma nova página do navegador.  

## <a name="create-your-first-experiment"></a>Criar seu primeiro experimento

A ferramenta de interface visual fornece um lugar visual e interativo para criar, testar e iterar em um modelo de análise preditiva com facilidade. Arraste e solte conjuntos de dados e módulos de análise em uma tela interativa, unindo-os para formar um _teste_.  Crie seu primeiro teste agora.

1. No canto inferior esquerdo, selecione **Adicionar novo**.
![Adicionar novo teste](./media/ui-quickstart-run-experiment/add-new.png)

1. Selecione **Teste em branco**.

1. Seu teste recebe um nome padrão. Selecione esse texto e renomeie-o para o "Início rápido-explorar dados". Esse nome não precisa ser único.

1. O **Minimapa** na parte inferior da tela é útil para exibir testes grandes.  Você não precisará dele neste início rápido; portanto, clique na seta na parte superior para minimizá-lo.  

    ![Renomear o teste](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Adicionar dados

A primeira coisa de que você precisa para o aprendizado de máquina são os dados. Há vários conjuntos de dados de exemplo incluídos nessa interface que você pode usar, ou é possível importar dados de muitas fontes. Neste exemplo, você usará o conjunto de dados de exemplo **Dados de preço de automóvel (brutos)**. 

1. À esquerda da tela do experimento está uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de Dados Salvos**; em seguida, selecione **Exemplos** para exibir os conjuntos de dados de exemplo disponíveis.

1. Selecione o conjunto de dados, **Dados de preço de automóvel (brutos)** e arraste-o para a tela.

   ![Arraste os dados para a tela](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Selecionar colunas

Selecione com quais colunas de dados você deseja trabalhar.  Para começar, configure o módulo para mostrar todas as colunas disponíveis.

> [!TIP]
> Se você souber o nome dos dados ou do módulo desejado, use a barra de pesquisa na parte superior da paleta para localizá-los rapidamente.  O restante do início rápido usará esse atalho.

1. Digite **Selecionar** na caixa de pesquisa para localizar o módulo **Selecionar colunas no conjunto de dados**.

1. Clique e arraste **Selecionar colunas no conjunto de dados** para a tela. Solte o módulo embaixo do conjunto de dados adicionado anteriormente.

1. Conecte o conjunto de dados a **Selecionar colunas no conjunto de dados**: clique na porta de saída do conjunto de dados, arraste para a porta de entrada de **Selecionar colunas no conjunto de dados** e, em seguida, solte o botão do mouse. O conjunto de dados e o módulo permanecem conectados mesmo se você mover um deles nas telas.

    > [!TIP]
    > Os módulos e conjuntos de dados têm portas de entrada e saída representadas por círculos pequenos – portas de entrada na parte superior, portas de saída na parte inferior. Crie um fluxo de dados por meio do seu teste quando você conectar a porta de saída de um módulo a uma porta de entrada de outro.
    >
    > Se tiver problemas para conectar módulos, tente arrastar para dentro do nó que você está conectando.

    ![Conectar módulos](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    O ponto de exclamação vermelho indica que você não definiu as propriedades do módulo ainda. Você fará isso em seguida.
   
1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel **Propriedades** à direita da tela, selecione **Iniciar seletor de coluna**.

    Na caixa de diálogo **Selecionar colunas**, selecione **TODAS AS COLUNAS** e inclua **todos os recursos**. A caixa de diálogo deve ter esta aparência:

     ![column-selector](./media/ui-quickstart-run-experiment/select-all.png)

1. No canto inferior direito, selecione o botão de marca de verificação (OK) para fechar o seletor de coluna.

## <a name="run-the-experiment"></a>Execute o experimento

A qualquer momento, clique na porta de saída de um conjunto de dados ou de um módulo para ver qual é a aparência dos dados nesse momento no fluxo de dados.  Se a opção **Visualizar** estiver desabilitada, primeiro será necessário executar o teste.  Você fará isso em seguida.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Depois que o destino de computação estiver disponível, o teste será executado. Quando a execução for concluída, uma marca de verificação verde será exibida em cada módulo.

![Exibir status](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Visualizar os dados

Agora que você executou o teste inicial, pode visualizar os dados para entender mais sobre as informações com as quais precisa trabalhar.

1. Selecione a porta de saída na parte inferior de **Selecionar colunas no conjunto de dados** e, em seguida, selecione **Visualizar**.

1. Clique em diferentes colunas na janela de dados para exibir informações sobre ela.  

    No conjunto de dados de exemplo, cada linha representa uma instância de um automóvel e as variáveis associadas a cada automóvel aparecem como colunas.    Há 205 linhas e 26 colunas nesse conjunto de dados.

     Cada vez que você clica em uma coluna de dados, as informações de **Estatísticas** e a imagem de **Visualização** dessa coluna são exibidas à esquerda.  Por exemplo, ao clicar em **num-of-doors**, você verá que ela tem dois valores exclusivos e dois valores ausentes.  Role para baixo para ver os valores: duas e quatro portas.

     ![Visualizar os dados](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Clique em cada coluna para entender mais sobre seu conjunto de dados.

## <a name="prepare-data"></a>Preparar dados

Um conjunto de dados geralmente requer algum pré-processamento antes de poder ser analisado. Você deve ter observado os valores ausentes presentes nas colunas de várias linhas. Os valores ausentes precisam ser limpos para que o modelo possa analisar os dados corretamente. Você removerá quaisquer linhas com valores ausentes. Além disso, a coluna **normalized-losses** tem uma grande proporção de valores ausentes; portanto, você a excluirá do modelo completamente.

> [!TIP]
> Limpar os valores ausentes dos dados de entrada é um pré-requisito para usar a maioria dos módulos.  

### <a name="remove-column"></a>Remover coluna

Primeiro, remova a coluna **normalized-losses** completamente.

1. Selecione o módulo **Selecionar colunas no conjunto de dados**.

1. No painel **Propriedades** à direita da tela, selecione **Iniciar seletor de coluna**.

    * Deixe **Com regras** e **TODAS AS COLUNAS** selecionado.

    * Nos menus suspensos, selecione **Excluir** e **nomes da coluna** e clique dentro da caixa de texto. Digite **normalized-losses**.

    * No canto inferior direito, selecione o botão de marca de verificação (OK) para fechar o seletor de coluna.

    ![Excluir uma coluna](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Agora o painel de propriedades para Selecionar colunas no Conjunto de dados indica que ele passará todas as colunas do conjunto de dados, exceto **normalized-losses**.
        
    O painel de propriedades mostra que a coluna **normalized-losses** foi excluída.
        
    ![Painel de propriedade](./media/ui-quickstart-run-experiment/property-pane.png)
        
    É possível adicionar um comentário em um módulo ao clicar duas vezes nele e inserir o texto. Isso pode ajudar a ver rapidamente o que o módulo está fazendo em seu experimento. 

1. Clique duas vezes no módulo **Selecionar Colunas no Conjunto de Dados** e digite o comentário "Excluir perdas normalizadas." 
    
    Após digitar o comentário, clique fora do módulo.  Uma seta para baixo é exibida para mostrar que o módulo contém um comentário.

1. Clique na seta para baixo para exibir o comentário.

    Agora o módulo mostra uma seta para cima para ocultar o comentário.
        
    ![Comentários](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Limpar dados ausentes

Agora adicione outro módulo que remove qualquer linha restante que tenha dados ausentes.

1. Digite **Limpar** na caixa de pesquisa para encontrar o módulo **Limpar Dados Ausentes**.

1. Arraste o módulo **Limpar Dados Ausentes** para a tela do teste e conecte-o ao módulo **Selecionar Colunas no Conjunto de Dados**. 

1. No painel Propriedades, selecione **Remover linha inteira** em **Modo de limpeza**.

    Essas opções instruem o módulo **Limpar Dados Ausentes** a limpar os dados removendo as linhas que têm valores ausentes.

1. Clique duas vezes no módulo e digite o comentário “Remover linhas de valor ausente".
 
    ![Remover linhas](./media/ui-quickstart-run-experiment/remove-rows.png)

    Agora seu teste deve ter a seguinte aparência:
    
    ![select-column](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualizar os resultados

Coo você fez alterações nos módulos do seu teste, o status mudou para “Em rascunho”.  Para visualizar os novos dados limpos, primeiro você precisa executar o teste novamente.

1. Selecione **Executar** na parte inferior para executar o teste.

    Neste momento, é possível reutilizar o destino de computação que você criou anteriormente.  

1. Selecione **Executar** na caixa de diálogo.

   ![Executar o experimento](./media/ui-quickstart-run-experiment/select-compute.png)

1. Após a conclusão da execução, clique com o botão direito do mouse no módulo **Limpar Dados Ausentes** para visualizar os novos dados limpos.  

    ![Visualizar dados limpos](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Clique em colunas diferentes na janela de dados limpos para ver como os dados mudaram.  

    ![Visualizar dados limpos](media/ui-quickstart-run-experiment/visualize-result.png)

    Agora há 193 linhas e 25 colunas.

    Ao clicar em **num-of-doors**, você verá que ele ainda tem dois valores exclusivos, mas agora tem 0 valores ausentes.  

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Crie seu primeiro teste para adicionar e visualizar dados
- Preparar os dados removendo os valores ausentes
- Visualizar os dados resultantes

Passe para o tutorial para usar esses dados para prever o preço de um automóvel.

> [!div class="nextstepaction"]
> [Tutorial: Prever o preço de automóveis com a interface visual](ui-tutorial-automobile-price-train-score.md)
