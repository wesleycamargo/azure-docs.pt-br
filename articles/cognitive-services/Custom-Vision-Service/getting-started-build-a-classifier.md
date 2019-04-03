---
title: Criar um classificador – Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como usar o site Visão Personalizada para criar um modelo de classificação de imagem.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: cf8a36145ebf5d5dabf8c539d4f245e1d4b209f0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886430"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Como compilar um classificador com Visão Personalizada

Para usar o Serviço de Visão Personalizada para classificação de imagens, primeiramente você deve criar um modelo de classificador. Neste guia, você aprenderá a criar um classificador usando o site Visão Personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura válida do Azure. [Criar uma conta](https://azure.microsoft.com/free/) gratuitamente.
- Um conjunto de imagens com o qual treinar o seu classificador. Veja abaixo as dicas sobre como escolher imagens.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no portal do Azure
Para usar o serviço de Visão Personalizada, você precisará criar recursos de treinamento e previsão de Visão Personalizada no [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). Isso criará um recurso de treinamento e previsão. 

## <a name="create-a-new-project"></a>Criar um novo projeto

No navegador da Web, navegue até o [site Visão Personalizada](https://customvision.ai) e selecione __Entrar__. Entrar com a mesma conta usada para entrar no portal do Azure.

![Imagem da página de entrada](./media/browser-home.png)


1. Para criar seu primeiro projeto, selecione **Novo Projeto**. A caixa de diálogo **Criar novo projeto** será exibida.

    ![A caixa de diálogo do novo projeto tem campos para nome, descrição e domínios.](./media/getting-started-build-a-classifier/new-project.png)

1. Insira um nome e uma descrição para o projeto. Selecione um Grupo de Recursos. Se a sua conta conectada estiver associada a uma conta do Azure, a lista suspensa Grupo de Recursos exibirá todos os seus Grupos de Recursos do Azure que contêm um Recurso do Serviço de Visão Personalizada. 

   > [!NOTE]
   > Se nenhum grupo de recursos está disponível, confirme que você fez logon na [customvision.ai](https://customvision.ai) com a mesma conta que é usada para fazer logon na [portal do Azure](https://portal.azure.com/). Além disso, confirme se você selecionou o mesmo “Diretório” no portal de Visão Personalizada que o diretório no portal do Azure onde se encontram seus recursos de Visão Personalizada. Em ambos os sites, é possível selecionar seu diretório no menu suspenso de conta no canto superior direito da tela. 

1. Selecione __Classificação__ em __Tipos de Projeto__. Em __Tipos de Classificação__, escolha **Multirótulo** ou **Multiclasse**, dependendo do seu caso de uso. A classificação multirótulo aplica qualquer número de marcas a uma imagem (zero ou mais), enquanto a classificação multiclasse agrupa as imagens em categorias únicas (cada imagem enviada será classificada de acordo com a marca mais provável). Você pode alterar o tipo de classificação posteriormente, se desejar.

1. Em seguida, selecione um dos domínios disponíveis. Cada domínio otimiza o classificador para tipos específicos de imagem, conforme descrito na tabela a seguir. Você pode alterar o domínio posteriormente, se desejar.

    |Domínio|Finalidade|
    |---|---|
    |__Genérico__| Otimizado para uma ampla gama de tarefas de classificação de imagens. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio Genérico. |
    |__Alimentos__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Caso queira classificar fotografias de frutas ou legumes individuais, use o domínio Alimentos.|
    |__Pontos de referência__|Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Este domínio funciona mesmo quando o ponto de referência está um pouco obstruído devido a pessoas na frente dele.|
    |__Varejo__|Otimizado para imagens encontradas em um catálogo ou site de compras. Caso deseje uma classificação de alta precisão entre vestidos, calças e camisas, use esse domínio.|
    |__Adulto__|Otimizado para oferecer uma melhor definição entre conteúdo adulto e não adulto. Por exemplo, se você quiser bloquear imagens de pessoas em trajes de banho, esse domínio permite que você crie um classificador personalizado para fazer isso.|
    |__Domínios compactos__| Otimizados para as restrições de classificação em tempo real em dispositivos móveis. Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente.|
    
1. Por fim, selecione __Criar projeto__.

## <a name="choose-training-images"></a>Escolher imagens de treinamento

É recomendável usar pelo menos 30 imagens por marca no conjunto de treinamento inicial. Também é conveniente coletar algumas imagens adicionais para testar o seu modelo após o treinamento.

Para treinar o seu modelo com eficiência, use imagens com variedade de visual. Selecione imagens com essa variação por:
* ângulo da câmera
* iluminação
* background
* estilo do visual
* assuntos individuais/agrupados
* tamanho
* Tipo

Além disso, certifique-se de que todas as suas imagens de treinamento atendam aos seguintes critérios:
* formato .jpg, .png ou .bmp
* não ultrapasse 6 MB em tamanho (4 MB para imagens de previsão)
* não tenha menos de 256 pixels na borda mais curta; qualquer imagem menor que isso será escalada verticalmente de maneira automática pelo Serviço de Visão Personalizada

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Nesta seção, você vai carregar e marcar manualmente imagens que ajudam a treinar o classificador. 

1. Para adicionar imagens, clique no botão __Adicionar imagens__ e selecione __Procurar arquivos locais__. Selecione __Abrir__ para mover para a marcação. A sua seleção de marca será aplicada a todo o grupo de imagens selecionado para upload, de modo que é mais fácil carregar imagens em grupos separados de acordo com as respectivas marcas desejadas. Você também pode alterar as marcas das imagens individuais depois que elas tiverem sido carregadas.

    ![O controle de adição de imagens é mostrado no canto superior esquerdo e como um botão na parte inferior central.](./media/getting-started-build-a-classifier/add-images01.png)


1. Para criar uma marca, digite o texto no campo __Minhas Marcas__ e pressione Enter. Se a marca já existir, ela aparecerá em um menu suspenso. Em um projeto multirótulo, é possível adicionar mais de uma marca às suas imagens, mas em um projeto multiclasse, só é possível adicionar uma. Para finalizar o carregamento das imagens, use o botão __Carregar [número] arquivos__. 

    ![Imagem da página de marca e upload](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecione __Concluído__ depois que as imagens tiverem sido carregadas.

    ![A barra de progresso mostra todas as tarefas concluídas.](./media/getting-started-build-a-classifier/add-images04.png)

Para carregar outro conjunto de imagens, retorne ao topo desta seção e repita as etapas.

## <a name="train-the-classifier"></a>Treinar o classificador

Para treinar o classificador, selecione o botão **Treinar**. O classificador usa todas as imagens atuais para criar um modelo que identifique as qualidades visuais de cada marca.

![Botão Treinar no canto superior direito na barra de ferramentas do cabeçalho da página da Web](./media/getting-started-build-a-classifier/train01.png)

O processo de treinamento deve levar apenas alguns minutos. Durante esse tempo, informações sobre o processo de treinamento são exibidas na barra **Desempenho**.

![A janela do navegador com uma caixa de diálogo de treinamento na seção principal](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Avaliar o classificador

Depois que o treinamento estiver concluído, o desempenho do modelo será estimado e exibido. O Serviço de Visão Personalizada usa as imagens que você enviou para treinamento para calcular a precisão e a recuperação usando um processo chamado [validação cruzada k-fold](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). A precisão e a recuperação são duas medidas diferentes da eficácia de um classificador:

- A **precisão** indica a fração de classificações identificadas que estão corretas. Por exemplo, se o modelo identificou 100 imagens como cachorros e 99 delas são realmente de cachorros, a precisão é de 99%.
- A **recuperação** indica a fração de classificações reais que foram corretamente identificadas. Por exemplo, se há de fato 100 imagens de maçãs e o modelo identifica 80 como maçãs, a recuperação é de 80%.

![Os resultados de treinamento mostram a precisão e recuperação geral e de cada marca no classificador.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Limite de probabilidade

Observe o controle deslizante **Limite de Probabilidade** no painel à esquerda da guia **Desempenho**. Esse é o limite para que uma probabilidade prevista seja considerada correta no cálculo da precisão e da recuperação.

Interpretar chamadas de previsão com um limite alto de probabilidade tende a retornar resultados com alta precisão às custas da recuperação (as classificações encontradas estão corretas, mas muitas não são encontradas); um limite baixo de probabilidade faz o oposto (a maioria das classificações reais é encontrada, mas há falsos positivos nesse conjunto). Com isso em mente, você deve definir o limite de probabilidade de acordo com as necessidades específicas do seu projeto. Posteriormente, no lado do cliente, você deve usar o mesmo valor de limite de probabilidade como um filtro ao receber os resultados de previsão do modelo.

## <a name="manage-training-iterations"></a>Gerenciar iterações de treinamento

Toda vez que você treina o classificador, cria uma nova _iteração_ com as suas próprias métricas de desempenho atualizadas. É possível exibir todas as iterações no painel à esquerda da guia **Desempenho**. Ao selecionar uma, você tem a opção de torná-la a _iteração padrão_ clicando no botão **Tornar padrão** na parte superior. A _iteração padrão_ é o modelo que será usado por padrão quando você consultá-lo por meio da API de Previsão (em um aplicativo, por exemplo). Se você recusar a atualização da _iteração padrão_, é possível continuar treinando o modelo sem afetar o comportamento atual do aplicativo; quando estiver satisfeito com o modelo aprimorado, atualize o padrão.

No painel à esquerda, também é possível encontrar o botão **Excluir**, que pode ser usado para excluir uma iteração se ela estiver obsoleta. Ao excluir uma iteração, você exclui qualquer imagem que esteja exclusivamente associada a ela.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a criar e treinar um modelo de classificação de imagem usando o site Visão Personalizada. A seguir, saiba mais sobre o processo iterativo para melhorar o seu modelo.

[Teste e treinar novamente um modelo](test-your-model.md)

