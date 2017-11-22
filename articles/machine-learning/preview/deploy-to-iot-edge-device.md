---
title: "Implantar um modelo de aprendizado de máquina do Azure em um dispositivo de IoT Edge do Azure | Microsoft Docs"
description: "Este documento descreve como os modelos de aprendizado de máquina do Azure podem ser implantados em dispositivos de IoT Edge do Azure."
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 924766aee7486f0cf5006dd89d5d77b83753833b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Implantar um modelo de aprendizado de máquina do Azure em um dispositivo de IoT Edge do Azure | Microsoft Docs

Todos os modelos de aprendizado de máquina do Azure em contêineres como serviços web baseados em Docker também podem executar em dispositivos de IoT Edge do Azure. Instruções e scripts adicionais podem ser encontradas no [Kit de ferramentas do AI do Azure IoT Edge](http://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operacionalizar o modelo
Operacionalizar seu modelo, seguindo as instruções em [implantação de serviço da Web de Gerenciamento de Modelos do Machine Learning do Azure](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy) para criar uma imagem do Docker com o modelo.

## <a name="deploy-to-azure-iot-edge"></a>Implantar o Azure IoT Edge
IoT Edge do Azure move a análise de nuvem e lógica de negócios personalizada para dispositivos. Todos os modelos de Machine Learning podem ser executado em dispositivos de IoT Edge. A documentação para configurar um dispositivo de IoT Edge e criar uma implantação pode ser encontrada em [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc).

Os seguintes são coisas adicionais a observar.

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>Adicionar credenciais de registro para o tempo de execução de Edge em seu dispositivo Edge
No computador em que você está executando IoT Edge, adicione as credenciais do registro para que o tempo de execução pode ter acesso a retirar o contêiner.

Para Windows, execute o comando a seguir:
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
Para Linux, execute o comando a seguir:
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>Encontrar o local de imagem de contêiner de Machine Learning
É necessário o local da sua imagem de contêiner de Machine Learning. Para encontrar o local da imagem de contêiner:

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
2. No **Registro de Contêiner do Azure**, selecione o Registro que deseja inspecionar.
3. No Registro, clique em **Repositórios** para ver uma lista de todos os repositórios e suas imagens.













