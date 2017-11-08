---
title: Publicar um aplicativo gerenciado do Azure por meio do portal | Microsoft Docs
description: "Mostra como usar o portal do Azure para criar um aplicativo gerenciado do Azure destinado aos membros de sua organização."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2017
ms.author: tomfitz
ms.openlocfilehash: 9b938dbb817c0d82c92f358e4547fd95d8bae1b9
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicar um aplicativo de catálogo de serviços por meio do portal do Azure

Você pode usar o portal do Azure para publicar [aplicativos gerenciados](overview.md) destinados aos membros de sua organização. Por exemplo, um departamento de TI pode publicar aplicativos gerenciados que garantem a conformidade com os padrões organizacionais. Esses aplicativos gerenciados estão disponíveis por meio do catálogo de serviços, não pelo Azure Marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Ao publicar um aplicativo gerenciado, você pode especificar uma identidade para gerenciar os recursos. Recomendamos que você especifique um grupo de usuários do Azure Active Directory. Para criar um novo grupo de usuários do Azure Active Directory, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

O arquivo .zip que contém a definição de aplicativo gerenciado deve estar disponível por meio de um URI. É recomendável que você carregue seu arquivo .zip em um blob de armazenamento. 

## <a name="create-managed-application-with-portal"></a>Criar aplicativo gerenciado com o portal

1. No canto superior esquerdo, selecione **+Novo**.

   ![Novo serviço](./media/publish-portal/new.png)

1. Pesquise o **catálogo de serviços**.

1. Nos resultados, role até encontrar a **Definição de Aplicativo gerenciado do Catálogo de Serviços**. Selecione-o.

   ![Pesquisar por definições de aplicativo gerenciado](./media/publish-portal/select-managed-apps-definition.png)

1. Selecione **Criar** para iniciar o processo de criação da definição de aplicativo gerenciado.

   ![Criar a definição de aplicativo gerenciado](./media/publish-portal/create-definition.png)

1. Forneça valores para o nome, o nome de exibição, a descrição, o local, a assinatura e o grupo de recursos. Para o URI do arquivo de pacote, forneça o caminho para o arquivo zip que você criou.

   ![Forneça os valores](./media/publish-portal/fill-application-values.png)

1. Quando chegar até a seção de Autenticação e Nível de Bloqueio, selecione **Adicionar Autorização**.

   ![Adicionar autorização](./media/publish-portal/add-authorization.png)

1. Selecione um grupo do Azure Active Directory para gerenciar os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/publish-portal/add-auth-group.png)

1. Quando tiver fornecido todos os valores, selecione **Criar**.

   ![Criar aplicativo gerenciado](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](overview.md).
* Para obter exemplos de aplicativo gerenciado, veja [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para obter informações sobre como publicar aplicativos gerenciados para o Azure Marketplace, consulte [Aplicativos gerenciados do Azure no Marketplace](publish-marketplace-app.md).
* Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).