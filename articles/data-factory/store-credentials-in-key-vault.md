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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: f7604e251bd62ec382ac9ace3de058e345abb863
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que use o armazenamento de dados. Atualmente, apenas o [conector do Dynamics](connector-dynamics-crm-office-365.md) e [conector Salesforce](connector-salesforce.md) dão suporte a esse recurso.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está normalmente disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende da identidade de serviço da data factory. Saiba como ele funciona de [identidade de serviço de Data factory](data-factory-service-identity.md) e verifique se sua data factory tem um associado.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. [Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity) copiando o valor de "SERVIÇO DE IDENTIDADE ID DO APLICATIVO" gerado junto com seu alocador.
2. Conceder à identidade do serviço o acesso ao seu Azure Key Vault. Em seu cofre de chaves -> Controle de acesso -> Adicionar -> pesquise essa ID do aplicativo da identidade do serviço para adicionar permissão de **Leitura**. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. Criar um serviço vinculado que aponte para seu Azure Key Vault. Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. Criar um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves. Consulte [fazer referência a credencial armazenada no cofre de chaves](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | Sim |

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

## <a name="reference-credential-stored-in-key-vault"></a>Fazer referência a credencial armazenada no cofre de chaves

As propriedades a seguir têm suporte quando você configura um campo no serviço vinculado, referenciando um segredo do cofre de chaves:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | Sim |

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