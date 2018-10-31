---
title: Entidades predefinidas para LUIS (Reconhecimento vocal)
titleSuffix: Azure Cognitive Services
description: O LUIS incluem um conjunto de entidades predefinidas para reconhecer tipos comuns de informações, como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647588"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entidades predefinidas para reconhecer os tipos de dados comuns

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações como datas, horas, números, medidas e moeda. 

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 

1. Na página **Entidades**, clique em **Gerenciar entidades predefinidas**.

1. Na caixa de diálogo **Adicionar as entidades predefinidas**, selecione o datetimeV2 entidade predefinida. 

    ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecione **Concluído**.

## <a name="publish-the-app"></a>Publicar o aplicativo

A maneira mais fácil para exibir o valor de uma entidade predefinida é a consulta de ponto de extremidade publicado. 

1. Na barra de ferramentas superior, selecione **Publicar**. Publique em **Produção**. 

1. Quando a notificação de êxito verde aparecer, selecione o link **Referir-se à lista de pontos de extremidade** para ver os pontos de extremidade.

1. Selecionar um ponto de extremidade. Uma nova guia do navegador é aberta para esse ponto de extremidade. Mantenha a guia do navegador aberta e continue na seção **Testar**.

## <a name="test"></a>Teste
Depois que a entidade é adicionada, você precisa treinar o aplicativo. 

Teste a nova intenção no ponto de extremidade adicionando um valor para o parâmetro **q**. Use a tabela a seguir para declarações sugeridas para **q**:

|Enunciado do teste|Valor de entidade|
|--|:--|
|Reservar um voo para amanhã|19-10-2018|
|cancelar o compromisso em 3 de março|LUIS retornou o mais recente 3 de março no passado (03-03-2018) e 3 de março do futuro (03-03-2019) porque o enunciado não especificou um ano.|
|Agendar uma reunião às 10h|10:00:00|


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Referência de entidade predefinida](./luis-reference-prebuilt-entities.md)
