---
title: Armazenar credenciais no Azure Key Vault | Microsoft Docs
description: Saiba como armazenar as credenciais para os armazenamentos de dados usados em um Azure Key Vault, de modo que o Azure Data Factory possa recuperá-las automaticamente em tempo de execução.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896204"
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados e computar em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

No momento, todos os tipos de atividade, exceto a atividade personalizada suportam esse recurso. Para configuração de conector especificamente, verifique a seção "propriedades do serviço vinculadas" em [cada tópico conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende da identidade do data factory gerenciado. Saiba como ele funciona de [identidade de fábrica de dados gerenciado](data-factory-service-identity.md) e certifique-se de seu data factory tem um associado.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. **Recuperar a identidade do data factory gerenciado** copiando o valor de "Serviço de identidade ID do aplicativo" gerado junto com seu alocador. Se você usar o ADF criação da interface do usuário, a ID do aplicativo gerenciado de identidade será mostrada na janela de criação do serviço vinculado do Azure Key Vault; Você também pode recuperá-lo do portal do Azure, consulte [fábrica de dados de recuperar a identidade gerenciada](data-factory-service-identity.md#retrieve-managed-identity).
2. **Conceda o acesso de identidade gerenciada para seu Azure Key Vault.** Em seu Cofre de chaves -> acesso políticas -> Adicionar Novo -> pesquisa isso gerenciado ID do aplicativo de identidade conceder **obter** permissão na lista suspensa permissões secretas. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. **Crie um serviço vinculado que aponte para seu Azure Key Vault.** Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves.** Consulte [fazer referência a segredo armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AzureKeyVault**. | Sim |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | Sim |

**Usando a criação da interface do usuário:**

Clique em **Conexões** -> **Serviços vinculados** -> **+ Novo** -> procure "Azure Key Vault":

![Pesquisar AKV](media/store-credentials-in-key-vault/search-akv.png)

Selecione o Azure Key Vault provisionado onde as credenciais são armazenadas. Você pode fazer uma **Conexão de teste** para garantir que sua conexão AKV seja válida. 

![Configurar AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Exemplo de JSON:**

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
| Tipo | A propriedade de tipo do campo deve ser definida como: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Azure Key Vault. | Sim |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não  |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | Sim |

**Usando a criação da interface do usuário:**

Selecione **Azure Key Vault** para os campos secretos ao criar a conexão para seu armazenamento de dados/computação. Selecione o serviço vinculado do Azure Key Vault provisionados e forneça o **Nome secreto**. Opcionalmente, você pode fornecer uma versão do segredo também. 

>[!TIP]
>Para conectores usando a cadeia de caracteres de conexão no serviço vinculado como o SQL Server, armazenamento de BLOBs, etc., você pode escolher armazenar somente o campo secreto, por exemplo, a senha no AKV ou armazenar a cadeia de conexão toda em AKV. Você pode encontrar as duas opções da interface do usuário.

![Configurar segredo do AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemplo de JSON: (consulte a seção "senha")**

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
