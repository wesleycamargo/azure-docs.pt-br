---
title: Moderar conteúdo usando revisões humanas no Azure Content Moderator | Microsoft Docs
description: Saiba como criar revisões humanas no console de API do Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 214695ed3e23d1f501d6d4691104b3f8a91f6efc
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866501"
---
# <a name="create-reviews-from-the-api-console"></a>Criar análises a partir do console de API

Use as [operações de análise](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) da API de Análise para criar análises de imagem ou texto para moderação humana. Moderadores humanos usam a Ferramenta de Análise para analisar o conteúdo. Use essa operação com base em sua lógica de negócios de pós-moderação. Use-o depois de você ter verificado seu conteúdo usando qualquer API de imagem ou texto do Content Moderator ou outras APIs de Serviços Cognitivos. 

Depois que um moderador humano revisa as marcas atribuídas automaticamente e os dados de previsão e envia uma decisão de moderação final, a API de Análise envia todas as informações para o ponto de extremidade de API.

## <a name="use-the-api-console"></a>Usar o console de API
Para testar a API usando o console online, você precisa de alguns valores para inserir no console:

- **teamName**: O nome da equipe que você criou quando configurou sua conta da ferramenta de análise. 
- **ContentId**: esta cadeia de caracteres é passada para a API e retornada por meio do retorno de chamada. A ContentId é útil para a associação de identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **Metadados**: pares de valores-chave personalizados retornados ao ponto de extremidade de API durante o retorno de chamada. Se a chave for um código curto que é definido na ferramenta de análise, ele aparece como uma marca.
- **Ocp-Apim-Subscription-Key**: localizado na guia **Configurações**. Para obter mais informações, confira a [Visão geral](overview.md).

É a maneira mais simples de acessar um console de teste da janela **Credenciais**.

1.  Na janela **Credenciais**, selecione [referência da API de Análise](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  A página **Análise - Criar** será aberta.

2.  Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.

  ![Análise - Criar página seleção de região](images/test-drive-region.png)

  O console de API **Análise - Criar** será aberto.
  
3.  Insira valores para os parâmetros de consulta necessários, o tipo de conteúdo e a sua chave de assinatura. Na caixa **Corpo da solicitação**, especifique o conteúdo (por exemplo, o local de imagem), metadados e outras informações associadas com o conteúdo.

  ![Análise - Criar console parâmetros de consulta, cabeçalhos e Caixa de corpo da solicitação](images/test-drive-review-1.PNG)
  
4.  Selecione **Enviar**. Uma ID de análise é criada. Copie esta ID para usar nas etapas a seguir.

  ![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-review-2.PNG)
  
5.  Selecione **Obter**e, em seguida, abra a API selecionando o botão que corresponde à sua região. Na página resultante, insira os valores para **teamName**, **ReviewID**, e **chave de assinatura**. Selecione o botão **Enviar** na página. 

  ![Análise - Criar console Obter resultados](images/test-drive-review-3.PNG)
  
6.  Você verá os resultados da varredura.

  ![Análise - Criar console Caixa de conteúdo de resposta](images/test-drive-review-4.PNG)
  
7.  No painel do Content Moderator, selecione **Análise** > **Imagem**. A imagem que você tiver examinado é exibida, pronta para revisão humana.

  ![Imagem da ferramenta de análise de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou comece com o [Início rápido do .NET de revisões](moderation-reviews-quickstart-dotnet.md) para integrar seu aplicativo.
