---
title: Visão geral das mensagens do Azure Blockchain Workbench
description: Visão geral do uso de mensagens no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4a2e85cc619d17745be9d8f72af5f99049ce7c6b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302085"
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Visão geral das mensagens do Azure Blockchain Workbench

Além de fornecer uma API REST, o Azure Blockchain Workbench também fornece integração baseada em mensagens. O workbench publica eventos centrados em razão via Grade de Eventos do Azure, possibilitando que os clientes downstream ingiram dados ou tomem ação baseados nesses eventos. Para os clientes que exigem troca de mensagem confiável, o Azure Blockchain Workbench também entrega mensagens a um ponto de extremidade do Azure Service Bus.

Os desenvolvedores também expressaram interesse na capacidade de fazer com que sistemas externos se comuniquem para iniciar transações para criar usuários, criar contratos e atualizar contratos em um razão. Embora essa funcionalidade não esteja exposta no momento em visualização pública, um exemplo que oferece esse recurso que pode ser encontrado em [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Notificações de eventos

Notificações de eventos podem ser usadas para notificar os usuários e sistemas downstream dos eventos que acontecem no Workbench e a rede de blockchain que está conectado. As notificações de evento pode ser consumidas diretamente no código ou usadas com ferramentas como Aplicativos Lógicos e seguir para acionar o fluxo de dados dos sistemas downstream.

Consulte [Referência de mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumindo Eventos de Grade com o Azure Functions

Se um usuário deseja usar a Grade de Eventos para ser notificado sobre eventos que ocorrem no Blockchain Workbench, é possível consumir eventos de grade de eventos usando o Azure Functions.

1. Criar um **Aplicativo de funções no Azure** no portal do Azure.
2. Criar uma nova função.
3. Localize o modelo para a Grade de Eventos. Código do modelo básico para ler a mensagem que é mostrada. Modifique o código conforme necessário.
4. Salve a função. 
5. Selecione a Grade de Eventos do grupo de recursos do Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumindo Eventos da Grade de Eventos do Azure com os Aplicativos Lógicos

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
2.  Ao abrir o Aplicativo Lógico do Azure no portal, será solicitado a selecionar um gatilho. Digite **Barramento de Serviço** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação para a qual você deseja ter com o Barramento de Serviço. Por exemplo, **Barramento de Serviço -- Quando uma mensagem é recebida em uma assinatura de tópico (auto-preenchimento)**.
3. Quando o designer de fluxo de trabalho é exibido, especifique as informações de conexão para o Barramento de Serviço.
4. Selecione sua assinatura e especifique o tópico de **workbench-externo**.
5. Desenvolva a lógica do seu aplicativo que utiliza a mensagem do gatilho.

## <a name="notification-message-reference"></a>Referência da mensagem de notificação

Dependendo do OperationName, as mensagens de notificação terão um dos seguintes tipos de mensagem.

### <a name="accountcreated"></a>AccountCreated

Indica que uma nova conta foi solicitada a ser adicionado à cadeia especificada.

| NOME    | DESCRIÇÃO  |
|----------|--------------|
| UserId  | ID do usuário que foi criado |
| ChainIdentifier | Endereço do usuário que foi criado na rede blockchain. Em Ethereum, isso seria o endereço do usuário "na cadeia". |

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
| ChainID | Um identificador exclusivo para a cadeia associada à solicitação.|
  BlockId | O identificador exclusivo para um bloco no razão.|
  ContractId | O identificador exclusivo para o contrato.|
  ContractAddress |       O endereço do contrato no razão.|
  TransactionHash  |     O hash da transação no razão.|
  OriginatingAddress |   O endereço do originador da transação.|
  ActionName       |     O nome da ação.|
  IsUpdate        |      Identifica se esta é uma atualização.|
  parâmetros       |     Uma lista de objetos que identifica o tipo de dados de nome e valor dos parâmetros enviados a uma ação.|
  TopLevelInputParams |  Em cenários onde um contrato está conectado a um ou mais contratos, estes são os parâmetros do contrato de nível superior. |

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
| ContractActionId         | O identificador exclusivo para esta ação do contrato                                                                                                                                |
| ChainIdentifier          | O identificador exclusivo da cadeia                                                                                                                                           |
| ConnectionId             | O identificador exclusivo da conexão                                                                                                                                      |
| UserChainIdentifier      | Endereço do usuário que foi criado na rede blockchain. Em Ethereum, isso seria o endereço do usuário "em cadeia".                                                     |
| ContractLedgerIdentifier | O endereço do contrato no razão.                                                                                                                                        |
| WorkflowFunctionName     | Nome da função do fluxo de trabalho.                                                                                                                                                |
| WorkflowName             | Nome do fluxo de trabalho.                                                                                                                                                         |
| WorkflowBlobStorageURL   | A url do contrato no armazenamento de blob.                                                                                                                                      |
| ContractActionParameters | Parâmetros para a ação de contrato.                                                                                                                                           |
| TransactionHash          | O hash da transação no razão.                                                                                                                                    |
| Status de provisionamento      | O status atual de provisionamento da ação.</br>0 – Criou</br>1 – Em andamento</br>2 – Completo</br> Completo indica uma confirmação do razão que foi adicionado com êxito.                                               |
|                          |                                                                                                                                                                               |

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
| Endereço | O endereço do usuário que foi consolidado. |
| Saldo | O saldo do saldo do usuário.         |
| ChainID | O identificador exclusivo da cadeia.     |


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
| ChainId        | O identificador exclusivo da cadeia para o qual o bloco foi adicionado.             |
| BlockId        | O identificador exclusivo para uma conexão dentro do Azure Blockchain Workbench. |
| BlockHash      | O hash do bloco.                                                 |
| BlockTimeStamp | Carimbo de data/hora do bloco.                                            |

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
| ChainId         | O identificador exclusivo da cadeia para o qual o bloco foi adicionado.             |
| BlockId         | O identificador exclusivo para uma conexão dentro do Azure Blockchain Workbench. |
| TransactionHash | Hash da transação.                                           |
| Da            | O endereço do originador da transação.                      |
| Para              | O endereço do destinatário pretendido da transação.              |
| Valor           | O valor incluído na transação.                                 |
| IsAppBuilderTx  | Identifica se esta é uma transação Blockchain Workbench.                         |

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
| ContractId      | É o identificador exclusivo para o contrato dentro do Azure Blockchain Workbench. |
| ChainIdentifier | Este é o identificador para o contrato na cadeia.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Padrões de integração e contratos inteligentes](blockchain-workbench-integration-patterns.md)