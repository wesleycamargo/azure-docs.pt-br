---
title: Publish-WebApplicationWebSite (script do Windows PowerShell) | Microsoft Docs
description: Saiba como publicar um projeto Web em um site do Azure. Se os recursos necessários não existirem, este script criará tais recursos em sua assinatura do Azure.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: aaa1f679b0368b0ca93305fe867a63f3971a788c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publish-WebApplicationWebSite (script do Windows PowerShell)
## <a name="syntax"></a>Sintaxe
Publica um projeto Web em um site do Azure. Se os recursos necessários não existirem, o script criará tais recursos em sua assinatura do Azure.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuração
O caminho para o arquivo de configuração de JSON que descreve os detalhes da implantação.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |verdadeiro |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="subscriptionname"></a>SubscriptionName
O nome da assinatura do Azure na qual você deseja criar o site.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="webdeploypackage"></a>WebDeployPackage
O caminho para o pacote de implantação Web a publicar no site. Você pode criar este pacote usando o assistente Publicar Web no Visual Studio. Para obter mais informações, consulte [Introdução aos serviços de nuvem do Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
O nome do administrador e a senha do Banco de Dados SQL no Azure.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Se seu valor for true, imprimir mensagens do script para o fluxo de saída.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |falso |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="remarks"></a>Comentários
Para obter uma explicação completa de como usar o script para criar ambientes de desenvolvimento e teste, consulte [Usando scripts do Windows PowerShell para publicar para ambientes de desenvolvimento e teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O arquivo de configuração JSON especifica os detalhes daquilo que está para ser implantado. Ele inclui as informações que você especificou quando criou o projeto, como o nome e também o nome de usuário para o site. Ele também inclui o banco de dados a provisionar, se houver. O código a seguir mostra um exemplo de arquivo de configuração JSON:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Você pode editar o arquivo de configuração JSON para alterar o que é implantado. Uma seção de site é obrigatória, mas a seção de banco de dados é opcional.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte [WebApplicationVM de publicação (script do Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

