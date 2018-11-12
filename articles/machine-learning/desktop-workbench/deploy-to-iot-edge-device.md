---
title: Implantar um modelo de aprendizado de máquina do Azure em um dispositivo de IoT Edge do Azure | Microsoft Docs
description: Este documento descreve como os modelos de aprendizado de máquina do Azure podem ser implantados em dispositivos de IoT Edge do Azure.
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 7322b07740d5dec85b6217e122fb262647527c96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258401"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>Implantar um modelo de aprendizado de máquina do Azure em um dispositivo Azure IoT Edge

Os modelos do Azure Machine Learning podem ser colocados em contêineres como serviços Web baseados em Docker. O Azure IoT Edge permite que você implante contêineres remotamente em dispositivos. Use esses serviços em conjunto para executar seus modelos na borda a fim de obter tempos de resposta mais rápidos e um volumem menor de transferência de dados. 

Instruções e scripts adicionais podem ser encontradas no [Kit de ferramentas do AI do Azure IoT Edge](https://aka.ms/AI-toolkit).

## <a name="operationalize-the-model"></a>Operacionalizar o modelo

Os módulos do Azure IoT Edge se baseiam em imagens de contêiner. Para implantar seu modelo de machine learning em um dispositivo IoT Edge, você precisará criar uma imagem do Docker.

Operacionalizar seu modelo, seguindo as instruções em [implantação de serviço da Web de Gerenciamento de Modelos do Machine Learning do Azure](model-management-service-deploy.md) para criar uma imagem do Docker com o modelo.

## <a name="deploy-to-azure-iot-edge"></a>Implantar o Azure IoT Edge

Quando tiver a imagem de seu modelo, você poderá implantá-la em qualquer dispositivo Azure IoT Edge. Todos os modelos de Machine Learning podem ser executado em dispositivos de IoT Edge. 

### <a name="set-up-an-iot-edge-device"></a>Configurar um dispositivo IoT Edge

Use a documentação do Azure IoT Edge para preparar um dispositivo. 

1. [Registrar um dispositivo no Hub IoT do Azure](../../iot-edge/how-to-register-device-portal.md). A saída desses processos é uma cadeia de conexão que você pode usar para configurar seu dispositivo físico. 
2. Instale o tempo de execução do IoT Edge em seu dispositivo físico e configure-o com uma cadeia de conexão. Você pode instalar o tempo de execução em dispositivos [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) ou [Linux](../../iot-edge/how-to-install-iot-edge-linux.md).  


### <a name="find-the-machine-learning-container-image-location"></a>Encontrar o local de imagem de contêiner de Machine Learning
É necessário o local da sua imagem de contêiner de Machine Learning. Para encontrar o local da imagem de contêiner:

1. Faça logon no [Portal do Azure](http://portal.azure.com/).
2. No **Registro de Contêiner do Azure**, selecione o Registro que deseja inspecionar.
3. No Registro, clique em **Repositórios** para ver uma lista de todos os repositórios e suas imagens.

Enquanto estiver examinando seu registro de contêiner no portal do Azure, recupere as credenciais do registro de contêiner. Essas credenciais precisam ser fornecidas para o dispositivo IoT Edge para que ele possa efetuar pull da imagem de seu registro privado. 

1. No registro de contêiner, clique em **Chaves de acesso**. 
2. **Habilite** o usuário administrador se ele ainda não estiver habilitado. 
3. Salve os valores de **Servidor de logon**, **Nome de usuário** e **Senha**. 

### <a name="deploy-the-container-image-to-your-device"></a>Implante a imagem de contêiner em seu dispositivo

Com a imagem de contêiner e as credenciais do registro de contêiner, você está pronto para implantar o modelo de machine learning em seu dispositivo IoT Edge. 

Siga as instruções em [Implantar módulos do IoT Edge do portal do Azure](../../iot-edge/how-to-deploy-modules-portal.md) para inicializar o modelo em seu dispositivo IoT Edge. 











