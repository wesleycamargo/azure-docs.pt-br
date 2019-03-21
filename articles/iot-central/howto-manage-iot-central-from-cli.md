---
title: Gerenciar Central IoT da CLI do Azure | Microsoft Docs
description: Gerencie Central IoT da CLI do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809078"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerenciar Central IoT da CLI do Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar aplicativos de IoT Central da IoT Central [Gerenciador de aplicativos](https://aka.ms/iotcentral) página, você pode usar [CLI do Azure](/cli/azure/) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você preferir executar a CLI do Azure em seu computador local, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli). Ao executar a CLI do Azure localmente, use o **login az** comando para entrar no Azure antes de tentar os comandos neste artigo.

## <a name="create-an-application"></a>Criar um aplicativo

Use o [az iotcentral aplicativo criar](/cli/azure/iotcentral/app#az-iotcentral-app-create) comando para criar um aplicativo Central da IoT em sua assinatura do Azure. Por exemplo: 

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Primeiro, esses comandos criam um grupo de recursos em que a região Leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o **az iotcentral aplicativo criar** comando:

| Parâmetro         | DESCRIÇÃO |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém o aplicativo. Esse grupo de recursos já precisa existir na sua assinatura. |
| location          | Por padrão, esse comando usa o local do grupo de recursos. No momento, você pode criar um aplicativo do IoT Central nas regiões **Leste dos EUA**, **Oeste dos EUA**, **Europa Setentrional** ou **Europa Ocidental**. |
| Nome              | Digite o nome do aplicativo no portal do Azure. |
| Subdomínio         | O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é https://mysubdomain.azureiotcentral.com. |
| sku               | Atualmente, o único valor é **S1** (camada standard). Confira [Preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | O modelo de aplicativo a usar. Para obter mais informações, confira a tabela a seguir: |
| display-name      | O nome do aplicativo, conforme exibido na interface do usuário. |

**Modelos de aplicativo**

| Nome do modelo            | DESCRIÇÃO |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |
| iotc-demo@1.0.0          | Cria um aplicativo que inclui um modelo de dispositivo já criado para uma Máquina de Vendas Refrigerada. Use esse modelo para começar a explorar o Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos experimentando com algum desses dispositivos. |

## <a name="view-your-applications"></a>Exibir seus aplicativos

Use o [lista de aplicativos do az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-list) comando para listar seus aplicativos de IoT Central e exibir os metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o [atualização de aplicativo az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-update) comando para atualizar os metadados de um aplicativo de IoT Central. Por exemplo, para alterar o nome de exibição do aplicativo:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover um aplicativo

Use o [exclusão de aplicativo do az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-delete) comando para excluir um aplicativo de IoT Central. Por exemplo: 

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar aplicativos do Azure IoT Central da CLI do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)
