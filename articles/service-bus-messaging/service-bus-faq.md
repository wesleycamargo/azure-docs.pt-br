---
title: "Perguntas frequentes (FAQ) sobre o Barramento de Serviço | Microsoft Docs"
description: "Responde a algumas perguntas frequentes sobre o Barramento de Serviço do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: da7f8e3e61705cf07ff65c9dd1d8f292f4fb9f62
ms.openlocfilehash: 9061829e42ed5563d64860774aa7d80f2ab011bd


---
# <a name="service-bus-faq"></a>Perguntas frequentes sobre o Barramento de Serviço
Este artigo responde a algumas perguntas frequentes sobre o Barramento de Serviço do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure.

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Barramento de Serviço do Azure
### <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?
[O Barramento de Serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite enviar dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do Barramento de Serviço?
Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um hardware é necessária para a utilização do Barramento de Serviço e será uma das primeiras etapas da introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Barramento de Serviço do Azure?
A [Fila do Barramento de Serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são particularmente úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar umas com as outras. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) são recebidos e então enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço do Azure?
Um tópico pode ser visualizado como uma fila e ao usar várias assinaturas, ele se torna um modelo mais abrangente de mensagens; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) habilita um aplicativo que envia uma mensagem para um tópico com várias assinaturas para fazer com que essa mensagem seja recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?
Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. Uma [fila ou um tópico particionado](service-bus-partitioning.md) é manipulada por vários agentes de mensagens e armazenada em vários repositórios de mensagens. Isso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível.

Observe que a ordenação não é garantida ao usar o particionamento de entidades. Se uma partição não estiver disponível, você poderá enviar e receber mensagens de outras partições.

## <a name="best-practices"></a>Práticas recomendadas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do Barramento de Serviço do Azure?
* [Práticas recomendadas para melhorias de desempenho usando o Barramento de Serviço][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho na troca de mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As propriedades de uma fila e tópico a seguir são imutáveis. Leve isso em conta ao provisionar suas entidades, já que isso não pode ser modificado sem criar uma nova entidade de substituição.

* Tamanho
* Particionamento
* Sessões
* Detecção de duplicidade
* Entidade expressa

## <a name="pricing"></a>Preços
Esta seção responde a perguntas frequentes sobre a estrutura de preços do Barramento de Serviço. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços do Microsoft Azure. Para saber mais sobre o preço do Barramento de Serviço, consulte [Detalhes de preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Como é cobrado o Barramento de Serviço?
Para saber mais sobre o preço do Barramento de Serviço, veja [Detalhes de preço do Barramento de Serviço][Pricing overview]. Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que é não está?
Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente, bem como qualquer transferência de dados recebida. A transferência de dados fora de uma região está sujeita a encargos de saída que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>O Barramento de Serviço cobra pelo armazenamento?
Não, o Barramento de Serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

## <a name="quotas"></a>Cotas
Para obter uma lista de cotas e limites do Barramento de Serviço, veja [Visão geral sobre cotas][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>O Barramento de Serviço tem cotas de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas de um cliente. Como compreendemos que talvez seja necessário usar mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos entender as suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, as cotas de uso agregado são de 5 bilhões de mensagens por mês.

Embora reservemos o direito de desabilitar uma conta de cliente que tenha excedido suas cotas de uso em determinado mês, forneceremos uma notificação por email e faremos várias tentativas de contatar o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelas cobranças que excederem as cotas.

Como ocorre com outros serviços no Azure, o Barramento de Serviço aplica um conjunto de cotas específicas para garantir que haja um uso inteligente dos recursos. A seguir estão as cotas de uso impostas pelo serviço:

#### <a name="queuetopic-size"></a>Tamanho do tópico/fila
Especifique o tamanho máximo da fila ou do tópico no momento da sua criação. Essa cota pode ter um valor de 1, 2, 3, 4 ou 5 GB. Se tamanho máximo for atingido, as mensagens de entrada adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada.

#### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um nome de namespace do Barramento de Serviço só pode ter entre 6 e 50 caracteres. O limite da contagem de caracteres para cada fila, tópico ou assinatura está entre 1 e 50 caracteres.

#### <a name="number-of-concurrent-connections"></a>Número de conexões simultâneas
Fila/Tópico/Assinatura – o número de conexões TCP simultâneas em uma fila/tópico/assinatura é limitado a 100. Se essa cota for atingida, as solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. Para cada fábrica de mensagens, o Barramento de Serviço mantém uma conexão TCP se algum dos clientes criados por essa fábrica de mensagens tiver uma operação ativa pendente ou tiver concluído uma operação há menos de 60 segundos. Operações REST não são consideradas conexões TCP simultâneas.

#### <a name="number-of-topicsqueues-per-service-namespace"></a>Número de tópicos/filas por namespace de serviço
O número máximo de tópicos/filas (entidades de armazenamento duráveis) em um namespace de serviço é limitado a 10.000. Se essa cota for atingida, as solicitações subsequentes para a criação de uma novo tópico/fila no namespace de serviço serão rejeitadas. Nesse caso, o portal clássico do Azure exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

### <a name="message-size-quotas"></a>Cotas de tamanho de mensagem
#### <a name="queuetopicsubscription"></a>Fila/Tópico/Assinatura
**Tamanho da mensagem** : cada mensagem está limitada a um tamanho total de 256KB, incluindo os cabeçalhos da mensagem.

**Tamanho do cabeçalho da mensagem** – cada cabeçalho de mensagem é limitado a 64KB.

As mensagens que excederem essas cotas de tamanho serão rejeitadas e uma exceção será recebida pelo código de chamada.

**Número de assinaturas por tópico** – o número máximo de assinaturas por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes para a criação de assinaturas adicionais para o tópico serão rejeitadas. Nesse caso, o portal clássico do Azure exibirá uma mensagem de erro ou o código de cliente de chamada receberá uma exceção, dependendo se a tentativa foi feita por meio do portal ou no código do cliente.

**Número de filtros SQL por tópico** – o número máximo de filtros SQL por tópico é limitado a 2 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

**Filtros de número de correlação por tópico** – o número máximo de filtros de correlação por tópico é limitado a 100 mil. Se essa cota for atingida, as solicitações subsequentes de criação de filtros adicionais para o tópico serão rejeitadas e uma exceção será recebida pelo código de chamada.

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Barramento de Serviço do Azure e suas ações sugeridas?
Para obter uma lista de exceções possíveis do Barramento de Serviço, confira [Visão geral das exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?
As Assinaturas de Acesso Compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para saber mais sobre como gerar suas próprias assinaturas no Node, PHP, Java e C\#, veja o artigo [Assinaturas de Acesso Compartilhado][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?
Usando o Portal do Azure, você pode migrar namespaces do Barramento de Serviço para outra assinatura seguindo as instruções [aqui](../azure-resource-manager/resource-group-move-resources.md#use-portal). Se preferir usar o PowerShell, siga as instruções abaixo: 

A sequência de comandos a seguir move um namespace de uma assinatura do Azure para outra. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um administrador em assinaturas de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Barramento de Serviço, veja os tópicos a seguir.

* [Introdução ao Barramento de Serviço Premium do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Barramento de Serviço Premium do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Barramento de Serviço](service-bus-messaging-overview.md)
* [Visão geral da arquitetura de Barramento de Serviço do Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas-overview.md



<!--HONumber=Feb17_HO2-->


