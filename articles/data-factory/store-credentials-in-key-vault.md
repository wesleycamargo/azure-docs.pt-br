---
title: Armazenar credenciais no Azure Key Vault | Microsoft Docs
description: "Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em tempo de execução."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 51318a6bb4f88759984531b005c65712b6817ec0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados e computar em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

No momento, todos os tipos de atividade, exceto a atividade personalizada suportam esse recurso. Para configuração de conector especificamente, verifique a seção "propriedades do serviço vinculadas" em [cada tópico conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>pré-requisitos

Esse recurso depende da identidade de serviço da data factory. Saiba como ele funciona de [identidade de serviço de Data factory](data-factory-service-identity.md) e verifique se sua data factory tem um associado.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. **[Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity)** copiando o valor de "ID DO APLICATIVO DE IDENTIDADE DO SERVIÇO" gerado junto com seu alocador.
2. **Conceda à identidade do serviço o acesso ao seu Azure Key Vault.** No seu cofre de chaves -> Políticas de acesso -> Adicionar novo -> pesquise esta ID do aplicativo de identidade do serviço para conceder permissão **Get** na lista suspensa Permissões secretas. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. **Crie um serviço vinculado que aponte para seu Azure Key Vault.** Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves.** Consulte [fazer referência a segredo armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AzureKeyVault**. | sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | sim |

**Exemplo:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Fazer referência a segredo armazenado no cofre de chaves

As propriedades a seguir têm suporte quando você configura um campo no serviço vinculado, referenciando um segredo do cofre de chaves:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | sim |
| secretName | O nome do segredo no Azure Key Vault. | sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não  |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | sim |

**Exemplo: (consulte a seção "senha")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).