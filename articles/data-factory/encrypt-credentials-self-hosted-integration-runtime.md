---
title: Criptografar credenciais no Azure Data Factory | Microsoft Docs
description: "Aprenda a criptografar e armazenar credenciais para seus armazenamentos de dados locais em um computador com tempo de execução de integração auto-hospedado."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: abnarain
ms.openlocfilehash: eb07deab834f63b48b7441f32521c48b27e549ca
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2017
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory
Você pode criptografar e armazenar credenciais para seus armazenamentos de dados locais (serviços vinculados com informações confidenciais) em um computador com tempo de execução de integração auto-hospedado. 

Você passa um arquivo de definição de JSON com as credenciais para o <br/>cmdlet [**New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) para produzir um arquivo de definição de JSON de saída com as credenciais criptografadas. Em seguida, use a definição atualizada do JSON para criar os serviços vinculados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="author-sql-server-linked-service"></a>Criar serviço vinculado do SQL Server
Crie um arquivo JSON denominado **SqlServerLinkedService.json** em qualquer pasta com o conteúdo a seguir:  

Substitua `<servername>`, `<databasename>`, `<username>` e `<password>` por valores de seu SQL Server antes de salvar o arquivo. E substitua `<integration runtime name>` pelo nome de seu tempo de execução de integração. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Criptografar credenciais
Para criptografar os dados confidenciais do conteúdo JSON em um tempo de execução de integração auto-hospedado local, execute **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** e passe o conteúdo JSON. Esse cmdlet garante que as credenciais sejam criptografadas usando DPAPI e armazenadas no nó de tempo de execução de integração auto-hospedado localmente. O conteúdo de saída pode ser redirecionado para outro arquivo JSON (no caso, 'encryptedLinkedService.json'), que contém credenciais criptografadas.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Use o JSON com credenciais criptografadas
Agora, use o arquivo JSON de saída do comando anterior que contém a credencial criptografada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre considerações de segurança para a movimentação de dados, consulte [considerações de segurança da movimentação de dados](data-movement-security-considerations.md).

