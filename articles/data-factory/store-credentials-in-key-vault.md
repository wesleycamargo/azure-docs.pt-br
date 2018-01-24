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
ms.date: 12/14/2017
ms.author: jingwang
ms.openlocfilehash: 145c2bc0556010389e78e523fde6fd4b9063f930
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que use o armazenamento de dados.

Atualmente, o [conector do Dynamics](connector-dynamics-crm-office-365.md) e [conector do Salesforce](connector-salesforce.md), e alguns conectores recentemente habilitados, dão suporte a esse recurso. Espere mais em breve. Você pode verificar cada tópico de conector detalhadamente. Para os campos secretos que dão suporte a esse recurso, você verá uma nota na descrição indicando que "*Você pode optar por marcar este campo como um SecureString para armazená-lo com segurança no ADF, ou armazenar a senha no Azure Key Vault e permitir que o pull de atividade de cópia possa agir a partir daí ao executar a cópia de dados - Saiba mais em Armazenar Credenciais no Key Vault.*"

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>pré-requisitos

Esse recurso depende da identidade de serviço da data factory. Saiba como ele funciona de [identidade de serviço de Data factory](data-factory-service-identity.md) e verifique se sua data factory tem um associado.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. [Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity) copiando o valor de "SERVIÇO DE IDENTIDADE ID DO APLICATIVO" gerado junto com seu alocador.
2. Conceder à identidade do serviço o acesso ao seu Azure Key Vault. Em seu cofre de chaves -> Controle de acesso -> Adicionar -> pesquise essa ID do aplicativo da identidade do serviço para adicionar permissão de **Leitura**. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. Criar um serviço vinculado que aponte para seu Azure Key Vault. Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. Criar um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves. Consulte [fazer referência a credencial armazenada no cofre de chaves](#reference-credential-stored-in-key-vault).

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

## <a name="reference-credential-stored-in-key-vault"></a>Fazer referência a credencial armazenada no cofre de chaves

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