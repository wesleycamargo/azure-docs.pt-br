<properties 
    pageTitle="Encaminhamento automático de entidades de mensagens do Barramento de Serviço | Microsoft Azure"
    description="Como encadear uma fila ou assinatura em outra fila ou outro tópico."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Encadeando entidades do Barramento de Serviço com o encaminhamento automático

O recurso *encaminhamento automático* permite encadear uma fila ou assinatura em outra fila ou outro tópico que faça parte do mesmo namespace. Quando o encaminhamento automático está habilitado, o Barramento de Serviço remove automaticamente as mensagens colocadas na primeira fila ou assinatura (origem) e as coloca na segunda fila ou tópico (destino). Observe que ainda é possível enviar uma mensagem diretamente para a entidade de destino. Além disso, não é possível encadear uma subfila, por exemplo, uma fila de mensagens mortas, em outra fila ou outro tópico.

## Usando o encaminhamento automático

Você pode habilitar o encaminhamento automático configurando as propriedades [QueueDescription.ForwardTo][] ou [SubscriptionDescription.ForwardTo][] nos objetos [QueueDescription][] ou [SubscriptionDescription][] para a origem, como no exemplo a seguir.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A entidade de destino deverá existir no momento da criação da entidade de origem. Se a entidade de destino não existir, o Barramento de Serviço retornará uma exceção quando receber uma solicitação para criar a entidade de origem.

Você pode usar o encaminhamento automático para escalar horizontalmente um tópico individual. O Barramento de Serviço limita o [número de assinaturas em determinado tópico](service-bus-quotas.md) para 2.000. Você pode acomodar outras assinaturas criando tópicos de segundo nível. Perceba que mesmo se você não estiver ligado à limitação do Barramento de Serviço no número de assinaturas, adicionar um segundo nível de tópicos poderá melhorar a taxa de transferência geral de seu tópico.

![Cenário de encaminhamento automático][0]

Você também pode usar o encaminhamento automático para separar os remetentes dos destinatários. Por exemplo, considere um sistema ERP composto por três módulos: Processamento de Pedidos, Gerenciamento de Estoque e Gerenciamento de Relacionamentos com o Cliente. Cada um desses módulos gera mensagens que são enfileiradas em um tópico correspondente. Brenda e Pedro são representantes de vendas interessados em todas as mensagens relacionadas aos seus clientes. Para receber essas mensagens, Brenda e Pedro criaram, cada um, uma fila particular e uma assinatura em cada um dos tópicos ERP que encaminham automaticamente todas as mensagens para suas filas.

![Cenário de encaminhamento automático][1]

Se Brenda entrar de férias, sua fila pessoal, em vez do tópico ERP, ficará cheia. Nesse cenário, como um representante de vendas não recebeu qualquer mensagem, nenhum dos tópicos ERP atingirá a cota.

## Considerações sobre o encaminhamento automático

Se a entidade de destino tiver acumulado várias mensagens e exceder a cota, ou se a entidade de destino estiver desabilitada, a entidade de origem adicionará as mensagens à sua [fila de mensagens mortas](service-bus-dead-letter-queues.md) até que haja espaço no destino (ou a entidade seja habilitada novamente). Essas mensagens continuarão ativas na fila de mensagens mortas, portanto, você deve receber e processá-las explicitamente a partir da fila de mensagens mortas.

Ao encadear tópicos individuais a fim de obter um tópico composto com diversas assinaturas, recomendamos uma quantidade moderada de assinaturas no tópico de primeiro nível e diversas assinaturas nos tópicos de segundo nível. Por exemplo, um tópico de primeiro nível com 20 assinaturas, cada uma delas encadeada com um tópico de segundo nível com 200 assinaturas, permite uma taxa de transferência superior do que um tópico de primeiro nível com 200 assinaturas, cada uma delas encadeada com um tópico de segundo nível com 20 assinaturas.

O Barramento de Serviço cobra uma operação por cada mensagem encaminhada. Por exemplo, o envio de uma mensagem a um tópico com 20 assinaturas, cada uma delas configurada para encaminhar automaticamente mensagens para outra fila ou tópico, receberá uma cobrança por 21 operações, caso todas as assinaturas de primeiro nível recebam uma cópia da mensagem.

Para criar uma assinatura encadeada a outra fila ou tópico, o criador da assinatura deve ter permissões de **gerenciamento** tanto na entidade de origem quanto na entidade de destino. O envio de mensagens para o tópico de origem só exige permissões de **envio** no tópico de origem.

## Próximas etapas

Para obter informações detalhadas sobre o encaminhamento automático, veja os seguintes tópicos de referência:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Para saber mais sobre as melhorias de desempenho do Barramento de Serviço, veja [Entidades de mensagens particionadas][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Entidades de mensagens particionadas]: service-bus-partitioning.md

<!---HONumber=AcomDC_0518_2016-->