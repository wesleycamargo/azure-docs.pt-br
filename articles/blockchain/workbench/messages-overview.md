---
title: Visão geral da integração de mensagens do Azure Blockchain Workbench
description: Visão geral do uso de mensagens no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614354"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integração de mensagens do Azure Blockchain Workbench

Além de fornecer uma API REST, o Azure Blockchain Workbench também fornece integração baseada em mensagens. O workbench publica eventos centrados em razão via Grade de Eventos do Azure, possibilitando que os clientes downstream ingiram dados ou tomem ação baseados nesses eventos. Para os clientes que exigem troca de mensagem confiável, o Azure Blockchain Workbench também entrega mensagens a um ponto de extremidade do Azure Service Bus.

## <a name="input-apis"></a>Entrada APIs

Se você quiser iniciar transações de sistemas externos para criar usuários, criar contratos e atualizar contratos, poderá usar APIs de entrada de mensagens para executar transações em um razão. Ver [exemplos de integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para obter um exemplo que demonstra as APIs de entrada.

A seguir estão as APIs de entrada disponíveis no momento.

### <a name="create-user"></a>Criar usuário

Cria um novo usuário.

A solicitação exige os seguintes campos:

| **Nome**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID do cliente fornecido                                |
| firstName            | Primeiro nome do usuário                              |
| lastName             | Sobrenome do usuário                               |
| emailAddress         | Endereço de e-mail do usuário                           |
| externalId           | Microsoft Azure Active Directory do usuário                      |
| ConnectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens                            |
| messageName          | **CreateUserRequest**                               |

Exemplo:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID do cliente fornecido |
| userId                | ID do usuário que foi criado |
| userChainIdentifier   | Endereço do usuário que foi criado na rede blockchain. No Ethereum, o endereço é o endereço  **on-chain**  do usuário. |
| ConnectionId          | Identificador exclusivo para a conexão blockchain|
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateUserUpdate** |
| status                | Status da solicitação de criação do usuário.  Se bem sucedido, o valor é **Sucesso**. Em caso de falha, o valor é **falha**.     |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo bem-sucedida **criar usuário** resposta do Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Criar contrato

Cria um novo contrato.

A solicitação exige os seguintes campos:

| **Nome**             | **Descrição**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID do cliente fornecido |
| userChainIdentifier  | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço **do usuário na cadeia**. |
| applicationName      | Nome do aplicativo |
| workflowName         | Nome do fluxo de trabalho |
| parâmetros           | Parâmetros de entrada para criação de contrato |
| ConnectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName          | **CreateContractRequest** |

Exemplo:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**                 | **Descrição**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID do cliente fornecido                                                             |
| contractId               | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Endereço do contrato no razão                                            |
| ConnectionId             | Identificador exclusivo para a conexão blockchain                               |
| messageSchemaVersion     | Versão do esquema de mensagens                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status da solicitação de criação do contrato.  Os valores possíveis: **Submitted**, **confirmado**, **falha**.  |
| additionalInformation    | Informações adicionais fornecidas com base no status                              |

Exemplo de um enviado **criar contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Exemplo de um compromisso **criar contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Criar ação de contrato

Cria uma nova ação de contrato.

A solicitação exige os seguintes campos:

| **Nome**                 | **Descrição**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID do cliente fornecido |
| userChainIdentifier      | Endereço do usuário que foi criado na rede blockchain. No Ethereum, este é o endereço do **na cadeia** do usuário. |
| contractLedgerIdentifier | Endereço do contrato no razão |
| WorkflowFunctionName     | Nome da função de fluxo de trabalho |
| parâmetros               | Parâmetros de entrada para criação de contrato |
| ConnectionId             | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion     | Versão do esquema de mensagens |
| messageName              | **CreateContractActionRequest** |

Exemplo:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID do cliente fornecido|
| contractId            | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| ConnectionId          | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateContractActionUpdate** |
| status                | Status da solicitação de ação do contrato. Os valores possíveis: **Submitted**, **confirmado**, **falha**.                         |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo de uma resposta de **ação de contrato criada** enviada pelo Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemplo de uma resposta de **ação de contrato** confirmada do Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha serão incluídos em informações adicionais.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Mensagens e códigos de erro da API entrados

**Código de erro 4000: erro de solicitação inválida**
- ID de conexão inválida
- Falha na desserialização CreateUserRequest
- Falha na desserialização CreateContractRequest
- Falha na desserialização CreateContractActionRequest
- O aplicativo {identificado pelo nome do aplicativo} não existe
- Aplicativo {identificado pelo nome do aplicativo} não tem fluxo de trabalho
- UserChainIdentifier não existe
- O contrato {identificado pelo identificador do razão} não existe
- Contrato {identificado pelo identificador do razão} não tem função {nome da função do fluxo de trabalho}
- UserChainIdentifier não existe

**Código de Erro 4090: Erro de Conflito**
- O usuário já existe
- Já existe um contrato
- Ação de contrato já existe

**O código de erro 5000: erro interno do servidor**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

As notificações de eventos podem ser usadas para notificar usuários e sistemas downstream de eventos que acontecem no Blockchain Workbench e na rede de blockchain a que está conectado. As notificações de evento pode ser consumidas diretamente no código ou usadas com ferramentas como Aplicativos Lógicos e seguir para acionar o fluxo de dados dos sistemas downstream.

Consulte [Referência de mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumindo eventos da Grade de Eventos com o Azure Functions

Se um usuário deseja usar a Grade de Eventos para ser notificado sobre eventos que ocorrem no Blockchain Workbench, é possível consumir eventos de grade de eventos usando o Azure Functions.

1. Criar um **Aplicativo de funções no Azure** no portal do Azure.
2. Criar uma nova função.
3. Localize o modelo para a Grade de Eventos. Código do modelo básico para ler a mensagem que é mostrada. Modifique o código conforme necessário.
4. Salve a função. 
5. Selecione a Grade de Eventos do grupo de recursos do Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumindo eventos da Grade de Eventos com os Aplicativos Lógicos

1.  Criar um novo **Aplicativo Lógico no Azure** no portal do Azure.
2.  Ao abrir o Aplicativo Lógico do Azure no portal, será solicitado a selecionar um gatilho. Selecione **Grade de Eventos do Azure – quando ocorrer um evento de recurso**.
3. Quando o designer de fluxo de trabalho for exibido, você será solicitado a entrar.
4. Selecione a Assinatura. Recursos como **Microsoft.EventGrid.Topics**. Selecione o **Nome do Recurso** do nome do recurso do grupo de recursos do Azure Blockchain Workbench.
5. Selecione a Grade de Eventos do grupo de recursos do Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Usando tópicos do Barramento de Serviço para notificações

Os Tópicos do Barramento de Serviço podem ser usados para notificar os usuários sobre eventos que ocorrem no Blockchain Workbench. 

1.  Navegue até o Barramento de Serviço no grupo de recursos do Workbench.
2.  Selecionar **Tópicos**.
3.  Selecione **workbench-externo**.
4.  Criar uma nova assinatura a esse tópico. Obter uma chave para isso.
5.  Crie um programa que assina eventos dessa assinatura.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumir Mensagens de Barramento de Serviço com Aplicativos Lógicos

1. Criar um novo **Aplicativo Lógico no Azure** no portal do Azure.
2. Ao abrir o Aplicativo Lógico do Azure no portal, será solicitado a selecionar um gatilho. Digite **Barramento de Serviço** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação para a qual você deseja ter com o Barramento de Serviço. Por exemplo, **Barramento de Serviço -- Quando uma mensagem é recebida em uma assinatura de tópico (auto-preenchimento)**.
3. Quando o designer de fluxo de trabalho é exibido, especifique as informações de conexão para o Barramento de Serviço.
4. Selecione sua assinatura e especifique o tópico de **workbench-externo**.
5. Desenvolva a lógica do seu aplicativo que utiliza a mensagem do gatilho.

## <a name="notification-message-reference"></a>Referência da mensagem de notificação

Dependendo do **OperationName**, as mensagens de notificação serão de um dos seguintes tipos.

### <a name="accountcreated"></a>AccountCreated

Indica que uma nova conta foi solicitada a ser adicionado à cadeia especificada.

| NOME    | DESCRIÇÃO  |
|----------|--------------|
| UserId  | ID do usuário que foi criado. |
| ChainIdentifier | Endereço do usuário que foi criado na rede blockchain. Em Ethereum, isso seria o endereço do usuário **em cadeia**. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Indica que foi feita uma solicitação para inserir ou atualizar um contrato em um razão distribuído.

| NOME | DESCRIÇÃO |
|-----|--------------|
| ChainID | Identificador exclusivo para a cadeia associada a solicitação |
| BlockId | Identificador exclusivo para um bloco no ledger |
| ContractId | Um identificador exclusivo para o contrato |
| ContractAddress |       O endereço do contrato no razão |
| TransactionHash  |     O resumo da mensagem de transação na razão |
| OriginatingAddress |   O endereço do originador da transação |
| ActionName       |     O nome da ação |
| IsUpdate        |      Identifica quando se trata de uma atualização |
| parâmetros       |     Uma lista de objetos que identificam o nome, valor e tipo de dados dos parâmetros enviados para uma ação |
| TopLevelInputParams |  Em cenários onde um contrato está conectado a um ou mais contratos, estes são os parâmetros do contrato de nível superior. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Indica que foi feita uma solicitação para executar uma ação em um contrato específico em um razão distribuído.

| NOME                     | DESCRIÇÃO                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Identificador exclusivo para esta ação de contrato |
| ChainIdentifier          | Identificador exclusivo para a cadeia |
| ConnectionId             | Identificador exclusivo para a conexão |
| UserChainIdentifier      | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço **do usuário na cadeia**. |
| ContractLedgerIdentifier | Endereço do contrato no razão |
| WorkflowFunctionName     | Nome da função de fluxo de trabalho |
| WorkflowName             | Nome do fluxo de trabalho |
| WorkflowBlobStorageURL   | A URL do contrato no armazenamento de blobs |
| ContractActionParameters | Parâmetros para a ação de contrato |
| TransactionHash          | O resumo da mensagem de transação na razão |
| Status de provisionamento      | O status atual de provisionamento da ação.</br>0 – Criou</br>1 – Em andamento</br>2 – Completo</br> Concluída indica uma confirmação do razão que nesse foi adicionado com êxito |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Indica que uma solicitação foi feita para atualizar o saldo de usuário em um razão distribuído específico.

> [!NOTE]
> Esta mensagem é gerada apenas para os razões que exigem o financiamento de contas.
> 

| NOME    | DESCRIÇÃO                              |
|---------|------------------------------------------|
| Endereço | O endereço do usuário que foi fundado |
| Saldo | O saldo do saldo de usuário         |
| ChainID | Identificador exclusivo para a cadeia     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

A mensagem indica que foi feita uma solicitação para adicionar um bloco em um razão distribuído.

| NOME           | DESCRIÇÃO                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Identificador exclusivo da cadeia à qual o bloco foi adicionado             |
| BlockId        | Identificador exclusivo para o bloco dentro do Azure Blockchain Workbench |
| BlockHash      | O resumo da mensagem do bloco                                                 |
| BlockTimeStamp | O carimbo de hora do bloco                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

A mensagem fornece detalhes sobre uma solicitação para adicionar uma transação em um razão distribuído.

| NOME            | DESCRIÇÃO                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Identificador exclusivo da cadeia à qual o bloco foi adicionado             |
| BlockId         | Identificador exclusivo para o bloco dentro do Azure Blockchain Workbench |
| TransactionHash | O resumo da mensagem da transação                                           |
| Da            | O endereço do originador da transação                      |
| Para              | O endereço do destinatário pretendido da transação              |
| Valor           | O valor incluído na transação                                 |
| IsAppBuilderTx  | Identifica se esta é uma transação do Blockchain Workbench                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Fornece detalhes sobre a atribuição de um identificador de cadeia de um contrato. Por exemplo, no Ethereum blockchain, o endereço de um contrato no razão.

| NOME            | DESCRIÇÃO                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| ChainIdentifier | Identificador do contrato da cadeia                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Classes usadas por tipos de mensagens

### <a name="messagemodelbase"></a>MessageModelBase

O modelo de base para todas as mensagens.

| NOME          | DESCRIÇÃO                          |
|---------------|--------------------------------------|
| OperationName | O nome da operação           |
| RequestId     | Identificador exclusivo para a solicitação |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Contém o nome, o valor e o tipo de um parâmetro.

| NOME  | DESCRIÇÃO                 |
|-------|-----------------------------|
| NOME  | O nome do parâmetro  |
| Valor | O valor do parâmetro |
| Tipo  | O tipo do parâmetro  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Contém a ID, o nome, o valor e o tipo de uma propriedade.

| NOME  | DESCRIÇÃO                |
|-------|----------------------------|
| ID    | O ID da propriedade    |
| NOME  | O nome da propriedade  |
| Valor | O valor da propriedade |
| Tipo  | O tipo da propriedade  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Padrões de integração e contratos inteligentes](integration-patterns.md)