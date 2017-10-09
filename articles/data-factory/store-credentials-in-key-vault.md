---
title: Armazenar credenciais no Azure Key Vault | Microsoft Docs
description: "Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em tempo de execução."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que use o armazenamento de dados.

> [!NOTE]
> Atualmente, apenas o [conector do Dynamics](connector-dynamics-crm-office-365.md) dá suporte a esse recurso. 

## <a name="steps"></a>Etapas

Ao criar um data factory, uma identidade do serviço é criada ao mesmo tempo. A identidade do serviço é um aplicativo gerenciado registrado no Azure Active Directory e representa esse data factory específico. Você pode encontrar as informações sobre a identidade do serviço no Portal do Azure -> seu data factory -> Propriedades: 

- ID DA IDENTIDADE DO SERVIÇO
- LOCATÁRIO DA IDENTIDADE DO SERVIÇO
- ID DO APLICATIVO DA IDENTIDADE DO SERVIÇO

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. Copiar a "ID DO APLICATIVO DA IDENTIDADE DO SERVIÇO" gerada junto com seu data factory.
2. Conceder à identidade do serviço o acesso ao seu Azure Key Vault. Em seu cofre de chaves -> Controle de acesso -> Adicionar -> pesquise essa ID do aplicativo da identidade do serviço para adicionar permissão de leitura. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. Criar um serviço vinculado que aponte para seu Azure Key Vault. Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. Criar um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves. Consulte [Fazer referência a credencial armazenada no cofre de chaves](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifica a URL (nome DNS) do Azure Key Vault. | Sim |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
