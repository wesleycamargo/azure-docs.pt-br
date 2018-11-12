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
ms.openlocfilehash: 7bac2b291bec2ceda118c877cde997a2fa9f9f37
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283965"
---
1. Entre no [Portal do Azure](http://portal.azure.com).

1. No painel de navegação à esquerda, clique em **Criar um recurso**. Pesquise *gêmeos digitais*e selecione **Gêmeos Digitais (versão prévia)**. Clique em **Criar** para iniciar o processo de implantação.

    ![Criar Gêmeos Digitais](./media/create-digital-twins-portal/create-digital-twins.png)

1. No painel **Gêmeos Digitais**, insira as seguintes informações:
   * **Nome do recurso**: criar um nome exclusivo para sua instância de Gêmeos Digitais.
   * **Assinatura**: escolha a assinatura que deseja usar para criar essa instância de Gêmeos Digitais. 
   * **Grupo de recursos**: selecione ou crie um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) para a instância de Gêmeos Digitais.
   * **Local**: selecione o local mais próximo para seus dispositivos.

    ![Criar Gêmeos Digitais](./media/create-digital-twins-portal/create-digital-twins-param.png)

1. Examine as informações dos Gêmeos Digitais e clique em **Criar**. Sua instância de Gêmeos Digitais pode levar alguns minutos para ser criada. Você pode monitorar o progresso no painel **Notificações**.

1. Abra o painel **Visão geral** da sua instância de Gêmeos Digitais. Observe o link sob **API de gerenciamento**.

    1. A URL de **API de gerenciamento** é formatada como: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger_**. Esta URL leva você até a documentação da API REST de Gêmeos Digitais do Azure que se aplica à sua instância. Leia [Como usar o Swagger dos Gêmeos Digitais do Azure](../articles/digital-twins/how-to-use-swagger.md) para aprender a ler e usar essa documentação da API.

    1. Modifique a URL da **API de gerenciamento** para este formato: **_https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_**. Seu aplicativo usará a URL modificada como a URL base para acessar a instância. Copie esta URL modificada para um arquivo temporário. Você precisará dela na seção seguinte.

    ![APIs de gerenciamento](./media/create-digital-twins-portal/digital-twins-management-api.png)