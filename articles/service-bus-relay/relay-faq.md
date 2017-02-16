---
title: Perguntas frequentes (FAQ) sobre o Azure Relay | Microsoft Docs
description: "Responde a algumas perguntas frequentes sobre Retransmissão do Azure."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 22781ec965decbfd33923478794a76692151c363


---
# <a name="relay-faq"></a>Perguntas frequentes sobre Retransmissão
Este artigo responde a algumas perguntas frequentes sobre Retransmissão do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure.

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é Retransmissão do Azure?
O [serviço de Retransmissão](relay-what-is-it.md) do Azure facilita seus aplicativos híbridos habilitando a exposição segura de serviços que residem em uma rede empresarial corporativa para a nuvem pública sem precisar abrir uma conexão de firewall nem exigir mudanças intrusivas em uma infraestrutura de rede corporativa.

### <a name="what-is-a-relay-namespace"></a>O que é um namespace de Retransmissão?
Um [namespace](relay-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos de Retransmissão dentro de seu aplicativo. Criar um é necessário para usar a Retransmissão e será uma das primeiras etapas da introdução.

### <a name="what-happened-to-the-previously-named-relay-service"></a>O que aconteceu com o serviço nomeado anteriormente como Retransmissão?
O serviço denominado anteriormente como **Retransmissão** agora se chama *Retransmissão do WCF*. Você pode continuar a usar esse serviço como de costume. As Conexões Híbridas são uma versão atualizada de um serviço transplantado do BizTalk. A Retransmissão do WCF e Conexões Híbridas continuarão a ter suporte no futuro.

## <a name="pricing"></a>Preços
Esta seção responde a algumas perguntas frequentes sobre a estrutura de preços de Retransmissão. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços do Microsoft Azure. Para saber mais sobre o preço da Retransmissão, consulte [Detalhes de preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como são cobradas as Conexões Híbridas e a Retransmissão do WCF?
Para saber mais sobre o preço da Retransmissão, consulte [Detalhes de preço do Barramento de Serviço][Pricing overview]. Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como sou cobrado por Conexões Híbridas?
Aqui estão três cenários de exemplo:

1. Se você tiver um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas, instalado e em execução contínua no mês inteiro e enviar 3 GB de dados pela conexão no decorrer do mês, seu encargo total será de US$ 5.
2. Se você tiver um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas, instalado e em execução contínua no mês inteiro e enviar 10 GB de dados pela conexão no decorrer do mês, seu encargo total será de US$ 7,50: US$ 5 pela conexão e os primeiros 5 GB + US$ 2,50 pelos 5 GB de dados adicionais.
3. Se você tiver duas instâncias, A e B, do Gerenciador de Conexões Híbridas instaladas e em execução contínua no mês inteiro e enviar 3 GB de dados pela conexão A e 6 GB pela conexão B, seu encargo total será de US$ 10,50: US$ 5 pela conexão A + US$ 5 pela conexão B + US$ 0,50 (pelo sexto GB na conexão B).

Observe que os preços usados nos exemplos são aplicáveis somente durante o período de versão prévia e estão sujeitos à alteração quando as Conexões Híbridas entrarem em disponibilidade geral.

### <a name="how-are-wcf-relay-hours-calculated"></a>Como as horas de Retransmissão do WCF são calculadas?
As horas de retransmissão são cobradas de acordo com a quantidade de tempo acumulado durante o qual cada Retransmissão do Barramento de Serviço fica "aberta". Uma Retransmissão é implicitamente instanciada e aberta em determinado endereço do Barramento de Serviço (URL do namespace do serviço) quando um serviço WCF habilitado para retransmissão ou "Ouvinte de retransmissão", se conectar pela primeira vez a esse endereço. Ela será fechada apenas quando o último ouvinte se desconectar do endereço. Portanto, para fins de cobrança, uma retransmissão é considerada "aberta" a partir do momento em que o primeiro ouvinte de Retransmissão se conecta até o momento em que o último ouvinte de Retransmissão se desconecta do endereço do Barramento de Serviço dessa Retransmissão.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>E se eu tiver mais de um ouvinte conectado a uma determinada Retransmissão?
Em alguns casos, uma única Retransmissão pode ter vários ouvintes conectados. Uma Retransmissão é considerada "aberta" quando pelo menos um ouvinte de Retransmissão está conectado a ele. Adicionar mais ouvintes a uma Retransmissão aberta resultará em horas de retransmissão adicionais. O número de remetentes de Retransmissão (clientes que invocam ou enviam mensagens para Retransmissões) conectados a uma Retransmissão também não afeta o cálculo de horas de Retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como esse medidor de mensagens é calculado para Retransmissões de WCF?
**Isso se aplica apenas a Retransmissões de WCF e não é um custo de Conexões Híbridas**

Em geral, as mensagens cobráveis são calculadas para Retransmissões usando o mesmo método descrito acima para entidades agenciadas (filas, tópicos e assinaturas). No entanto, há algumas diferenças perceptíveis:

O envio de uma mensagem para uma Retransmissão do Barramento de Serviço é tratado como um envio “full through” para o ouvinte da Retransmissão que recebe a mensagem, em vez de como um envio para a Retransmissão do Barramento de Serviço seguido por uma entrega para o ouvinte de Retransmissão. Portanto, uma invocação de serviço do estilo solicitação-resposta (de até 64 KB) em relação a um ouvinte de Retransmissão resultará em duas mensagens cobráveis: uma mensagem cobrável para a solicitação e uma mensagem cobrável para a resposta (supondo que a resposta também seja \<= 64 KB). Isso é diferente de usar uma fila para atuar como mediador entre um cliente e um serviço. Neste último caso, o mesmo padrão de solicitação-resposta exigiria um envio de solicitação para a fila, seguido por uma remoção da fila/entrega da fila para o serviço, seguido por um envio de resposta para outra fila e uma remoção da fila/entrega dessa fila para o cliente. Usando as mesmas suposições de tamanho (\<= 64 KB) para tudo, o padrão de fila mediado resultaria em quatro mensagens cobráveis, duas vezes o número cobrado para implementar o mesmo padrão usando a Retransmissão. É claro que há vantagens em usar filas para atingir esse padrão, como durabilidade e nivelamento de carga. Essas vantagens podem justificar a despesa adicional.

As retransmissões abertas usando a associação WCF netTCPRelay tratam as mensagens não como mensagens individuais, mas como um fluxo de dados através do sistema. Em outras palavras, somente o remetente e o ouvinte têm visibilidade do enquadramento das mensagens individuais enviadas/recebidas usando essa associação. Assim, para Retransmissões usando a associação netTCPRelay, todos os dados são tratados como uma transmissão para fins de cálculo das mensagens cobráveis. Nesse caso, o Barramento de Serviço calculará a quantidade total de dados enviados ou recebidos por meio de cada Retransmissão individual a cada 5 minutos e dividirá esse total por 64 KB para determinar o número de mensagens cobráveis para a retransmissão em questão durante aquele período.

## <a name="quotas"></a>Cotas
| Nome da Cota | Escopo | Tipo | Comportamento quando excedido | Valor |
| --- | --- | --- | --- | --- |
| Ouvintes simultâneos em uma retransmissão |Entidade |Estático |Solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. |25 |
| Ouvintes simultâneos da retransmissão |Todo o sistema |Estático |Solicitações subsequentes de conexões adicionais serão rejeitadas e uma exceção será recebida pelo código de chamada. |2.000 |
| Conexões de retransmissão simultâneas por todos os pontos de extremidade de retransmissão em um namespace de serviço |Todo o sistema |Estático |- |5.000 |
| Pontos de extremidade de retransmissão por namespace de serviço |Todo o sistema |Estático |- |10.000 |
| Tamanho de mensagem para retransmissões [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) e [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) |Todo o sistema |Estático |As mensagens de entrada que excederem essas cotas serão rejeitadas e uma exceção será recebida pelo código de chamada. |64 KB |
| Tamanho de mensagem para retransmissões [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) e [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) |Todo o sistema |Estático |- |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A retransmissão tem alguma cota de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas de um cliente. Como compreendemos que talvez seja necessário usar mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos entender as suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, as cotas totais de uso são:

* 5 bilhões de mensagens
* 2 milhões de horas de Retransmissão

Embora reservemos o direito de desabilitar uma conta de cliente que tenha excedido suas cotas de uso em determinado mês, forneceremos uma notificação por email e faremos várias tentativas de contatar o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelas cobranças que excederem as cotas.

### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um namespace de Retransmissão pode ter apenas entre 6 e 50 caracteres.

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?
Você pode usar comandos do PowerShell (encontrados no artigo [aqui](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) para mover um namespace de uma assinatura do Azure para outra. Para executar a operação, o namespace já deve estar ativo. Além disso, o usuário que executa os comandos deve ser administrador nas assinaturas de origem e de destino.

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs de Retransmissão do Azure e suas ações sugeridas?
O artigo [Exceções de retransmissão][Relay exceptions] descreve algumas exceções às ações sugeridas.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?
As Assinaturas de Acesso Compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para saber mais sobre como gerar suas próprias assinaturas no Node, PHP, Java e C\#, veja o artigo [Assinaturas de Acesso Compartilhado][Shared Access Signatures].

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas-overview.md

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível fazer uma lista de pontos de extremidade de Retransmissão?
Sim. O cliente de Retransmissão faz conexões com o serviço de Retransmissão usando nomes de domínio totalmente qualificado. Isso permite aos clientes adicionar uma entrada para `*.servicebus.windows.net` nos firewalls que oferecem suporte à lista de permissões de DNS.

## <a name="next-steps"></a>Próximas etapas
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Jan17_HO4-->


