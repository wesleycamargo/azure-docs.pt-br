<properties
	pageTitle="Hubs de Notificação do Azure - Perguntas frequentes (FAQs)"
	description="Perguntas frequentes sobre como projetar/implementar soluções em Hubs de Notificação"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="dwrede"
    keywords="notificação por push, notificações por push, notificações por push do iOS, notificações por push do android, push do ios, push do android"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="wesmc" />

#Notificações por push com os Hubs de Notificação do Azure - perguntas frequentes

##Geral
###1\. Qual é o modelo de preço para os Hubs de Notificação?
Os Hubs de Notificação são oferecidos em três camadas:

* **Gratuita** - obtenha até um milhão envios por push por assinatura em um mês.
* **Básico** - obtenha 10 milhões de envios por push por assinatura em um mês como uma linha de base, sem opções de aumento de cota.
* **Padrão** - Obtenha 10 milhões de envios por push por assinatura em um mês como uma linha de base, com opções de aumento de cota, além de recursos avançados de telemetria.

Os detalhes mais recentes podem ser encontrados na página [Preços dos Hubs de Notificação]. Os preços serão estabelecidos no nível da assinatura e se baseiam no número de iniciações de notificações por push, portanto não importa quantos namespaces ou hubs de notificação você tenha criado em sua assinatura do Azure.

A camada **Gratuita** é oferecida para fins de desenvolvimento, sem nenhuma garantia de SLA. Embora essa camada possa ser um bom ponto de partida para aqueles que queiram explorar os recursos das notificações por push por meio dos Hubs de Notificação do Azure, ela pode não ser a melhor opção para os aplicativos de média e larga escala.

Os tipos **Básico** e **Standard** são oferecidas para uso em produção com os seguintes recursos principais habilitados *somente para a camada Padrão*:

- *Telemetria avançada* - os Hubs de notificação oferecem uma série de recursos para exportar os dados de telemetria, bem como para enviar informações de registro de notificação por push para exibição e análise offline.
- *Multilocação* - ideal se você estiver criando um aplicativo móvel usando os Hubs de Notificação para oferecer suporte a vários locatários. Isso permite que você defina credenciais de Serviços de Notificação por Push (PNS) no nível de namespace do Hub de Notificação para o aplicativo e desta forma, você pode separar os locatários fornecendo a eles hubs individuais neste namespace comum. Facilita a manutenção e mantém as chaves SAS para enviar e receber notificações por push dos hubs de notificação separados para cada locatário, garantindo a sobreposição não cruzada do locatário.
- *Envio por Push Agendado* - permite agendar notificações por push, que posteriormente serão enfileiradas e enviadas.
- *Importação em massa* - permite a você importar registros em massa.

###2\. O que é o SLA dos Hubs de Notificação?
Para os tipos **Básico** e **Standard** dos Hubs de Notificação, garantimos que em pelo menos 99,9% do tempo, os aplicativos configurados corretamente poderão enviar notificações por push ou executar operações de gerenciamento de registros em relação a um Hub de Notificação implantado em uma camada com suporte. Para saber mais sobre o nosso SLA, visite a página [SLA dos Hubs de Notificação].

> [AZURE.NOTE] Não há nenhuma garantia de SLA para o segmento entre o Serviço de Notificação de Plataforma e o dispositivo, visto que os Hubs de Notificação dependem de provedores externos de plataforma para entregar a notificação por push para o dispositivo.

###3\. Quais clientes estão usando os Hubs de Notificação?
Temos um grande número de clientes importantes usando os Hubs de Notificação:

* Sochi 2014 – centenas de grupos de interesse, mais de três milhões de dispositivos, mais de 150 milhões de notificações enviadas em duas semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle Times - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Aplicativos Bing – dezenas de milhões de dispositivos, enviando três milhões de notificações por dia.

###4\. Como faço para atualizar ou fazer downgrade dos meus Hubs de Notificação para alterar minha camada de serviço?
Acesse o [Portal Clássico do Azure], clique em Barramento de Serviço, clique no namespace e no hub de notificação. Na guia Escala, você poderá alterar a camada de serviço dos Hubs de Notificação.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##Design e Desenvolvimento
###1\. A quais plataformas do lado do servidor você oferece suporte?
Nós fornecemos SDKs e [exemplos completos] para .NET, Java, PHP, Python, Node .js para que um back-end do aplicativo possa ser configurado para se comunicar com os Hubs de Notificação usando qualquer uma dessas plataformas. As APIs de Hubs de Notificação se baseiam em interfaces REST para que você possa optar por conversar diretamente com elas caso não queira adicionar uma dependência extra. Encontre mais detalhes na página [HN - APIs REST].

###2\. A quais plataformas de cliente você oferece suporte?
Oferecemos suporte ao envio de notificações por push para as plataformas [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push notification-apns-get-started.md) e [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para obter uma lista completa de tutoriais de introdução ao tratamento de notificações por push nessas plataformas, visite nossa página [HN - tutoriais de introdução].

###3\. Você oferece suporte a notificações de Email/SMS/web?
Os Hubs de Notificação são projetados principalmente para enviar notificações aos aplicativos móveis usando as plataformas listadas acima. Ainda não oferecemos o recurso de enviar email ou alertas por SMS, no entanto plataformas de terceiros que fornecem esses recursos podem ser integradas com Hubs de Notificação para enviar notificações de push nativo usando os [Aplicativos Móveis do Azure].

Os Hubs de Notificação também não oferecem um serviço de entrega de notificações por push no navegador pronto para uso. Os clientes podem optar por implementar isso usando o SignalR sobre as plataformas com suporte do lado do servidor. Se você estiver procurando enviar notificações aos aplicativos de navegador na área restrita do Chrome, confira o [tutorial de Aplicativos do Chrome].

###4\. Qual é a relação entre os Aplicativos Móveis do Azure e os Hubs de Notificação do Azure e quando usá-los?
Se você tiver um back-end do aplicativo móvel existente e desejar apenas adicionar o recurso para enviar notificações por push, então poderá usar os Hubs de Notificação do Azure. Se quiser configurar o back-end do aplicativo móvel do zero, você deverá considerar o uso dos Aplicativos Móveis do Azure. Um Aplicativo Móvel do Azure provisiona automaticamente um Hub de Notificação para que você possa enviar notificações por push facilmente a partir do back-end do aplicativo móvel. Os preços dos Aplicativos Móveis do Azure incluem os encargos de base para um Hub de Notificação e você paga apenas quando ultrapassar os envios incluídos. Há mais detalhes sobre os custos disponíveis na página [Preços do Serviço de Aplicativo].

###5\. Para quantos dispositivos posso dar suporte se eu enviar notificações por push por meio dos Hubs de Notificação?
Consulte a página [Preços dos Hubs de Notificação] para obter detalhes sobre o número de dispositivos com suporte.

Para determinados cenários, se você precisar de suporte para mais de 10.000.000 dispositivos registrados, [entre em contato conosco](https://azure.microsoft.com/overview/contact-us/) diretamente e ajudaremos você a dimensionar sua solução.

###6\. Quantas notificações por push posso enviar?
Dependendo da camada selecionada, o Azure escalará verticalmente de forma automática, com base no número de notificações fluindo pelo sistema.

>[AZURE.NOTE] O custo de uso geral pode subir com base no número de notificações por push servidas. Esteja ciente dos limites de camada existente indicados na página [Preços dos Hubs de Notificação].

Nossos clientes atuais estão usando os Hubs de Notificação para enviar milhões de notificações por push por dia. Você não precisa fazer nada especial para dimensionar o envio de notificações por push, desde que esteja usando os Hubs de Notificação do Azure.

###7\. Quanto tempo leva para as notificações chegarem ao meu dispositivo?
Os Hubs de Notificação do Azure conseguem processar pelo menos **um milhão envios por push em um minuto** em um cenário de uso normal em que a carga de entrada é muito consistente e não aumenta consideravelmente por natureza. Essa taxa pode variar dependendo do número de marcas, da natureza dos envios de entrada e outros fatores externos.

Durante o tempo de entrega estimado, o serviço pode calcular os destinos por plataforma e encaminhar mensagens para os serviços de entrega de notificação por push respectivos com base em expressões de marca/marcas registradas. Daqui em diante, é de responsabilidade dos Serviços de Notificações por Push (PNS) para enviar a notificação para o dispositivo.

Um PNS não garante qualquer SLA para a entrega de notificações; no entanto, normalmente a maior parte das notificações por push é entregue aos dispositivos de destino em poucos minutos (em geral, em menos de 10 minutos) desde o momento em que elas são enviadas para nossa plataforma. Talvez haja algumas exceções que podem levar mais tempo.

>[AZURE.NOTE] Os Hubs de Notificação do Azure têm uma política em vigor para descartar todas as notificações por push que não puderem ser entregues ao PNS em 30 minutos. Esse atraso pode acontecer por várias razões, mais comumente porque o PNS está limitando seu aplicativo.

###8\. Há qualquer garantia de latência?
Devido à natureza das notificações por push (entregues por uma plataforma externa específica, o Serviço de Notificações por Push), não há nenhuma garantia de latência. Normalmente, a maioria das notificações é entregue em alguns minutos.

###9\. Que considerações devo fazer durante a criação de uma solução com namespaces e Hubs de Notificação?

####Ambiente/Aplicativo Móvel

* Deve haver um Hub de Notificação por aplicativo móvel, por ambiente.
* Em um cenário multilocatário, cada locatário deve ter um hub separado.
* Você nunca deve compartilhar o mesmo Hub de Notificação entre ambientes de teste e produção, visto que isso pode causar problemas futuros durante o envio de notificações. Por exemplo, a Apple oferece pontos de extremidade de área restrita e push de produção, cada qual com credenciais separadas.
* Por padrão, você pode enviar notificações de teste para seus dispositivos registrados por meio do Portal do Azure ou do componente integrado do Azure no Visual Studio. O limite é definido como 10 dispositivos selecionados aleatoriamente do pool de registro.

>[AZURE.NOTE] Se o hub tiver sido originalmente configurado com um certificado de área restrita da Apple e depois reconfigurado para usar um certificado de produção da Apple, os tokens de dispositivo antigos se tornariam inválidos com o novo certificado e causariam falhas de envios por push. É melhor separar a produção e ambientes de teste e usar os hubs diferentes para ambientes diferentes.

####Credenciais PNS

Quando um aplicativo móvel é registrado com o portal do desenvolvedor da plataforma (por exemplo, Apple ou Google etc), então você obtém um identificador de aplicativos e tokens de segurança que um back-end do aplicativo precisa fornecer serviços de Notificação por Push da Plataforma para poder enviar notificações por push para os dispositivos. Esses tokens de segurança que podem ser apresentados na forma de certificados (por exemplo, para Apple iOS ou Windows Phone) ou chaves de segurança (Google Android, Windows, etc.) precisam ser configurados nos Hubs de Notificação. Isso é normalmente feito no nível do hub de notificação, mas também pode ser feito no nível de namespace em um cenário de multilocatário.

####Namespaces

Os namespaces podem ser usados para o agrupamento de implantação. Também podem ser usados para representar todos os Hubs de Notificação para todos os locatários do mesmo aplicativo no cenário de multilocatário.

####Distribuição geográfica

A distribuição geográfica nem sempre é crítica em cenários de notificações por push. Deve-se observar que vários serviços de Notificação por push (por exemplo, APNS, GCM etc.) que, por fim, entregam as notificações por push aos dispositivos, não são igualmente distribuídos.

Se você tiver um aplicativo usado no mundo inteiro, poderá criar vários hubs em namespaces diferentes, aproveitando a disponibilidade do serviço de Hubs de Notificação em diferentes regiões do Azure no mundo todo.

>[AZURE.NOTE] Isso aumentará o custo de gerenciamento, principalmente em torno de registros, então realmente não é recomendado e só deve ser feito se houver uma necessidade explícita.

###10\. Devo fazer registros do back-end do aplicativo ou diretamente por meio de dispositivos cliente?
Os registros de back-end do aplicativo são úteis quando você precisa fazer a autenticação de cliente antes de criar o registro ou quando há marcas que devem ser criadas ou modificadas pelo back-end do aplicativo, com base em alguma lógica de aplicativo. Para obter detalhes, leia mais nas páginas [Diretrizes de registro de back-end] e [Diretrizes de registro de back-end - 2].

###11\. O que é o modelo de segurança de entrega de notificação por push?
Os Hubs de Notificação do Azure usam um modelo de segurança baseado em [Assinatura de Acesso Compartilhado (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). Você pode usar os tokens SAS no nível do namespace raiz ou no nível de Hubs de Notificação granular. Esses tokens SAS podem ser definidos com diferentes regras de autorização por exemplo, as permissões para enviar mensagens, permissões para ouvir notificação etc. Mais detalhes estão disponíveis no documento [Modelo de segurança dos HN].

###12\. Como lidar com a carga confidencial nas notificações por push?
Todas as notificações são entregues aos dispositivos de destino pelos Serviços de Notificação (PNS) da plataforma. Quando um remetente enviar uma notificação para os Hubs de Notificação do Azure, processamos e transmitimos a notificação ao respectivo PNS.

Todas as conexões do remetente para os Hubs de Notificações do Azure e para o PNS usam HTTPS.

>[AZURE.NOTE] Os Hubs de Notificações do Azure não registram a carga da mensagem de forma alguma.

Para o envio de cargas confidenciais no entanto, recomendamos um padrão Push Seguro, em que o remetente entrega uma notificação 'ping' com um identificador de mensagem para o dispositivo sem a carga confidencial e quando o aplicativo no dispositivo recebe essa carga, pode chamar uma API segura diretamente para buscar os detalhes da mensagem. Um guia sobre como implementar o padrão descrito acima está disponível na página [HN - tutorial de Push Seguro].

##Operações
###1\. O que é a história de Recuperação de Desastres (DR)?
Fornecemos uma cobertura de Recuperação de Desastre de metadados em nosso lado (o nome do Hub de Notificação, a cadeia de conexão e outras informações fundamentais). Quando um cenário de Recuperação de Desastre for acionado, os dados dos registros serão o **único segmento** da infraestrutura dos Hubs de Notificação perdido. Você precisará implementar uma solução para preencher esses dados novamente em seu novo hub após a recuperação.

- *Etapa1* - Criar um Hub de Notificação secundário em um datacenter diferente. Você pode criá-lo dinamicamente no momento do evento de Recuperação de Desastre ou pode criá-lo do princípio. A opção escolhida não fará muita diferença, já que o provisionamento do Hub de Notificação é um processo relativamente rápido, na ordem de alguns segundos. Ter um desde o início também protegerá você do evento de Recuperação de Desastres que afete seus recursos de gerenciamento, por isso ele é a opção altamente recomendável.

- *Step2* - Alimentar o Hub de Notificação secundário com os registros do Hub de Notificação primário. Não é recomendável tentar manter os registros em ambos os hubs e tentar mantê-los em sincronia dinamicamente, visto que os registros estão chegando - em geral, isso não funciona bem por causa da tendência inerente de os registros expirarem no lado do PNS. Os Hubs de Notificação limpa-os enquanto recebemos comentários dos PNS sobre registros expirados ou inválidos.

Recomenda-se usar um back-end do aplicativo que:

- Mantém um determinado conjunto de registros em seu lado para poder fazer uma inserção em massa no hub de notificação secundário no caso de Recuperação de Desastre

**OR**

- Obtenha um despejo de registros do hub principal como um backup e depois faça uma inserção em massa no NH secundário.

>[AZURE.NOTE] A funcionalidade Exportação/Importação de Registros disponível na camada Padrão é descrita no documento [Importação/Exportação de Registros].

Se você não tiver um back-end, então quando o aplicativo for iniciado em qualquer um dos dispositivos de destino, eles executarão um novo registro no Hub de Notificação secundário e, por fim, o Hub de Notificação secundário terá todos os dispositivos ativos registrados.

A desvantagem é que haverá um período em que os dispositivos onde os aplicativos ainda não abriram não receberão notificações.

###2\. Há algum recurso de log de auditoria?
Todas as operações de Gerenciamento de Hubs de Notificação vão para Logs de Operação expostos no [Portal Clássico do Azure].

##Monitoramento e Solução de Problemas
###1\. Quais recursos de solução de problemas estão disponíveis?
Os Hubs de Notificação do Azure fornecem vários recursos para executar a solução de problemas comum, principalmente no cenário em torno de notificações removidas. Veja detalhes em nosso white paper [HN - solução de problemas].

###2\. Quais recursos de telemetria estão disponíveis?
Os Hubs de Notificação do Azure permitem a exibição de dados de telemetria no [Portal Clássico do Azure]. Há detalhes das métricas disponíveis na página [HN - métricas].

>[AZURE.NOTE] As notificações bem-sucedidas significam apenas que as notificações por push foram entregues ao Serviço de Notificação por Push externo (por exemplo, APNS para a Apple, GCM para o Google etc). É de responsabilidade do PNS entregar a notificação aos dispositivos de destino. Normalmente, o PNS não expõe as métricas de entrega para terceiros.

Também fornecemos a capacidade de exportar a telemetria programaticamente (na camada **Padrão**). Veja [HN - exemplo de métricas] para obter detalhes.

[Portal Clássico do Azure]: https://manage.windowsazure.com
[Preços dos Hubs de Notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[SLA dos Hubs de Notificação]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[HN - APIs REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[HN - tutoriais de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
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
[Azure Portal]: https://portal.azure.com
[exemplos completos]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicativos Móveis do Azure]: https://azure.microsoft.com/services/app-service/mobile/
[Preços do Serviço de Aplicativo]: https://azure.microsoft.com/pricing/details/app-service/

<!---HONumber=AcomDC_0622_2016-->