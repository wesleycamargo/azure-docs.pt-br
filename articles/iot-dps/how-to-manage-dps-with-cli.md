---
title: "Como usar a CLI do Azure 2.0 e a extensão de IoT para gerenciar serviços de provisionamento de dispositivos | Microsoft Docs"
description: "Saiba como usar a CLI do Azure 2.0 e a extensão de IoT para gerenciar serviços de provisionamento de dispositivos"
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Como usar a CLI do Azure 2.0 e a extensão de IoT para gerenciar serviços de provisionamento de dispositivos

A [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) é uma ferramenta de linha de comando de software livre para gerenciamento dos recursos do Azure como, por exemplo, o IoT Edge. A CLI do Azure 2.0 está disponível para Windows, Linux e MacOS. A CLI do Azure 2.0 permite que você gerencie instantaneamente recursos, instâncias de serviço de provisionamento de dispositivos e hubs vinculados do Hub IoT do Azure.

A extensão de IoT aprimora a CLI do Azure 2.0 com recursos como gerenciamento de dispositivos e todos os recursos do IoT Edge.

Neste tutorial, você primeiro conclui as etapas para configurar a CLI do Azure 2.0 e a extensão de IoT. Em seguida, você aprende como executar comandos CLI para executar operações básicas de serviço de provisionamento de dispositivos. 

## <a name="installation"></a>Instalação 

### <a name="step-1---install-python"></a>Etapa 1 - Instale o Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) são necessários.

### <a name="step-2---install-azure-cli-20"></a>Etapa 2 - Instale a CLI do Azure 2.0

Siga as [instruções de instalação](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure 2.0 no seu ambiente. A versão 2.0 da CLI do Azure deve ser 2.0.24 ou superior. Use `az –version` para validar. Esta versão dá suporte aos comandos da extensão az e introduz a estrutura de comandos Knack. Uma maneira simples de instalar no Windows é baixar e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Etapa 3 - Instale a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-cli-iot-ext`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-cli-iot-ext` para ver os detalhes sobre a extensão de IoT. Para remover a extensão, você pode usar `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operações básicas do serviço de provisionamento de dispositivos
O exemplo mostra como fazer logon na sua conta do Azure, criar um Grupo de Recursos do Azure (um contêiner que contém recursos relacionados para uma solução do Azure), criar um Hub IoT, criar um serviço de provisionamento de dispositivos, listar os serviços de provisionamento de dispositivos existentes e criar um Hub IoT vinculado com os comandos da CLI. 

Conclua as etapas de instalação descritas anteriormente antes de começar. Se você ainda não tiver uma conta do Azure, você pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje. 


### <a name="1-log-in-to-the-azure-account"></a>1. Fazer logon na conta do Azure
  
    az login

![logon][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Criar um grupo de recursos IoTHubBlogDemo no eastus

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Criar dois serviços de provisionamento de dispositivo

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Criar DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Listar todos os serviços de provisionamento de dispositivos existentes neste grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Listar DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Criar um blogDemoHub do Hub IoT no grupo de recursos recentemente criado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar Hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Vincular o Hub IoT a um serviço de provisionamento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Vincular o Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
