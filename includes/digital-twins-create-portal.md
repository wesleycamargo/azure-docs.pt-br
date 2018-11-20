---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/17/2018
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: e5e015c1e12e68f54ae7fd32082faed1abfebe74
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51626381"
---
1. Entre no [Portal do Azure](http://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**. Pesquise **gêmeos digitais**e selecione **Gêmeos Digitais (versão prévia)**. Selecione **Criar** para iniciar o processo de implantação.

   ![Seleções para criar uma nova instância dos Gêmeos Digitais](./media/create-digital-twins-portal/create-digital-twins.png)

1. No painel **Gêmeos Digitais**, insira as seguintes informações:
   * **Nome do recurso**: criar um nome exclusivo para sua instância de Gêmeos Digitais.
   * **Assinatura**: escolha a assinatura que deseja usar para criar essa instância de Gêmeos Digitais. 
   * **Grupo de recursos**: selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância de Gêmeos Digitais.
   * **Local**: selecione o local mais próximo para seus dispositivos.

    ![Painel Gêmeos Digitais com informações inseridas](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Examine as informações de Gêmeos Digitais e, em seguida, selecione **Criar**. Sua instância de Gêmeos Digitais pode levar alguns minutos para ser criada. Você pode monitorar o progresso no painel **Notificações**.

1. Abra o painel **Visão geral** da sua instância de Gêmeos Digitais. Observe o link em **API de Gerenciamento**.

   A URL de **API de Gerenciamento** é formatada como: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_. Esta URL leva você até a documentação da API REST de Gêmeos Digitais do Azure que se aplica à sua instância. Leia [Como usar o Swagger dos Gêmeos Digitais do Azure](../articles/digital-twins/how-to-use-swagger.md) para aprender a ler e usar essa documentação da API.

    Modifique a URL da **API de Gerenciamento** para este formato: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Seu aplicativo usará a URL modificada como a URL base para acessar a instância. Copie esta URL modificada para um arquivo temporário. Você precisará disso nas próximas seções.

    ![API de gerenciamento](./media/create-digital-twins-portal/digital-twins-management-api.png)