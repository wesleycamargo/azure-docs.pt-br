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
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2017
ms.author: jingwang
ms.openlocfilehash: 3428fb5034435d9f3444347329171d803136177c
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944661"
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credencial no Azure Key Vault

Você pode armazenar credenciais para armazenamentos de dados e computar em um [Azure Key Vault](../key-vault/key-vault-whatis.md). O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

No momento, todos os tipos de atividade, exceto a atividade personalizada suportam esse recurso. Para configuração de conector especificamente, verifique a seção "propriedades do serviço vinculadas" em [cada tópico conector](copy-activity-overview.md#supported-data-stores-and-formats) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Esse recurso depende da identidade de serviço da data factory. Saiba como ele funciona de [identidade de serviço de Data factory](data-factory-service-identity.md) e verifique se sua data factory tem um associado.

>[!TIP]
>No Azure Key Vault, quando você cria um segredo, **coloque todo o valor de uma propriedade secreta solicitada pelo serviço vinculado do ADF (por exemplo, cadeia de conexão/senha/chave da entidade de serviço/etc)**. Por exemplo, para o serviço vinculado ao Armazenamento do Azure, insira `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` como segredo AKV e a referência no campo "connectionString" do ADF; para o serviço vinculado do Dynamics, insira `myPassword` como segredo AKV e, em seguida, a referência no campo "senha" do ADF. Consulte o artigo de cada conector/computação nos detalhes de propriedades com suporte.

## <a name="steps"></a>Etapas

Para referenciar uma credencial armazenada no Azure Key Vault, você precisa:

1. **Recuperar a identidade do serviço de data factory** copiando o valor de "SERVIÇO DE IDENTIDADE ID DO APLICATIVO" gerado junto com seu alocador. Se você usar a IU de criação do ADF, a ID de identidade de serviço será exibida na janela de criação de serviço vinculado do Azure Key Vault; você também poderá recuperá-lo no portal do Azure, consulte [Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity).
2. **Conceda à identidade do serviço o acesso ao seu Azure Key Vault.** No seu cofre de chaves -> Políticas de acesso -> Adicionar novo -> pesquise esta ID do aplicativo de identidade do serviço para conceder permissão **Get** na lista suspensa Permissões secretas. Isso permite que esse factory específico acesse o segredo no cofre de chaves.
3. **Crie um serviço vinculado que aponte para seu Azure Key Vault.** Consulte [Serviço vinculado do Azure Key Vault](#azure-key-vault-linked-service).
4. **Crie um serviço vinculado de armazenamento de dados, dentro do qual fazer referência ao segredo correspondente armazenado no cofre de chaves.** Consulte [fazer referência a segredo armazenado no cofre de chaves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Serviço vinculado do Azure Key Vault

As propriedades a seguir têm suporte no serviço vinculado do Azure Key Vault:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AzureKeyVault**. | SIM |
| baseUrl | Especifique a URL (nome DNS) do Azure Key Vault. | SIM |

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
| Tipo | A propriedade type do campo deve ser definida como: **AzureKeyVaultSecret**. | SIM |
| secretName | O nome do segredo no Azure Key Vault. | SIM |
| secretVersion | A versão do segredo no Azure Key Vault.<br/>Se não for especificada, a versão mais recente do segredo será sempre usada.<br/>Se especificada, a versão especificada será sempre usada.| Não  |
| store | Refere-se a um serviço vinculado do Azure Key Vault que você usa para armazenar a credencial. | SIM |

**Usando a criação da interface do usuário:**

Selecione **Azure Key Vault** para os campos secretos ao criar a conexão para seu armazenamento de dados/computação. Selecione o serviço vinculado do Azure Key Vault provisionados e forneça o **Nome secreto**. Opcionalmente, você pode fornecer uma versão do segredo também. 

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
