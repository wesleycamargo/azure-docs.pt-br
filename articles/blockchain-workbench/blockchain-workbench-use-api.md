---
title: Usando a API REST do Azure Blockchain Workbench
description: Cenários para saber como usar a API REST do Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: cec2ab862a34a8753601dfeef3081ae9e9ca9fd9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Usando a API REST do Azure Blockchain Workbench 

A API REST do Azure Blockchain Workbench fornece aos desenvolvedores e profissionais de informações uma maneira de criar integrações avançados para aplicativos blockchain. Este documento orienta você através de vários métodos de chave da API REST do Workbench. Considere um cenário em que um desenvolvedor quer criar um cliente blockchain personalizado, que permite que usuários conectados exibam e interajam com seus aplicativos blockchain atribuídos. O cliente permite aos usuários exibir instâncias de contrato e executar ações em contratos inteligentes. O cliente usa a API REST do Workbench no contexto do usuário conectado para fazer o seguinte:

* Listar aplicativos
* Listar fluxos de trabalho para um aplicativo
* Listar instâncias de contrato inteligentes para um fluxo de trabalho
* Listar ações disponíveis para um contrato
* Executar uma ação para um contrato

## <a name="list-applications"></a>Listar aplicativos

Depois que um usuário tiver se conectado ao cliente blockchain, a primeira tarefa será recuperar todos os aplicativos blockchain do Blockchain Workbench para o usuário. Nesse cenário, o usuário tem acesso a dois aplicativos:

1.  Asset Transfer
2.  Refrigerated Transportation

Use a [API GET de aplicativos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

Resposta lista todos os aplicativos blockchain aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm todos os aplicativos blockchain, enquanto os administradores não Workbench obtêm todos os blockchains para os quais eles têm pelo menos uma função de aplicativo associada ou uma função de instância de contrato inteligente associada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Listar fluxos de trabalho para um aplicativo

Depois que um usuário seleciona o aplicativo blockchain aplicável, nesse caso, Asset Transfer, o cliente blockchain recupera todos os fluxos de trabalho do aplicativo blockchain específico. Os usuários podem, em seguida, selecionar o fluxo de trabalho aplicável antes da exibição de todas as instâncias de contrato inteligente para o fluxo de trabalho. Cada aplicativo blockchain tem um ou mais fluxos de trabalho, e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Ao criar aplicativos cliente blockchain, é recomendável ignorar o fluxo de experiência de usuário permitindo que os usuários selecionem o fluxo de trabalho apropriado quando há apenas um fluxo de trabalho para o aplicativo blockchain. Nesse caso, o Asset Transfer tem apenas um fluxo de trabalho, também chamado Asset Transfer.

Use a [API GET de Fluxos de Trabalho de Aplicativos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Resposta lista todos os fluxos de trabalho do aplicativo blockchain especificado aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm todos os fluxos de trabalho blockchain, enquanto os administradores não Workbench obtêm todos os fluxos de trabalho para os quais eles têm pelo menos uma função de aplicativo associada ou associada à função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligentes para um fluxo de trabalho

Depois que um usuário selecionar o fluxo de trabalho aplicável, neste caso, Asset Transfer, o cliente blockchain recuperará todas as instâncias de contrato inteligente para o fluxo de trabalho especificado. Você pode usar essas informações para mostrar todas as instâncias de contrato inteligentes para o fluxo de trabalho e permitir que os usuários se aprofundem em qualquer uma das instâncias de contrato inteligente mostradas. Neste exemplo, considere que um usuário gostaria de interagir com uma das instâncias de contrato inteligente para agir.

Use a [API GET de Contratos](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Resposta lista todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores do Workbench obtêm todas as instâncias de contrato inteligente, enquanto os administradores não Workbench obtêm todas as instâncias de contrato inteligente para as quais eles têm pelo menos uma função de aplicativo associada ou associada à função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Listar ações disponíveis para um contrato

Depois que um usuário tiver decidido se aprofundar em um dos contratos, o cliente blockchain poderá mostrar todas as ações disponíveis para o usuário, dependendo do estado do contrato. Neste exemplo, o usuário está procurando todas as ações disponíveis para um novo contrato inteligente criado:

* Modificar: permite que o usuário modifique a descrição e o preço de um ativo.
* Terminar: permite que o usuário encerre o contrato do ativo.

Use a [API GET de Ação de Contrato](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Resposta lista todas as ações que um usuário pode executar levando em consideração o estado atual da instância do contrato inteligente especificado. Os usuários obterão todas as ações aplicáveis se o usuário tiver uma função de aplicativo associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Executar uma ação para um contrato

Um usuário pode decidir executar uma ação para a instância de contrato inteligente especificada. Nesse caso, considere o cenário em que um usuário deseja modificar a descrição e o preço de um ativo para o seguinte:

* Descrição: "Meu atualizado carro"
* Preço: 54321

Use a [API POST de Ação de Contrato](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Os usuários podem executar a ação somente com base no estado atual da instância de contrato inteligente especificada e na função de aplicativo associada do usuário ou na função de instância de contrato inteligente. Se a publicação for bem-sucedida, uma resposta HTTP 200 OK é retornada sem corpo de resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)