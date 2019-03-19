---
title: Criptografar credenciais no Azure Data Factory | Microsoft Docs
description: Aprenda a criptografar e armazenar credenciais para seus armazenamentos de dados locais em um computador com tempo de execução de integração auto-hospedado.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 65518e7515f9e233b12ae5406819c91e8e3f2a77
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453160"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Criptografar credenciais para armazenamentos de dados locais no Azure Data Factory
Você pode criptografar e armazenar credenciais para seus armazenamentos de dados locais (serviços vinculados com informações confidenciais) em um computador com tempo de execução de integração auto-hospedado. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você passa um arquivo de definição de JSON com as credenciais para o <br/>[**Novo AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet para produzir um arquivo de definição de JSON de saída com as credenciais criptografadas. Em seguida, use a definição atualizada do JSON para criar os serviços vinculados.

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
Para criptografar os dados confidenciais do conteúdo JSON em um tempo de execução de integração auto-hospedado local, execute **New-AzDataFactoryV2LinkedServiceEncryptedCredential**e passe o conteúdo JSON. Esse cmdlet garante que as credenciais sejam criptografadas usando DPAPI e armazenadas no nó de tempo de execução de integração auto-hospedado localmente. O conteúdo de saída pode ser redirecionado para outro arquivo JSON (no caso, 'encryptedLinkedService.json'), que contém credenciais criptografadas.

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Use o JSON com credenciais criptografadas
Agora, use o arquivo JSON de saída do comando anterior que contém a credencial criptografada para configurar o **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre considerações de segurança para a movimentação de dados, consulte [considerações de segurança da movimentação de dados](data-movement-security-considerations.md).

