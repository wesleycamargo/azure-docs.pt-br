---
title: Referência de configuração do Azure Blockchain Workbench
description: Visão geral de configuração do aplicativo Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referência de configuração do Azure Blockchain Workbench

 Os aplicativos do Azure Blockchain Workbench são fluxos de trabalho de várias partes definidos por metadados de configuração e código de contrato inteligente. Metadados de configuração definem os fluxos de trabalho de alto nível e modelo de interação do aplicativo de blockchain. Contratos inteligentes definem a lógica de negócios do aplicativo blockchain. O Workbench usa a configuração e o código de contrato inteligente para gerar as experiências de usuário do aplicativo blockchain.

Os metadados de configuração especificam as seguintes informações para cada aplicativo blockchain: 

* Nome e descrição do aplicativo blockchain
* Funções exclusivas para usuários que podem atuar ou participar do aplicativo blockchain
* Um ou mais fluxos de trabalho. Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo da lógica de negócios. Os fluxos de trabalho podem ser independentes ou interagir entre si.

Cada fluxo de trabalho definido especifica o seguinte:

* O nome e a descrição do fluxo de trabalho
* Estados do fluxo de trabalho.  Cada estado é uma etapa no fluxo de controle da lógica de negócios. 
* Ações para fazer a transição para o próximo estado
* Funções de usuário permitidas para iniciar cada ação
* Contratos inteligentes que representam a lógica de negócios em arquivos de código

## <a name="application"></a>Aplicativo

Um aplicativo blockchain contém regras de configuração para metadados, fluxos de trabalho e usuário que podem funcionar ou participar dentro do aplicativo.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| ApplicationName | Nome de aplicativo exclusivo. O contrato inteligente correspondente deve usar o mesmo **ApplicationName** para a classe de contrato aplicável.  | sim |
| DisplayName | O nome de exibição amigável do aplicativo. | sim |
| DESCRIÇÃO | Descrição do aplicativo. | Não  |
| ApplicationRoles | Coleção de [ApplicationRoles](#application-roles). Funções de usuário que podem funcionar ou participar no aplicativo.  | sim |
| Fluxos de trabalho | Coleção de [Fluxos de trabalho](#workflows). Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo da lógica de negócios. | sim |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="workflows"></a>Fluxos de trabalho

Uma lógica de negócios de um aplicativo pode ser modelada como uma máquina de estado em que executar uma ação faz com que o fluxo da lógica de negócios mova de um estado para outro. Um fluxo de trabalho é uma coleção destes estados e ações. Cada fluxo de trabalho consiste em um ou mais contratos inteligentes que representam a lógica de negócios nos arquivos de código. Um contrato executável é uma instância de fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| NOME | Nome do fluxo de trabalho exclusivo. O contrato inteligente correspondente deve usar o mesmo **Nome** para a classe de contrato aplicável. | sim |
| DisplayName | Nome de exibição amigável do fluxo de trabalho. | sim |
| DESCRIÇÃO | Descrição do fluxo de trabalho. | Não  |
| Iniciadores | Coleção de [ApplicationRoles](#application-roles). Funções que são atribuídas a usuários que estão autorizados a criar contratos no fluxo de trabalho. | sim |
| StartState | Nome do estado inicial do fluxo de trabalho. | sim |
| propriedades | Coleção de [identificadores](#identifiers). Representa os dados que podem ser lidos visualizados em cadeia em uma ferramenta de experiência do usuário. | sim |
| Construtor | Define os parâmetros de entrada para criar uma instância de fluxo de trabalho. | sim |
| Funções | Uma coleção de [funções](#functions) que podem ser executadas no fluxo de trabalho. | sim |
| Estados | Uma coleção de [estados](#states) de fluxo de trabalho. | sim |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="type"></a>type

Tipos de dados com suporte.

| type | DESCRIÇÃO |
|-------|-------------|
| endereço  | Tipo de endereço de blockchain, como *contratos* ou *usuários* |
| bool     | Tipo de dados boolianos |
| contrato | Endereço do contrato de tipo |
| int      | Tipo de dados inteiros |
| money    | Tipo de dados Money |
| state    | Estado de fluxo de trabalho |
| string   | Tipos de dados de cadeia de caracteres |
| usuário     | Endereços do usuário de tipo |
| tempo real     | Tipos de dados de tempo |
|`[ Application Role Name ]`| Qualquer nome especificado na função de aplicativo. Limita os usuários desse tipo de função. |

## <a name="constructor"></a>Construtor

Define os parâmetros de entrada para uma instância de fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| parâmetros | Coleção de [identificadores](#identifiers) necessários para iniciar um contrato inteligente. | sim |

### <a name="constructor-example"></a>Exemplo de construtor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Funções

Define as funções que podem ser executadas no fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| NOME | O nome exclusivo da função. O contrato inteligente correspondente deve usar o mesmo **Nome** para a função aplicável. | sim |
| DisplayName | Nome de exibição amigável da função. | sim |
| DESCRIÇÃO | Descrição da função | Não  |
| parâmetros | Coleção de [identificadores](#identifiers) correspondente aos parâmetros da função. | sim |

### <a name="functions-example"></a>Exemplo de funções

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Estados

Uma coleção de estados exclusivos dentro de um fluxo de trabalho. Cada estado captura uma etapa no fluxo de controle da lógica de negócios. 

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| NOME | Nome único do estado. O contrato inteligente correspondente deve usar o mesmo **Nome** para o estado aplicável. | sim |
| DisplayName | Nome de exibição amigável do estado. | sim |
| DESCRIÇÃO | Descrição do estado. | Não  |
| PercentComplete | Um valor inteiro exibido na interface do usuário Blockchain Workbench para mostrar o progresso dentro do fluxo de controle de lógica de negócios. | sim |
| Estilo | Dica visual que indica se o estado representa um estado de êxito ou falha. Há dois valores válidos: `Success` ou `Failure`. | sim |
| Transições | Coleção de [transições](#transitions) disponíveis do estado atual para o próximo conjunto de estados. | Não  |

### <a name="states-example"></a>Exemplo de estados

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transições

Ações disponíveis para o próximo estado. Uma ou mais funções de usuário podem executar uma ação em cada estado, onde uma ação transicionar um estado para outro estado no fluxo de trabalho. 

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| AllowedRoles | Lista de funções de aplicativos permitidos para iniciar a transição. Todos os usuários da função especificada podem ser capazes de executar a ação. | Não  |
| AllowedInstanceRoles | Lista de funções de usuário participante ou especificado no contrato inteligente permitido para iniciar a transição. As funções de instância são definidas em **Propriedades** nos Fluxos de trabalho. Os usuários representam um usuário participante ou especificado no contrato inteligente como oposto a todos os usuários de um tipo de função. | Não  |
| DisplayName | Nome de exibição amigável da transição. | sim |
| DESCRIÇÃO | Descrição da transição. | Não  |
| Função | Nome da função para iniciar a transição. | sim |
| NextStates | Uma coleção de potenciais próximos estados após uma transição com êxito. | sim |

### <a name="transitions-example"></a>Exemplo de transições

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Funções de aplicativo

Funções de aplicativo definem um conjunto de funções que podem ser atribuídos a usuários que desejam agir ou participar do aplicativo. Funções de aplicativo podem ser usadas para restringir as ações e participação dentro do aplicativo blockchain e fluxos de trabalho correspondente. 

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| NOME | O nome exclusivo da função do aplicativo. O contrato inteligente correspondente deve usar o mesmo **Nome** para a função aplicável. Nomes de tipo de base são reservados. Você não pode nomear uma função de aplicativo com o mesmo nome como [Tipo](#type)| sim |
| DESCRIÇÃO | Descrição da função do aplicativo. | Não  |

### <a name="application-roles-example"></a>Exemplo de funções de aplicativo

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identificadores

Identificadores representam uma coleção de informações usadas para descrever as propriedades de fluxo de trabalho, construtor e parâmetros de função. 

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| NOME | O nome exclusivo da propriedade ou parâmetro. O contrato inteligente correspondente deve usar o mesmo **Nome** para a propriedade ou parâmetro aplicável. | sim |
| DisplayName | Nome para exibição amigável para a propriedade ou parâmetro. | sim |
| DESCRIÇÃO | Descrição da propriedade ou do parâmetro. | Não  |

### <a name="identifiers-example"></a>Exemplo de identificadores

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exemplo de arquivo de configuração

O exemplo a seguir define um aplicativo de solicitação-resposta básica em que um solicitante envia que uma solicitação e o respondente enviam uma resposta à solicitação.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar o Azure Blockchain Workbench](blockchain-workbench-deploy.md)

