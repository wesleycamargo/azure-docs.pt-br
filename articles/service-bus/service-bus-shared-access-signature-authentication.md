---
title: Autenticação de Assinatura de Acesso Compartilhado com o Barramento de Serviço | Microsoft Docs
description: Detalhes sobre a autenticação SAS com o Barramento de Serviço.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2016
ms.author: sethm

---
# Autenticação de assinatura de acesso compartilhado com Barramento de serviço
A autenticação [Assinatura de Acesso Compartilhado (SAS)](service-bus-sas-overview.md) permite que os aplicativos se autentiquem no Barramento de Serviço usando uma chave de acesso configurada no namespace ou na entidade do sistema de mensagens (fila ou tópico) à qual estão associados direitos específicos. Você poderá então usar essa chave para gerar um token SAS que os clientes poderão usar para se autenticarem no Barramento de Serviço.

O suporte à autenticação SAS está incluído no SDK do Azure versão 2.0 e posteriores. Para saber mais sobre a autenticação do Barramento de Serviço, confira [Autenticação e autorização do Barramento de Serviço](service-bus-authentication-and-authorization.md).

## Conceitos
A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes solicitam acesso aos recursos do Barramento de Serviço ao apresentarem um token SAS. Esse token consiste no URI do recurso sendo acessado e uma expiração assinada com a chave configurada.

Você pode configurar as regras de autorização da Assinatura de Acesso Compartilhado em [retransmissões](service-bus-fundamentals-hybrid-solutions.md#relays), [filas](service-bus-fundamentals-hybrid-solutions.md#queues), [tópicos](service-bus-fundamentals-hybrid-solutions.md#topics) e [Hubs de Eventos](service-bus-fundamentals-hybrid-solutions.md#event-hubs) do Barramento de Serviço.

A autenticação SAS utiliza os seguintes elementos:

* [Regra de autorização de Acesso Compartilhado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): chave criptográfica primária de 256 bits na representação Base64, uma chave secundária opcional e um nome de chave e os direitos associados (uma coleção de direitos *Escutar*, *Enviar* ou *Gerenciar*).
* Token de [Assinatura de Acesso Compartilhado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx): gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso, que consiste no URI do recurso acessado e em uma expiração, com a chave de criptografia. A assinatura e outros elementos descritos nas seções a seguir são formatados em uma cadeia de caracteres para formar o token [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx).

## Configuração da autenticação de Assinatura de Acesso Compartilhado
Você pode configurar a regra [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) em namespaces, filas ou tópicos do Barramento de Serviço. A configuração de um [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) em uma assinatura do Barramento de Serviço não tem suporte no momento, mas você pode usar regras configuradas em um namespace ou em um tópico para proteger o acesso a assinaturas. Para obter um exemplo funcional que ilustre este procedimento, confira o exemplo [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com assinaturas do Barramento de Serviço](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

Você pode configurar até 12 dessas regras em um namespace, fila ou tópico do Barramento de Serviço. As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as entidades nesse namespace.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

Nesta figura, as regras de autorização *manageRuleNS*, *sendRuleNS* e *listenRuleNS* se aplicam à fila Q1 e ao tópico T1, enquanto *listenRuleQ* e *sendRuleQ* se aplicam somente à fila Q1 e *sendRuleT* se aplica somente ao tópico T1.

Os parâmetros de chave de uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) são os seguintes:

| Parâmetro | Descrição |
| --- | --- |
| *KeyName* |Uma cadeia de caracteres que descreve a regra de autorização. |
| *PrimaryKey* |Uma chave primária de 256 bits codificada em base64 para assinatura e validação do token SAS. |
| *SecondaryKey* |Uma chave secundária de 256 bits codificada em base64 para assinatura e validação do token SAS. |
| *AccessRights* |Uma lista de direitos de acesso concedidos pela regra de autorização. Esses direitos podem ser qualquer coleção de direitos Escutar, Enviar e Gerenciar. |

Quando um namespace do Barramento de Serviço é provisionado, uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) com [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) definido como **RootManageSharedAccessKey** é criada por padrão.

## Regenerar e revogar as chaves para as regras de Autorização de Acesso Compartilhado
É recomendável que você regenere periodicamente as chaves usadas no objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Geralmente, os aplicativos devem usar a [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) para gerar um token SAS. Ao regenerar as chaves, você deverá substituir a [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) pela chave primária antiga e gerar uma nova chave como a nova chave primária. Isso permite que você continue usando tokens para autorização que tenham sido emitidos com a antiga chave primária e que ainda não tenham expirado.

Se uma chave estiver comprometida e se for necessário revogar as chaves, será possível regenerar a [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) e a [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) de uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), substituindo-as por novas chaves. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## Gerando um token de Assinatura de Acesso Compartilhado
Qualquer cliente que tenha acesso às chaves de assinatura especificadas na regra de autorização de acesso compartilhado poderá gerar o token SAS. Ele é formatado da seguinte maneira:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

A **assinatura** do token SAS é calculada usando o hash HMAC-SHA256 de uma cadeia de caracteres a assinar com a propriedade [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) de uma regra de autorização. A cadeia de caracteres a assinar consiste em um URI de recurso e uma expiração, formatada da seguinte maneira:

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Observe que você deve usar o URI do recurso codificado para esta operação. O URI do recurso é o URI completo do recurso do Barramento de Serviço ao qual o acesso é solicitado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`; ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

A expiração é representada como o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970.

A regra de autorização de acesso compartilhado usada para assinar deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos sob o `<resourceURI>` usado na cadeia de caracteres a assinar.

O [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) no token SAS refere-se ao **keyName** da regra de autorização de acesso compartilhado usada para gerar o token.

O *URL-encoded-resourceURI* deve ser igual ao URI usado na cadeia de caracteres a assinar durante o cálculo da assinatura. Ele deve ser [codificado por percentual](https://msdn.microsoft.com/library/4fkewx0t.aspx).

## Como usar a autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço
Os cenários a seguir incluem a configuração de regras de autorização, a geração de tokens SAS e a autorização de cliente.

Para um exemplo funcional completo de um aplicativo do Barramento de Serviço que ilustre a configuração e use a autorização SAS, confira [Autenticação de Assinatura de Acesso Compartilhado com o Barramento de Serviço](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Há um exemplo relacionado que ilustra o uso de regras de autorização SAS configuradas em namespaces ou em tópicos para proteger assinaturas do Barramento de Serviço disponível aqui: [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com as assinaturas do Barramento de Serviço](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## Acessar regras de autorização de Acesso Compartilhado em um namespace
As operações na raiz do namespace do Barramento de Serviço exigem a autenticação de certificado. Você deve carregar um certificado de gerenciamento da sua assinatura do Azure. Para carregar um certificado de gerenciamento, clique em **Configurações** no painel esquerdo do [portal clássico do Azure][portal clássico do Azure]. Para saber mais sobre certificados de gerenciamento do Azure, confira [Visão geral sobre certificados do Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

O ponto de extremidade para acessar regras de autorização de acesso compartilhado em um namespace do Barramento de Serviço é o seguinte:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Para criar um objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) em um namespace do Barramento de Serviço, execute uma operação POST nesse ponto de extremidade com as informações da regra serializadas como JSON ou XML. Por exemplo:

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Da mesma forma, use uma operação GET no ponto de extremidade para ler as regras de autorização configuradas no namespace.

Para atualizar ou excluir uma regra de autorização específica, use o ponto de extremidade a seguir:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## Acessando regras de Autorização de Acesso Compartilhado em uma entidade
Você pode acessar um objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) configurado em uma fila ou tópico do Barramento de Serviço por meio da coleção [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) nos objetos [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) ou [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) correspondentes.

O código a seguir mostra como adicionar regras de autorização para uma fila.

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## Usando a autorização de Assinatura de Acesso Compartilhado
Aplicativos usando o SDK do .NET do Azure com as bibliotecas .NET do Barramento de Serviço podem usar a autorização SAS por meio da classe [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx). O código a seguir ilustra o uso do provedor do token para enviar mensagens para uma fila do Barramento de Serviço.

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Os aplicativos também podem usar SAS para autenticação usando uma cadeia de conexão SAS em métodos que aceitem cadeias de conexão.

Observe que, para usar a autorização SAS com as retransmissões do Barramento de Serviço, você poderá utilizar as chaves SAS configuradas no namespace do Barramento de Serviço. Se você criar explicitamente uma retransmissão no namespace ([NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) com um objeto [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx)), poderá definir as regras SAS apenas para essa retransmissão. Para usar a autorização SAS com assinaturas do Barramento de Serviço, você poderá usar as chaves SAS configuradas em um namespace ou em um tópico do Barramento de Serviço.

## Direitos necessários para operações do Barramento de Serviço
A tabela a seguir mostra os direitos de acesso necessários para diversas operações em recursos do Barramento de Serviço.

| Operação | Declaração Obrigatória | Escopo da Declaração |
| --- | --- | --- |
| **Namespace** | | |
| Configurar regra de autorização em um namespace |Gerenciar |Qualquer endereço de namespace |
| **Registro do Serviço** | | |
| Enumerar Políticas de Privacidade |Gerenciar |Qualquer endereço de namespace |
| Retransmissão | | |
| Iniciar a escuta em um namespace |Escutar |Qualquer endereço de namespace |
| Enviar mensagens a um ouvinte em um namespace |Enviar |Qualquer endereço de namespace |
| **Fila** | | |
| Criar uma fila |Gerenciar |Qualquer endereço de namespace |
| Excluir uma fila |Gerenciar |Qualquer endereço de fila válido |
| Enumerar filas |Gerenciar |/$Resources/Queues |
| Obter a descrição da fila |Gerenciar ou Enviar |Qualquer endereço de fila válido |
| Configurar regra de autorização para uma fila |Gerenciar |Qualquer endereço de fila válido |
| Enviar para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Escutar |Qualquer endereço de fila válido |
| Abandonar ou concluir as mensagens após o recebimento da mensagem em modo de bloqueio de pico |Escutar |Qualquer endereço de fila válido |
| Adiar uma mensagem para recuperação posterior |Escutar |Qualquer endereço de fila válido |
| Colocar uma mensagem nas mensagens mortas |Escutar |Qualquer endereço de fila válido |
| Obter o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Definir o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| **Tópico** | | |
| Criar um tópico |Gerenciar |Qualquer endereço de namespace |
| Excluir um tópico |Gerenciar |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerenciar |/$Resources/Topics |
| Obter a descrição do tópico |Gerenciar ou Enviar |Qualquer endereço de tópico válido |
| Configurar regra de autorização para um tópico |Gerenciar |Qualquer endereço de tópico válido |
| Enviar ao tópico |Enviar |Qualquer endereço de tópico válido |
| **Assinatura** | | |
| Criar uma assinatura |Gerenciar |Qualquer endereço de namespace |
| Excluir assinatura |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Enumerar assinaturas |Gerenciar |../myTopic/Subscriptions |
| Obter descrição da assinatura |Gerenciar ou Escutar |../myTopic/Subscriptions/mySubscription |
| Abandonar ou concluir as mensagens após o recebimento da mensagem em modo de bloqueio de pico |Escutar |../myTopic/Subscriptions/mySubscription |
| Adiar uma mensagem para recuperação posterior |Escutar |../myTopic/Subscriptions/mySubscription |
| Colocar uma mensagem nas mensagens mortas |Escutar |../myTopic/Subscriptions/mySubscription |
| Obter o estado associado a uma sessão de tópico |Escutar |../myTopic/Subscriptions/mySubscription |
| Definir o estado associado a uma sessão de tópico |Escutar |../myTopic/Subscriptions/mySubscription |
| **Regra** | | |
| Criar uma regra |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Excluir uma regra |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Enumerar regras |Gerenciar ou Escutar |../myTopic/Subscriptions/mySubscription/Rules |
| **Hubs de Notificação** | | |
| Criar um hub de notificação |Gerenciar |Qualquer endereço de namespace |
| Criar ou atualizar o registro para um dispositivo ativo |Escutar ou Gerenciar |../notificationHub/tags/{tag}/registrations |
| Atualizar informações do PNS |Escutar ou Gerenciar |../notificationHub/tags/{tag}/registrations/updatepnshandle |
| Enviar para um hub de notificação |Enviar |../notificationHub/messages |

## Próximas etapas
Para obter uma visão geral de alto nível do SAS no Barramento de Serviço, confira [Assinaturas de Acesso Compartilhado](service-bus-sas-overview.md).

Confira [Autenticação e autorização do Barramento de Serviço](service-bus-authentication-and-authorization.md) para obter mais informações sobre a autenticação do Barramento de Serviço.

[portal clássico do Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0622_2016-->