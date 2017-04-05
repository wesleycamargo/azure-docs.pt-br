---
title: "Hubs de Notificação do Azure - Perguntas frequentes (FAQs)"
description: "Perguntas frequentes sobre como projetar/implementar soluções em Hubs de Notificação"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "notificação por push, notificações por push, notificações por push do iOS, notificações por push do android, push do ios, push do android"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notificações por push com os Hubs de Notificação do Azure - perguntas frequentes
## <a name="general"></a>Geral
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.  O que é a estrutura de recursos dos Hubs de Notificação?

Os Hubs de Notificação têm dois níveis de recursos, hubs e namespaces. Um hub é um recurso de envio por push único que pode conter as informações de push de plataforma cruzada de um aplicativo. Um namespace é uma coleção de hubs em uma região.

O mapeamento geral recomendo coloca um namespace para corresponder a um aplicativo. Dentro do namespace, você pode ter um hub de produção que funciona com o seu aplicativo de produção, um hub de teste que funciona com o seu aplicativo de teste, etc.

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.  Qual é o modelo de preço para os Hubs de Notificação?
Os detalhes mais recentes podem ser encontrados na página [Preços dos Hubs de Notificação] . Os Hubs de Notificação são cobrados no namespace de nível (veja a estrutura de recurso acima para saber o que é um namespace) e oferecem três camadas:

* **Gratuito** – esse é um bom ponto de partida para explorar os recursos de envio por push, não é recomendados para aplicativos de produção. 500 dispositivos e um milhão de envios são incluídos por namespace por mês, sem nenhuma garantia de SLA.
* **Básico** – recomenda-se essa camada ou a Standard para aplicativos de produção menores. 200 mil dispositivos e dez milhões de envios por push são incluídos por namespace por mês como uma linha de base, com opções de aumento de cota.
* **Standard** – recomendado para aplicativos de produção médios a grandes. Dez milhões de dispositivos e dez milhões de envios por push são incluídos por namespace por mês como uma linha de base, com opções de aumento de cota e funcionalidades de telemetria avançada.

Aqui estão alguns recursos incríveis da camada Standard:
* *Telemetria avançada* – os Hubs de Notificação oferecem Telemetria Por Mensagem para rastrear quaisquer solicitações de envio por push e Comentários do Sistema de Notificação de Plataforma para depurar.
* *Multilocação* – você pode trabalhar com as credenciais do PNS (Sistema de Notificação de Plataforma) em um nível de namespace. Isso permite que você dividida locatários facilmente em hubs no mesmo namespace.
* *Push agendado* – você pode agendar para que notificações sejam enviadas a qualquer momento.

### <a name="2--what-is-the-notification-hubs-sla"></a>2.  O que é o SLA dos Hubs de Notificação?
Para os tipos **Básico** e **Standard** dos Hubs de Notificação, garantimos que em pelo menos 99,9% do tempo, os aplicativos configurados corretamente poderão enviar notificações por push ou executar operações de gerenciamento de registros em relação a um Hub de Notificação implantado em uma camada com suporte. Para saber mais sobre o nosso SLA, visite a página [SLA dos Hubs de Notificação](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Como notificações por push dependem de Sistemas de Notificação de Plataforma de terceiros (APNs da Apple, FCM do Google, etc), não há nenhuma garantia de SLA para a entrega dessas mensagens. Depois que os Hubs de Notificação produzem os lotes de saída dos envios para PNSes (com garantia de SLA), cabe aos PNSes entregas os envios por push (sem garantia de SLA).

### <a name="3--which-customers-are-using-notification-hubs"></a>3.  Quais clientes estão usando os Hubs de Notificação?
Temos um grande número de clientes importantes usando os Hubs de Notificação, com alguns em destaque entre eles listados abaixo:

* Sochi 2014 – centenas de grupos de interesse, mais de três milhões de dispositivos, mais de 150 milhões de notificações enviadas em duas semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle Times - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Aplicativos Bing – dezenas de milhões de dispositivos, enviando três milhões de notificações por dia.

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4. Como atualizar ou fazer downgrade do meu hub ou namespace para uma camada diferente?
Vá para o [Portal do Azure], Namespaces de Hubs de Notificação ou Hubs de Notificação, clique no recurso que você gostaria de atualizar e vá para Tipo de Preço na barra de navegação. Você pode atualizar para a camada que desejar, com algumas observações:
* O tipo de preço atualizado será aplicado a *todos* os hubs no namespace com o qual você está trabalhando.
* Se você estiver fazendo downgrade e a contagem de dispositivos exceder o limite da camada para a qual você está tentando fazer o downgrade, você precisará excluir dispositivos para satisfazer o limite antes de poder fazer o downgrade.


## <a name="design--development"></a>Design e Desenvolvimento
### <a name="1--which-server-side-platforms-do-you-support"></a>1.  A quais plataformas do lado do servidor você oferece suporte?
Temos SDKs do servidor para .NET, Java, Node. js, PHP e Python. Além disso, APIs de Hubs de notificação baseiam-se em interfaces REST, portanto, você poderá optar por trabalhar diretamente com as APIs REST se você estiver trabalhando com diferentes plataformas ou não desejar dependência extra. Encontre mais detalhes na página [NH – APIs REST].

### <a name="2--which-client-platforms-do-you-support"></a>2.  A quais plataformas de cliente você oferece suporte?
Damos suporte ao envio de notificações por push para plataformas [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Veja mais em nossa página [Tutoriais de Introdução].

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.  Você oferece suporte a notificações de Email/SMS/web?
Os Hubs de Notificação são projetados principalmente para enviar notificações aos aplicativos móveis e não oferecem funcionalidades de email ou SMS. No entanto, plataformas de terceiros que fornecem esses recursos podem ser integradas com Hubs de Notificação para enviar notificações de push nativo usando os [Aplicativos Móveis do Azure].

Os Hubs de Notificação também não oferecem um serviço de entrega de notificações por push no navegador pronto para uso. Os clientes podem optar por implementar isso usando o SignalR sobre as plataformas com suporte do lado do servidor. Se você estiver procurando enviar notificações aos aplicativos de navegador na área restrita do Chrome, confira o [tutorial de Aplicativos do Chrome].

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.    Qual é a relação entre os Aplicativos Móveis do Azure e os Hubs de Notificação do Azure e quando usá-los?
Se você tiver um back-end do aplicativo móvel existente e desejar apenas adicionar o recurso para enviar notificações por push, então poderá usar os Hubs de Notificação do Azure. Se quiser configurar o back-end do aplicativo móvel do zero, você deverá considerar o uso dos Aplicativos Móveis do Azure. Um Aplicativo Móvel do Azure provisiona automaticamente um Hub de Notificação para que você possa enviar notificações por push facilmente a partir do back-end do aplicativo móvel. Os preços dos Aplicativos Móveis do Azure incluem os encargos de base para um Hub de Notificação e você paga apenas quando ultrapassar os envios incluídos. Há mais detalhes sobre os custos disponíveis na página [Preços do Serviço de Aplicativo] .

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.    Para quantos dispositivos posso dar suporte se eu enviar notificações por push por meio dos Hubs de Notificação?
Consulte a página [Preços dos Hubs de Notificação] para obter detalhes sobre o número de dispositivos com suporte.

Para determinados cenários, se você precisar de suporte para mais de 10.000.000 dispositivos registrados, [entre em contato conosco](https://azure.microsoft.com/overview/contact-us/) diretamente e ajudaremos você a dimensionar sua solução.

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.    Quantas notificações por push posso enviar?
Dependendo da camada selecionada, o Azure escalará verticalmente de forma automática, com base no número de notificações fluindo pelo sistema.

> [!NOTE]
> O custo de uso geral pode subir com base no número de notificações por push servidas. Esteja ciente dos limites de camada existente indicados na página [Preços dos Hubs de Notificação] .
> 
> 

Nossos clientes atuais estão usando os Hubs de Notificação para enviar milhões de notificações por push por dia. Você não precisa fazer nada especial para dimensionar o envio de notificações por push, desde que esteja usando os Hubs de Notificação do Azure.

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.    Quanto tempo leva para as notificações chegarem ao meu dispositivo?
Os Hubs de Notificação do Azure conseguem processar pelo menos **um milhão envios por push em um minuto** em um cenário de uso normal em que a carga de entrada é muito consistente e não aumenta consideravelmente por natureza. Essa taxa pode variar dependendo do número de marcas, da natureza dos envios de entrada e outros fatores externos.

Durante o tempo de entrega estimado, o serviço pode calcular os destinos por plataforma e encaminhar mensagens para os serviços de entrega de notificação por push respectivos com base em expressões de marca/marcas registradas. Daqui em diante, é de responsabilidade dos Serviços de Notificações por Push (PNS) para enviar a notificação para o dispositivo.

Um PNS não garante qualquer SLA para a entrega de notificações; no entanto, normalmente a maior parte das notificações por push é entregue aos dispositivos de destino em poucos minutos (em geral, em menos de 10 minutos) desde o momento em que elas são enviadas para nossa plataforma. Talvez haja algumas exceções que podem levar mais tempo.

> [!NOTE]
> Os Hubs de Notificação do Azure têm uma política em vigor para descartar todas as notificações por push que não puderem ser entregues ao PNS em 30 minutos. Esse atraso pode acontecer por várias razões, mais comumente porque o PNS está limitando seu aplicativo.
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.    Há qualquer garantia de latência?
Devido à natureza das notificações por push (entregues por uma plataforma externa específica, o Serviço de Notificações por Push), não há nenhuma garantia de latência. Normalmente, a maioria das notificações é entregue em alguns minutos.

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.    Que considerações devo fazer durante a criação de uma solução com namespaces e Hubs de Notificação?
#### <a name="mobile-appenvironment"></a>Ambiente/Aplicativo Móvel
* Deve haver um Hub de Notificação por aplicativo móvel, por ambiente.
* Em um cenário multilocatário, cada locatário deve ter um hub separado.
* Você nunca deve compartilhar o mesmo Hub de Notificação entre ambientes de teste e produção, visto que isso pode causar problemas futuros durante o envio de notificações. Por exemplo, a Apple oferece pontos de extremidade de área restrita e push de produção, cada qual com credenciais separadas.
* Por padrão, você pode enviar notificações de teste para seus dispositivos registrados por meio do Portal do Azure ou do componente integrado do Azure no Visual Studio. O limite é definido como 10 dispositivos selecionados aleatoriamente do pool de registro.

> [!NOTE]
> Se o hub tiver sido originalmente configurado com um certificado de área restrita da Apple e depois reconfigurado para usar um certificado de produção da Apple, os tokens de dispositivo antigos se tornariam inválidos com o novo certificado e causariam falhas de envios por push. É melhor separar a produção e ambientes de teste e usar os hubs diferentes para ambientes diferentes.
> 
> 

#### <a name="pns-credentials"></a>Credenciais PNS
Quando um aplicativo móvel é registrado com o portal do desenvolvedor da plataforma (por exemplo, Apple ou Google etc), então você obtém um identificador de aplicativos e tokens de segurança que um back-end do aplicativo precisa fornecer serviços de Notificação por Push da Plataforma para poder enviar notificações por push para os dispositivos. Esses tokens de segurança que podem ser apresentados na forma de certificados (por exemplo, para Apple iOS ou Windows Phone) ou chaves de segurança (Google Android, Windows, etc.) precisam ser configurados nos Hubs de Notificação. Isso é normalmente feito no nível do hub de notificação, mas também pode ser feito no nível de namespace em um cenário de multilocatário.

#### <a name="namespaces"></a>Namespaces
Os namespaces podem ser usados para o agrupamento de implantação.  Também podem ser usados para representar todos os Hubs de Notificação para todos os locatários do mesmo aplicativo no cenário de multilocatário.

#### <a name="geo-distribution"></a>Distribuição geográfica
A distribuição geográfica nem sempre é crítica em cenários de notificações por push. Deve-se observar que vários serviços de Notificação por push (por exemplo, APNS, GCM etc.) que, por fim, entregam as notificações por push aos dispositivos, não são igualmente distribuídos.

Se você tiver um aplicativo usado no mundo inteiro, poderá criar vários hubs em namespaces diferentes, aproveitando a disponibilidade do serviço de Hubs de Notificação em diferentes regiões do Azure no mundo todo.

> [!NOTE]
> Isso aumentará o custo de gerenciamento, principalmente em torno de registros, então realmente não é recomendado e só deve ser feito se houver uma necessidade explícita.
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.    Devo fazer registros do back-end do aplicativo ou diretamente por meio de dispositivos cliente?
Os registros de back-end do aplicativo são úteis quando você precisa fazer a autenticação de cliente antes de criar o registro ou quando há marcas que devem ser criadas ou modificadas pelo back-end do aplicativo, com base em alguma lógica de aplicativo. Para obter detalhes, leia mais nas páginas [Diretrizes de registro de back-end] e [Diretrizes de registro de back-end - 2].

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.    O que é o modelo de segurança de entrega de notificação por push?
Os Hubs de Notificação do Azure usam um modelo de segurança baseado em [Assinatura de Acesso Compartilhado (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). Você pode usar os tokens SAS no nível do namespace raiz ou no nível de Hubs de Notificação granular. Esses tokens SAS podem ser definidos com diferentes regras de autorização por exemplo, as permissões para enviar mensagens, permissões para ouvir notificação etc. Mais detalhes estão disponíveis no documento [Modelo de segurança dos HN].

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.    Como lidar com a carga confidencial nas notificações por push?
Todas as notificações são entregues aos dispositivos de destino pelos Serviços de Notificação (PNS) da plataforma. Quando um remetente enviar uma notificação para os Hubs de Notificação do Azure, processamos e transmitimos a notificação ao respectivo PNS.

Todas as conexões do remetente para os Hubs de Notificações do Azure e para o PNS usam HTTPS.

> [!NOTE]
> Os Hubs de Notificações do Azure não registram a carga da mensagem de forma alguma.
> 
> 

Para o envio de cargas confidenciais no entanto, recomendamos um padrão Push Seguro, em que o remetente entrega uma notificação 'ping' com um identificador de mensagem para o dispositivo sem a carga confidencial e quando o aplicativo no dispositivo recebe essa carga, pode chamar uma API segura diretamente para buscar os detalhes da mensagem. Um guia sobre como implementar o padrão descrito acima está disponível na página [HN - tutorial de Push Seguro] .

## <a name="operations"></a>Operações
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.    O que é a história de Recuperação de Desastres (DR)?
Fornecemos uma cobertura de Recuperação de Desastre de metadados em nosso lado (o nome do Hub de Notificação, a cadeia de conexão e outras informações fundamentais). Quando um cenário de Recuperação de Desastre for acionado, os dados dos registros serão o **único segmento** da infraestrutura dos Hubs de Notificação perdido. Você precisará implementar uma solução para preencher esses dados novamente em seu novo hub após a recuperação.

* *Etapa1* - Criar um Hub de Notificação secundário em um datacenter diferente. Você pode criá-lo dinamicamente no momento do evento de Recuperação de Desastre ou pode criá-lo do princípio. A opção escolhida não fará muita diferença, já que o provisionamento do Hub de Notificação é um processo relativamente rápido, na ordem de alguns segundos. Ter um desde o início também protegerá você do evento de Recuperação de Desastres que afete seus recursos de gerenciamento, por isso ele é a opção altamente recomendável.
* *Step2* - Alimentar o Hub de Notificação secundário com os registros do Hub de Notificação primário. Não é recomendável tentar manter os registros em ambos os hubs e tentar mantê-los em sincronia dinamicamente, visto que os registros estão chegando - em geral, isso não funciona bem por causa da tendência inerente de os registros expirarem no lado do PNS. Os Hubs de Notificação limpa-os enquanto recebemos comentários dos PNS sobre registros expirados ou inválidos.  

Recomenda-se usar um back-end do aplicativo que:

* Mantém um determinado conjunto de registros em seu lado para poder fazer uma inserção em massa no hub de notificação secundário no caso de Recuperação de Desastre

**OR**

* Obtenha um despejo de registros do hub principal como um backup e depois faça uma inserção em massa no NH secundário.

> [!NOTE]
> A funcionalidade Exportação/Importação de Registros disponível na camada Padrão é descrita no documento [Importação/Exportação de Registros] .
> 
> 

Se você não tiver um back-end, então quando o aplicativo for iniciado em qualquer um dos dispositivos de destino, eles executarão um novo registro no Hub de Notificação secundário e, por fim, o Hub de Notificação secundário terá todos os dispositivos ativos registrados.

A desvantagem é que haverá um período em que os dispositivos onde os aplicativos ainda não abriram não receberão notificações.

### <a name="2----is-there-any-audit-log-capability"></a>2.    Há algum recurso de log de auditoria?
Todas as operações de Gerenciamento de Hubs de Notificação vão para Logs de Operação expostos no [Portal Clássico do Azure].

## <a name="monitoring--troubleshooting"></a>Monitoramento e Solução de Problemas
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.    Quais recursos de solução de problemas estão disponíveis?
Os Hubs de Notificação do Azure fornecem vários recursos para executar a solução de problemas comum, principalmente no cenário em torno de notificações removidas. Veja detalhes em nosso white paper [HN - solução de problemas] .

### <a name="2----what-telemetry-features-are-available"></a>2.    Quais recursos de telemetria estão disponíveis?
Os Hubs de Notificação do Azure permitem a exibição de dados de telemetria no [Portal Clássico do Azure]. Há detalhes das métricas disponíveis na página [HN - métricas] .

> [!NOTE]
> As notificações bem-sucedidas significam apenas que as notificações por push foram entregues ao Serviço de Notificação por Push externo (por exemplo, APNS para a Apple, GCM para o Google etc). É de responsabilidade do PNS entregar a notificação aos dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega para terceiros.  
> 
> 

Também fornecemos a capacidade de exportar a telemetria programaticamente (na camada **Padrão** ). Veja [HN - exemplo de métricas] para obter detalhes.

[Portal Clássico do Azure]: https://manage.windowsazure.com
[Preços dos Hubs de Notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH – APIs REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[tutorial de Aplicativos do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Diretrizes de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Diretrizes de registro de back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança dos HN]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[HN - tutorial de Push Seguro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[HN - solução de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[HN - métricas]: https://msdn.microsoft.com/library/dn458822.aspx
[HN - exemplo de métricas]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importação/Exportação de Registros]: https://msdn.microsoft.com/library/dn790624.aspx
[Portal do Azure]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicativos Móveis do Azure]: https://azure.microsoft.com/services/app-service/mobile/
[Preços do Serviço de Aplicativo]: https://azure.microsoft.com/pricing/details/app-service/

