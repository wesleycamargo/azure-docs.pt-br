---
title: Mover um projeto de avaliação limitado para o Azure
titlesuffix: Azure Cognitive Services
description: Saiba como mover um projeto de avaliação limitada para o Azure.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: anroth
ms.openlocfilehash: a9f49af54f391b159f8b3d626fffc36635f5e51f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821295"
---
# <a name="how-to-move-your-limited-trial-project-to-azure-using-the-customvisionai-site"></a>Como mover seu projeto de avaliação limitada para o Azure usando o site CustomVision.ai


Como o serviço de visão personalizada agora está em [versão prévia do Azure](https://azure.microsoft.com/services/preview/), suporte para projetos de avaliação limitada fora do Azure está terminando. Este documento lhe ensinará como usar o [site de visão personalizada](https://customvision.ai) para mover o seu projeto de avaliação limitada a ser associado um recurso do Azure. 

> [!NOTE]
> Quando você move seus projetos de visão personalizada para um recurso do Azure, eles herdar subjacente [permissões]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) do recurso do Azure. Se outros usuários em sua organização são proprietários do seu projeto estiver no recurso do Azure, eles poderão acessar seu projeto na [site de visão personalizada](https://customvision.ai). Da mesma forma, a exclusão de seus recursos excluirá seus projetos.  


Para obter uma introdução aos conceitos de assinaturas e recursos do Azure, consulte o [guia do desenvolvedor do Azure.](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#manage-your-subscriptions)


## <a name="prerequisites"></a>Pré-requisitos

Você precisará de uma assinatura do Azure válida associada com a mesma conta da Microsoft ou conta do Azure Active Directory (AAD) usada para fazer logon na [site de visão personalizada](https://customvision.ai). 

Se você não tiver uma conta do Azure, [criar uma conta](https://azure.microsoft.com/free/) gratuitamente.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Criar recursos de visão personalizada no portal do Azure
Para usar o serviço de visão personalizada com o Azure, você precisará criar recursos de treinamento de visão personalizada e previsão na [portal do Azure](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). 

 Para mover o seu projeto usando esse [site de visão personalizada](https://customvision.ai) experiência, você deve criar seus recursos na região Centro-Sul dos EUA, como todos os projetos de avaliação limitado são hospedados no Centro-Sul dos EUA. 

Vários projetos podem ser associados a um recurso único. Mais detalhes sobre [preços e limites](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/limits-and-quotas) está disponível. Para continuar a usar o serviço personalizado de visão gratuitamente, você pode selecionar a classe F0 no portal do Azure. 


## <a name="move-your-limited-trial-project-to-an-azure-resource"></a>Mova o projeto de avaliação limitada para um recurso do Azure

1.  No navegador da web, navegue até a [site de visão personalizada](https://customvision.ai) e selecione __entrar__. Abra o projeto que você deseja migrar para uma conta do Azure. 
2.  Abra a página de configurações para seu projeto clicando no ícone de engrenagem no canto superior direito da tela. 

    ![Configurações do projeto é o ícone de engrenagem no canto superior direito da página do projeto.](./media/move-your-project-to-azure/settings-icon.png)


3. Clique em __mover para o Azure__.

    ![Mova para o botão do Azure está na parte inferior esquerda da página Configurações do projeto.](./media/move-your-project-to-azure/move-to-azure.jpg)


4. Na lista suspensa a __mover para o Azure__ botão, selecione o recurso do Azure que você deseja mover seu projeto. Clique em __mover__. 

5. Se você não vir o recurso do Azure que você criou anteriormente para o serviço de visão personalizada, ele pode ser em outro diretório. Para mover o seu projeto para um recurso em outro diretório, siga as instruções abaixo. 

    ![Janela de migração do projeto.](./media/move-your-project-to-azure/Project_Migration_Window.jpg)


## <a name="move-project-to-another-azure-directory"></a>Mover o projeto para outro diretório do Azure 

> [!NOTE]
> No portal do Azure e para CustomVision.ai, você pode selecionar seu diretório do usuário no menu suspenso menu no canto superior direito da tela.   


1. Identifique qual diretório o recurso do Azure está em. Você pode encontrar o diretório listado em seu nome de usuário no canto superior direito da barra de menus do portal do Azure. 

    ![Seu diretório está listado em seu nome de usuário no canto superior direito da barra de menus do portal do Azure. .](./media/move-your-project-to-azure/identify_directory.jpg)

2. Localize a ID de recurso do seu recurso de treinamento de visão personalizada. Você pode encontrar isso no portal do Azure, abra seu recurso de treinamento de visão personalizada e selecione "Propriedades" na seção "Gerenciamento de recursos". Sua ID de recurso estará lá. 

    ![Localize sua ID de recurso no portal do Azure, abra seu recurso de treinamento de visão personalizada e selecione "Propriedades" na seção "Gerenciamento de recursos".](./media/move-your-project-to-azure/resource_ID_azure_portal.jpg)


3. Como alternativa, você pode encontrar a ID de recurso do seu recurso de visão personalizada diretamente no site de visão personalizada [página de configurações]( https://www.customvision.ai/projects#/settings). Você precisará alternar para o recurso do Azure está no mesmo diretório.

    ![Sua ID de recurso é listado para cada recurso em sua página de configurações no site da visão personalizada.](./media/move-your-project-to-azure/resource_ID_CVS_portal.jpg)

4. Agora que você tem a ID de recurso, retorne para o projeto de visão personalizada que você está tentando mover de uma avaliação limitada para um recurso do Azure. Como lembrete, você talvez precise voltar para seu diretório original para encontrá-lo. Siga as instruções fornecidas [acima](#move-your-limited-trial-project-to-an-azure-resource) para abrir a página de configurações do projeto e selecione __mover para o Azure__. 


5. Na mudança para a janela do Azure, marque a caixa para "Mover para um diretório diferente do Azure?". Selecione o diretório que você deseja mover seu projeto e insira a ID de recurso do recurso que você está movendo seu projeto para. Clique em __mover__. 



5. Lembre-se de que seu projeto agora está em um diretório diferente. Para encontrar seu projeto, você precisará alternar para o mesmo diretório no portal da web de visão personalizada que seu projeto está em. No portal do Azure e o [site de visão personalizada](https://customvision.ai), você pode selecionar seu diretório no menu suspenso de conta no canto superior direito da tela. 

## <a name="next-steps"></a>Próximas etapas

Seu projeto agora foi movido para um recurso do Azure. Você precisará atualizar suas chaves de treinamento e previsão em quaisquer aplicativos que você tenha escrito.
