---
title: Gerenciar Plano do Serviço de Aplicativo - Azure | Microsoft Docs
description: Saiba como executar diferentes tarefas para gerenciar um Plano do Serviço de Aplicativo.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, trocar, criar, gerenciar, gerenciamento
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 936abe80a66c1dbe99e7d8a255fe8995a2df0803
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715640"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerenciar um plano do Serviço de Aplicativo no Azure

Um [Plano do Serviço de Aplicativo do Azure](overview-hosting-plans.md) fornece os recursos que um aplicativo de serviço de aplicativo precisa para ser executado. Este guia mostra como gerenciar um Plano do Serviço de Aplicativo.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

> [!TIP]
> Se você tem um Ambiente do Serviço de Aplicativo, consulte [Criar um Plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Você pode criar um Plano do Serviço de Aplicativo vazio ou pode criar um plano como parte da criação de um aplicativo.

1. No [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Web + móvel** e, em seguida, selecione **Aplicativo Web** ou outro tipo de aplicativo de Serviço de Aplicativo.

2. Selecione ou crie o Plano do Serviço de Aplicativo para o novo aplicativo.

   ![Crie um aplicativo no portal do Azure.][createWebApp]

   Para criar um plano:

    a. Selecione **[+] Criar novo**.

      ![Crie um plano do Serviço de Aplicativo.][createASP] 

   b. Para um **plano do Serviço de Aplicativo**, insira um nome.

   c. Em **Local**, selecione o local apropriado.

   d. Em **Camada de Preços**, selecione uma camada de preços apropriada para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. Depois de selecionar o tipo de preço, clique no botão **Selecionar** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover um aplicativo para um plano de Serviço de Aplicativo

Você pode mover um aplicativo para outro Plano do Serviço de Aplicativo, desde que o plano de origem e o plano de destino estejam no _mesmo grupo de recursos e a região geográfica_.

> [!NOTE]
> O Azure implanta cada novo plano do Serviço de Aplicativo em uma unidade de implantação, chamada internamente de espaço da Web. Cada região pode ter muitos espaços da Web, mas seu aplicativo só pode se mover entre planos criados no mesmo espaço da Web. Um Ambiente de Serviço de Aplicativo é um espaço da Web isolado, portanto, os aplicativos podem ser movidos entre planos no mesmo Ambiente de Serviço de Aplicativo, mas não entre planos em Ambientes de Serviço de Aplicativo diferentes.
>
> Você não pode especificar o espaço da web que deseja ao criar um plano, mas é possível garantir que um plano seja criado no mesmo espaço da Web de um plano existente. Em resumo, todos os planos criados com o mesmo grupo de recursos e combinação de regiões são implementados no mesmo espaço da web. Por exemplo, se você criou um plano no grupo de recursos A e na região B, qualquer plano criado subsequentemente no grupo de recursos A e na região B será implantado no mesmo espaço da Web. Observe que os planos não é possível mover espaços Web depois que elas forem criadas, para que você não pode mover um plano para "mesmo espaço da Web" como outro plano, movendo-o para outro grupo de recursos.
> 

1. No [portal do Azure](https://portal.azure.com), navegue até o aplicativo que você deseja mover.

1. No menu, procure a seção **Plano do Serviço de Aplicativo**.

1. Selecione **Alterar Plano do Serviço de Aplicativo** para abrir o seletor **Plano do Serviço de Aplicativo**.

   ![Seletor de plano do Serviço de Aplicativo.][change] 

1. No seletor de **plano do Serviço de Aplicativo**, selecione um plano existente para onde mover esse aplicativo.   

A página **Selecionar plano do serviço de aplicativos** mostra apenas planos que estão no mesmo grupo de recursos e região geográfica que o plano do Serviço de Aplicativo do aplicativo atual.

Cada plano tem seu próprio tipo de preço. Por exemplo, mover um site de uma camada **Gratuita** para uma camada **Standard** habilita todos os aplicativos aplicados a ela a usarem os recursos da camada **Standard**. No entanto, mover um aplicativo de um plano de camada superior para um plano de camada inferior significa que você não tem mais acesso a determinados recursos. Se seu aplicativo usa um recurso que não está disponível no plano de destino, você receberá um erro que mostra qual recurso está em uso que não está disponível. 

Por exemplo, se um dos seus aplicativos usa certificados SSL, você verá esta mensagem de erro:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Nesse caso, antes de poder mover o aplicativo para o plano de destino, você precisa:
- Expandir a camada de preços do plano de destino para **Básico** ou superior.
- Remova todas as conexões SSL a seu aplicativo.

## <a name="move-an-app-to-a-different-region"></a>Mover um aplicativo para uma região diferente

A região em que seu aplicativo é executado é a região do plano de serviço de aplicativo está. No entanto, você não pode alterar a região de um plano serviço de aplicativo. Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. A clonagem faz uma cópia do seu aplicativo em um novo plano do Serviço de Aplicativo existente em qualquer região.

É possível localizar **Clonar aplicativo** na seção **Ferramentas de Desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Você pode ler sobre elas em [Clonagem de aplicativo do Serviço de Aplicativo do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de Serviço de Aplicativo

Para dimensionar um plano do Serviço de Aplicativo da camada de preços, consulte [Dimensionar um aplicativo no Azure](web-sites-scale.md).

Para dimensionamento horizontal de contagem de instância do aplicativo, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Deletar um plano do Serviço de Aplicativo

Para evitar encargos inesperados, quando você excluir o último aplicativo em um plano de serviço de aplicativo, o serviço de aplicativo também exclui o plano por padrão. Se optar por manter o plano em vez disso, você deve alterar o plano para camada **Gratuita** para que você não seja cobrado.

> [!IMPORTANT]
> Os planos do Serviço de Aplicativo que não têm aplicativos associados a eles ainda incorrerão em encargos, pois continuam a reservar as instâncias da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Expandir um aplicativo no Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
