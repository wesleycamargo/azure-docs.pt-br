---
title: Azure Disk Encryption para o Windows | Microsoft Docs
description: Implante o Azure Disk Encryption na máquina virtual do Windows usando uma extensão da máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: f9b8c64aadf813ab1157820fc4051d48989e48c4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448696"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Visão geral

O Azure Disk Encryption aproveita o Bitlocker para fornecer criptografia de disco completo em máquinas virtuais do Azure executando o Windows.  Está solução é integrada ao Azure Key Vault para gerenciar os segredos e as chaves de criptografia de disco em sua assinatura do cofre de chaves. 

## <a name="prerequisites"></a>pré-requisitos

Para obter uma lista completa dos pré-requisitos, consulte [Pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Sistema operacional

Para obter uma lista das versões do Windows no momento, consulte [Pré-requisitos do Azure Disk Encryption](../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="internet-connectivity"></a>Conectividade com a Internet

O Azure Disk Encryption exige conectividade com a Internet para acesso ao Active Directory, o Cofre de chaves, armazenamento e pontos de extremidade de gerenciamento de pacote.  Para saber mais sobre configurações de segurança de rede, consulte [Pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption.md#prerequisites).

## <a name="extension-schema"></a>Esquema de extensão

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| NOME | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publicador | Microsoft.Azure.Security | string |
| Tipo | AzureDiskEncryptionForWindows| string |
| typeHandlerVersion | 1.0, 2.2 (VMSS) | int |
| (opcional) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (opcional) AADClientSecret | Senha | string |
| (opcional) AADClientCertificate | impressão digital | string |
| EncryptionOperation | EnableEncryption | string | 
| KeyEncryptionAlgorithm | RSA-OAEP | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Sistema operacional, Dados, Tudo | string |

## <a name="template-deployment"></a>Implantação de modelo
Para obter um exemplo de implantação de modelo, consulte [Criar uma nova VM do Windows criptografada de imagem da galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

As instruções podem ser encontradas na última [documentação da CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Consulte o [Guia de solução de problemas do Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Windows](features-windows.md).