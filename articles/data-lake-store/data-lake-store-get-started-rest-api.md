---
title: 'API REST: operações de gerenciamento de conta no Azure Data Lake Store | Microsoft Docs'
description: Usar o Azure Data Lake Store e a API REST WebHDFS para executar operações de gerenciamento de conta no Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 5b31188eb5618d0ec5ac1f89c590913e4e284d9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337144"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Operações de gerenciamento de conta no Azure Data Lake Store usando API REST
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprenderá a executar operações de gerenciamento de conta no Data Lake Store usando a API REST. As operações de gerenciamento de conta incluem a criação de uma conta do Data Lake Store, exclusão de uma conta do Data Lake Store etc. Para obter instruções sobre como executar operações de filesystem no Data Lake Store usando API REST, consulte [Operações de filesystem no Data Lake Store usando API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>pré-requisitos
* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas à API REST em uma conta do Repositório Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como faço para me autenticar usando o Azure Active Directory?
Você pode usar duas abordagens para se autenticar usando o Azure Active Directory.

* Para saber sobre autenticação do usuário final em seu aplicativo (interativo), consulte [Autenticação do usuário final com Data Lake Store usando SDK do .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para saber sobre autenticação do usuário final em seu aplicativo (não interativo), consulte [Autenticação de serviço para serviço com Data Lake Store usando o SDK do .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake
Essa operação se baseia na chamada à API REST definida [aqui](https://docs.microsoft.com/en-us/rest/api/datalakestore/accounts/create).

Use o comando cURL a seguir. Substitua **\<nomedorepositório** pelo nome do Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização recuperado anteriormente. A carga de solicitação para esse comando está contida no arquivo **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do arquivo input.json lembra o seguinte trecho de código:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Excluir uma conta do Repositório do Data Lake
Essa operação se baseia na chamada à API REST definida [aqui](https://docs.microsoft.com/en-us/rest/api/datalakestore/accounts/delete).

Use o comando cURL a seguir para excluir uma conta do Repositório Data Lake. Substitua **\<nomedorepositório** pelo nome do Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Você deverá algo semelhante ao seguinte trecho:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Próximas etapas
* [Operações de filesystem no Data Lake Store usando API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência da API REST do Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplicativos de Big Data de software livre compatíveis com o Repositório Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

