---
title: Criar uma máquina virtual do Linux com a API REST do Azure | Microsoft Docs
description: Saiba como criar uma máquina virtual do Linux no Azure que use Managed Disks e autenticação SSH com a API REST do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 0f77b46be0207b0ce96e6dc2562fb5298afbe36b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928175"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Criar uma máquina virtual do Linux que usa autenticação SSH com a API REST

Uma VM (máquina virtual) no Azure é definida por vários parâmetros, como local, tamanho do hardware, imagem do sistema operacional e as credenciais de logon. Este artigo mostra a você como usar a API REST para criar uma máquina virtual do Linux que usa autenticação SSH.

Para criar ou atualizar uma máquina virtual, use a seguinte operação *PUT*:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Criar uma solicitação

Para criar a solicitação *PUT*, o parâmetro `{subscription-id}` é obrigatório. Se você tiver várias assinaturas, consulte [Trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Defina `{resourceGroupName}` e `{vmName}` para seus recursos, junto com o parâmetro `api-version`. Este artigo usa `api-version=2017-12-01`.

Os cabeçalhos a seguir são necessários:

| Cabeçalho da solicitação   | DESCRIÇÃO |
|------------------|-----------------|
| *Tipo de Conteúdo:*  | Obrigatório. Defina como `application/json`. |
| *Autorização:* | Obrigatório. Defina como um `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para saber mais sobre como criar a solicitação, confira [Componentes de uma solicitação/resposta de API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As definições comuns a seguir são usadas para criar um corpo de solicitação:

| NOME                       | Obrigatório | Tipo                                                                                | DESCRIÇÃO  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | string                                                                              | Local do recurso. |
| Nome                       |          | string                                                                              | Nome da máquina virtual. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as configurações de hardware da máquina virtual. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as configurações de armazenamento dos discos da máquina virtual. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as configurações do sistema operacional da máquina virtual. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

Para obter uma lista completa das definições disponíveis no corpo da solicitação, confira [Máquinas virtuais criam ou atualizam definições do corpo de solicitação](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Exemplo do corpo de solicitação

O exemplo de corpo da solicitação a seguir define uma imagem Ubuntu 18.04-LTS que usa discos gerenciados Premium. A autenticação de chave pública SSH é usada, e a VM usa uma placa de adaptador de rede virtual (NIC) [criada anteriormente](../../virtual-network/virtual-network-network-interface.md). Forneça a chave pública SSH no campo *osProfile.linuxConfiguration.ssh.publicKeys.keyData*. Se for necessário, você pode [gerar um par de chaves SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Respostas

Há duas respostas bem-sucedidas para a operação criar ou atualizar uma máquina virtual:

| NOME        | Tipo                                                                              | DESCRIÇÃO |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Para saber mais sobre as respostas da API REST, veja [Processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exemplo de resposta

Uma resposta *201 Criado* condensada do exemplo anterior de corpo da solicitação que cria uma VM mostra que uma *vmId* foi atribuída e o *provisioningState* como *Criando*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as APIs REST do Azure ou outras ferramentas de gerenciamento, como a CLI do Azure 2.0 ou o Azure PowerShell, veja o seguinte:

- [API REST do provedor de Computação do Azure](/rest/api/compute/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)
- [CLI 2.0 do Azure](/cli/azure/)
- [Módulo do Azure PowerShell](/powershell/azure/overview)
