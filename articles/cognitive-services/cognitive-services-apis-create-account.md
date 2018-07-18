---
title: Criar uma conta de APIs de Serviços Cognitivos no portal do Azure | Microsoft Docs
description: Como criar uma conta de APIs de Serviços Cognitivos da Microsoft no portal do Azure.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363789"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Criar uma conta de APIs de Serviços Cognitivos no Portal do Azure

Para usar APIs de Serviços Cognitivos da Microsoft, primeiro você precisa criar uma conta no portal do Azure.

1. Entre no [Portal do Azure](http://portal.azure.com).

2. Clique em **+ Criar um recurso**.

3. No Azure Marketplace, selecione **AI + Serviços Cognitivos** e descubra a lista de APIs disponíveis. Clique em **Ver todos** para ver toda a lista de APIs de Serviços Cognitivos. Clique na API de sua escolha para prosseguir.

    ![Selecionar APIs de Serviços Cognitivos](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Na página **Criar**, forneça as seguintes informações:

   - **Nome da Conta:** nome da conta. Recomendamos o uso de um nome descritivo, por exemplo *AFaceAPIAccount*.

   - **Assinatura:** selecione uma das assinaturas do Azure disponíveis nas qual você tem, no mínimo, permissões de Colaborador.

   - **Tipo de API:** escolha a API de Serviços Cognitivos que você deseja usar. Para obter mais informações sobre as várias APIs de Serviços Cognitivos disponíveis, visite o site [Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/).

   - **Tipo de preço:** o custo da conta dos Serviços Cognitivos depende do uso real e das opções escolhidas. Para obter mais informações sobre o preço de cada API, consulte as [páginas de preços](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Grupo de Recursos:** um grupo de recursos é uma coleção de recursos que compartilham o mesmo ciclo de vida, permissões e políticas. Para saber mais sobre Grupos de Recursos, consulte [Gerenciar recursos do Azure pelo portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Local do Grupo de Recursos:** isso é necessário apenas se a API selecionada for global (não limitado a um local). No entanto, se a API for global e não estiver limitada a uma localização, será necessário especificar uma localização para o grupo de recursos em que os metadados associados à conta da API de Serviços Cognitivos residem. Essa localização não tem impacto sobre a disponibilidade de tempo de execução da conta. Para saber mais sobre grupos de recursos, consulte [Gerenciar recursos do Azure pelo portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Reconhecimento explícito dos Termos de Serviços Online:** para criar uma conta, os Colaboradores ou Proprietários da assinatura (conforme definido pelo [Controle de Acesso Baseado em Função](https://docs.microsoft.com/azure/role-based-access-control/overview)) precisam reconhecer explicitamente os termos que aplicam-se aos Serviços Cognitivos nos [Termos de Serviços Online](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     O Proprietário da assinatura pode desabilitar a criação da conta dos Serviços Cognitivos para um grupo de recursos ou uma assinatura específica por meio das [Políticas do Azure](../azure-policy/azure-policy-introduction.md), seguindo o artigo [Usar o portal do Azure para atribuir e gerenciar políticas de recursos](../azure-policy/assign-policy-definition.md), atribuindo uma definição de política “Tipos de recursos não permitidos” e especificando **Microsoft.CognitiveServices/accounts** como o tipo de recurso de destino.

     Se a criação da conta estiver desabilitada, o erro s seguir será exibido no momento da criação da conta:

     ![Erro de criação de conta](media/cognitive-services-apis-create-account/error-message.png)

5. Para fixar a conta no painel do portal do Azure, clique em **Fixar no Painel**.

6. Clique em **Criar** para criar a conta.

Depois que a conta dos Serviços Cognitivos for implantada com êxito, clique no bloco do painel para exibir as informações da conta.

É possível usar a **URL do Ponto de Extremidade** na seção **Visão Geral** e as chaves na seção **Chaves** para começar a fazer chamadas à API nos aplicativos.

![Exibir informações da conta](media/cognitive-services-apis-create-account/display-account.png)

![Exibir chaves da conta](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre todos os Serviços Cognitivos da Microsoft disponíveis, consulte [Serviços Cognitivos](https://azure.microsoft.com/services/cognitive-services/).

Para obter guias de início rápido sobre o uso de algumas APIs de Serviços Cognitivos de exemplo:

 - [Guias de início rápido do C# da Pesquisa Visual Computacional](/computer-vision/quickstarts/csharp.md)
 - [Análise de Texto com Python](/text-analytics/quickstarts/python.md)
 - [API de Detecção Facial com JavaScript](/face/quickstarts/javascript.md)
