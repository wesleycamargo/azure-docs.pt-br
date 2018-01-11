---
title: "Gerenciar um Plano do Serviço de Aplicativo no Azure | Microsoft Docs"
description: "Saiba como os Planos do Serviço de Aplicativo executam diferentes tarefas para gerenciar um Plano do Serviço de Aplicativo."
keywords: "serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, trocar, criar, gerenciar, gerenciamento"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 61179c5bf29ed2c338b45ba909ec01237806cf26
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerenciar um plano do Serviço de Aplicativo no Azure

Um [Plano do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) fornece os recursos de um aplicativo de serviço de aplicativo precisa ser executado. Este guia de instruções mostra como gerenciar um Plano do Serviço de Aplicativo.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

> [!TIP]
> Se você tem um Ambiente do Serviço de Aplicativo, consulte [Criar um Plano do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Você pode criar um Plano do Serviço de Aplicativo vazio ou como parte da criação de um aplicativo.

No [Portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + Móvel** e, em seguida, selecione **Aplicativo Web** ou outra variante de aplicativo de Serviço de Aplicativo.

![Crie um aplicativo no portal do Azure.][createWebApp]

Você pode selecionar ou criar o Plano do Serviço de Aplicativo para o novo aplicativo.

 ![Crie um plano do Serviço de Aplicativo.][createASP]

Para criar um Plano do Serviço de Aplicativo, clique em **[+] Criar Novo**, digite o nome do **Plano do Serviço de Aplicativo** e selecione uma **Localização** apropriada. Clique em **Tipo de preço**e selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preço, como **Gratuito** e **Compartilhado**. 

Depois de selecionar o tipo de preço, clique no botão **Selecionar** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover um aplicativo para um plano de Serviço de Aplicativo

Você pode mover um aplicativo para outro Plano do Serviço de Aplicativo, desde que o plano de origem e o plano de destino estejam no _mesmo grupo de recursos e a região geográfica_.

Para mover um aplicativo para outro plano, vá para o aplicativo que deseja mover no [portal do Azure](https://portal.azure.com).

No **Menu**, procure a seção **Plano do Serviço de Aplicativo**.

Selecione **Alterar plano do Serviço de Aplicativo** para iniciar o processo.

**Alterar Plano do Serviço de Aplicativo** abre o seletor **Plano do Serviço de Aplicativo**. Selecione um plano existente para mover este aplicativo. 

> [!IMPORTANT]
> A página de **Plano do Serviço de Aplicativo** é filtrada pelos seguintes critérios: 
> - Existe dentro do mesmo grupo de recursos 
> - Existe na mesma região geográfica 
> - Existe dentro do mesmo espaço da Web  
> 
> Um _espaço Web_ é uma construção lógica dentro do Serviço de Aplicativo que define um agrupamento de recursos do servidor. Uma região geográfica (por exemplo, Oeste dos EUA) contém muitos espaços na Web para alocar os clientes que usam o Serviço de Aplicativo. No momento, os recursos do Serviço de Aplicativo não podem ser movidos entre espaços da Web. 
> 

![Seletor de plano do Serviço de Aplicativo.][change]

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Cada plano tem seu próprio tipo de preço. Por exemplo, mover um site de uma camada **Gratuita** para uma camada **Standard** habilita todos os aplicativos aplicados a ela a usarem os recursos da camada **Standard**. No entanto, mover um aplicativo de um plano de camada superior para um plano de camada inferior significa que você não tem mais acesso a determinados recursos. Se seu aplicativo usa um recurso que não está disponível no plano de destino, você receberá um erro que mostra qual recurso está em uso que não está disponível. Por exemplo, se um dos seus aplicativos usa certificados SSL, você poderá ver a mensagem de erro: `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`nesse caso, você precisa aumentar o preço do plano de destino para **Básico** ou superior, ou você precisa remover todas as conexões SSL seu aplicativo, antes de você poder mover o aplicativo para o plano de destino.

## <a name="move-an-app-to-a-different-region"></a>Mover um aplicativo para uma região diferente

A região em que seu aplicativo é executado é a região do plano de serviço de aplicativo está. No entanto, você não pode alterar a região de um plano serviço de aplicativo. Se desejar mover o aplicativo para uma região diferente, uma alternativa será a clonagem do aplicativo. A clonagem faz uma cópia do seu aplicativo em um novo plano do Serviço de Aplicativo existente em qualquer região.

É possível localizar **Clonar aplicativo** na seção **Ferramentas de Desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações sobre as quais você pode ler em [Clonagem de aplicativo do Serviço de Aplicativo do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de Serviço de Aplicativo

Para dimensionar um plano do Serviço de Aplicativo da camada de preços, consulte [Dimensionar um aplicativo no Azure](web-sites-scale.md).

Para dimensionamento horizontal de contagem de instância do aplicativo, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Deletar um plano do Serviço de Aplicativo

Para evitar encargos inesperados, quando você excluir o último aplicativo em um plano de serviço de aplicativo, o serviço de aplicativo também exclui o plano por padrão. Se optar por manter o plano em vez disso, você deve alterar o plano da camada **Livre** para que você não seja cobrado.

> [!IMPORTANT]
> Os **Planos do Serviço de Aplicativo** que não têm aplicativos associados a eles ainda incorrerão em encargos, pois continuam a reservar as instâncias da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Expandir um aplicativo no Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
