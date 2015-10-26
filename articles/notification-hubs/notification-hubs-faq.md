<properties
	pageTitle="Hubs de Notificação do Azure - Perguntas frequentes (FAQs)"
	description="Perguntas frequentes sobre como projetar/implementar soluções em Hubs de Notificação"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc"
	manager="dwrede"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc" />

#Hubs de Notificação do Azure - Perguntas frequentes (FAQs)

##Geral
###1\. Qual é o preço para Hubs de Notificação?
Hubs de Notificação são oferecidos em três camadas - as camadas *Gratuito*, *Básico* e *Padrão*. Mais detalhes aqui - [Preços de Hubs de Notificação]. O preço será cobrado no nível de assinatura e se baseia no número de envios por push, então não importa quantos namespaces ou hubs de notificação você tem. A camada Gratuito é oferecida para fins de desenvolvimento com nenhuma garantia de SLA. As camadas Básico e Padrão são oferecidas para uso em produção com os seguintes recursos principais habilitados somente para a camada Padrão:

- *Difusão* -A camada Básica limita o número de marcas de Hub de Notificação para 3K (se aplica a dispositivos > 5). Se você tiver um tamanho de audiência maior que 3K, então você deve mover para a camada Padrão.
- *Telemetria sofisticada* -A camada Básico não permite exportar os dados de telemetria ou de registros. Se precisar do recurso para exportar seus dados de telemetria para exibição offline e análise, então você deve mudar para a camada Padrão.
- *Multilocação* - Se você estiver criando um aplicativo móvel usando os Hubs de Notificação para oferecer suporte a vários locatários, então você deve considerar mudar para a camada Padrão. Isso permite que você defina credenciais de Serviços de Notificação por Push (PNS) no nível de namespace do Hub de Notificação para o aplicativo e desta forma, você pode separar os locatários fornecendo a eles hubs individuais neste namespace comum. Isso permite a facilidade de manutenção enquanto mantém as chaves SAS para enviar e receber notificações de hubs de notificação separados para cada locatário, garantindo a sobreposição não cruzada do locatário.

###2\. O que é o SLA?
Para as camadas do Hub de Notificação Básico e Padrão, garantimos que pelo menos 99,9% do tempo, os aplicativos configurados corretamente poderão enviar notificações ou executar operações de gerenciamento de registro em relação a um Hub de Notificação implantado dentro de uma camada do Hub de Notificação Básico ou Padrão. Para saber mais sobre nosso SLA, visite a página do SLA aqui - [SLA de Hubs de Notificação]. Observe que não há nenhuma garantia de SLA para o segmento entre o Serviço de Notificação de Plataforma e o dispositivo, visto que os Hubs de Notificação dependem de provedores externos de plataforma para entregar a notificação para o dispositivo.

###3\. Quais clientes estão usando os Hubs de Notificação?
Temos um número de clientes usando Hubs de Notificação com alguns importantes abaixo:

* Sochi 2014 – centenas de grupos de interesse, mais 3 milhões de dispositivos, mais de 150 milhões de notificações enviadas em 2 semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Seattle Times - [CaseStudy - Seattle Times]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Aplicativos Bing – dezenas de milhões de dispositivos, enviando 3 milhões de notificações por dia

##Design e Desenvolvimento
###1\. Quais plataformas do lado do serviço você oferece suporte?
Fornecemos SDKs e exemplos para .NET, Java, PHP, Python, Node .js para que um back-end do aplicativo possa ser configurado para se comunicar com os Hubs de Notificação usando qualquer uma dessas plataformas. APIs de Hubs de Notificação são baseadas em interface REST para que você escolha falar diretamente com elas. Mais detalhes aqui - [NH - APIs REST]

###2\. Quais plataformas de dispositivo você oferece suporte?
Oferecemos suporte para notificações de envio para Apple iOS, Android, Windows Universal e Windows Phone, Kindle, Android China (via Baidu), Xamarin (iOS e Android), plataformas de aplicativos Chrome. Tutoriais de introdução passo a passo para essas plataformas estão disponíveis aqui - [NH - Tutoriais de Introdução]

###3\. Você oferece suporte a notificações de Email/SMS/web?
Os Hubs de Notificação são projetados principalmente para enviar notificações aos aplicativos móveis usando as plataformas listadas acima. Não oferecemos o recurso de enviar email ou SMS, no entanto plataformas de terceiros que fornecem esses recursos podem ser integradas com Hubs de Notificação para enviar notificações de push nativo usando os Serviços Móveis do Azure. Também não fornecemos uma notificação por push no navegador pronto para uso. Os clientes podem optar por implementar isso usando o SignalR. Nós também fornecemos um tutorial para enviar notificação por push para aplicativos do Chrome que funcionarão no navegador Google Chrome. Veja isto - [Tutorial de aplicativos do Chrome]

###4\. Qual é a relação entre os Serviços Móveis do Azure e os Hubs de Notificação do Azure e quando usar qual?
Se você tiver um back-end do aplicativo móvel existente e desejar apenas adicionar o recurso para enviar notificações por push, então você deve usar os Hubs de Notificação do Azure. Se quiser configurar o back-end do aplicativo móvel do zero, então você deve considerar o uso dos Serviços Móveis do Azure. Um serviço móvel do Azure provisiona automaticamente um Hub de Notificação para que você possa enviar notificações por push facilmente a partir do back-end do aplicativo móvel. Os preços dos Serviços Móveis do Azure incluem os encargos de base para um Hub de Notificação e você paga apenas quando ultrapassar os envios incluídos. Mais detalhes aqui - [Preços de Serviços Móveis]

###5\. Quantos dispositivos você oferece suporte?
Na camada Básico e Padrão - nós não impomos nenhum limite no número de dispositivos ativos que podem receber notificações. Para obter detalhes, consulte: [Preços de Hubs de Notificação]

###6\. Quantas notificações por push posso enviar?
Os clientes estão usando os Hubs de Notificação do Azure para enviar milhões de notificações por push por dia. Você não precisa fazer nada extra para dimensionar os Hubs de Notificações. Podemos dimensionar automaticamente com base no número de notificações que fluem através do sistema. Observe que o preço é afetado com base nas notificações de push servidas.

###7\. Quanto tempo leva para as notificações alcançar meu dispositivo?
Os Hubs de Notificação do Azure conseguem processar pelo menos 1 milhão envios em 1 minuto em um cenário de uso normal em que a carga de entrada é muito consistente e não aumenta consideravelmente por natureza. Essa taxa pode variar dependendo do número de marcas, da natureza dos envios de entrada etc. Nessa duração, somos capazes de calcular os destinos por plataforma e encaminhar mensagens para os serviços de notificação por push respectivos com base em expressões de marca/marcas registradas. Daqui em diante, é de responsabilidade dos Serviços de Notificações por Push (PNS) para enviar a notificação para o dispositivo. O PNSs não garante qualquer SLA para entregar notificações, no entanto, normalmente a maior parte das mensagens são entregues aos dispositivos em poucos minutos (< 10 minutos) desde o momento em que elas são enviadas para nossa plataforma. Talvez haja algumas exceções que podem levar mais tempo. Os Hubs de Notificação do Azure também têm uma política em vigor para descartar todas as notificações que não podem ser entregues ao PNS em 30 minutos. Esse atraso pode acontecer por várias razões, mais comumente porque o PNS está limitando seu aplicativo.

###8\. Há qualquer garantia de latência?
Devido à natureza das notificações por push que são entregues por uma plataforma externa específica o Serviço de Notificações por Push, não há nenhuma garantia de latência. Normalmente, a maioria das notificações são entregues em alguns minutos.

###9\. Quais são as considerações precisamos levar em conta com a criação de uma solução com namespaces e Hubs de Notificação?
*Aplicativos móveis/Ambiente:* deve haver um Hub de Notificação conforme os aplicativos móveis por ambiente. Em um cenário de multilocatário - cada locatário deve ter um hub separado. Você nunca deve compartilhar o mesmo Hub de Notificação entre ambientes de teste e produção, visto que isso pode causar problemas futuros durante o envio de notificações. Por exemplo, a Apple oferece pontos de extremidade de área restrita e push de produção, cada qual com credenciais separadas. Se o hub foi originalmente configurado com certificado de área restrita da Apple e depois reconfigurado para usar o certificado de produção da Apple, os tokens de dispositivo antigos se tornariam inválidos com o novo certificado e causariam falhas de envios por push. É melhor separar a produção e ambientes de teste e usar os hubs diferentes para ambientes diferentes.

*Credenciais PNS:* quando um aplicativo móvel é registrado com o portal do desenvolvedor da plataforma (por exemplo, Apple ou Google etc), então você obtém um identificador de aplicativos e tokens de segurança que um back-end do aplicativo precisa fornecer serviços de Notificação por Push da Plataforma para poder enviar notificações por push para os dispositivos. Esses tokens de segurança que podem ser na forma de certificados (por exemplo, para Apple iOS ou Windows Phone) ou chaves de segurança (Google Android, Windows) etc precisam ser configurados em Hubs de Notificação. Isso é normalmente feito no nível do hub de notificação, mas também pode ser feito no nível de namespace em um cenário de multilocatário.

*Namespaces:* namespaces também podem ser usados para o agrupamento de implantação. Também pode ser usado para representar todos os hubs de notificação para todos os locatários do mesmo aplicativo no cenário de multilocatário.

*Distribuição geográfica:* a distribuição geográfica nem sempre é crítica em caso de notificações por push. Deve-se observar as vários serviços de Notificação por push (por exemplo, APNS, GCM etc.) que, por fim, entregam as notificações por push aos dispositivos que não são igualmente distribuídos. No entanto se você tiver um aplicativo que é usado em todo o mundo você pode criar vários hubs em namespaces diferentes aproveitando a disponibilidade do serviço de Hubs de Notificação em diferentes regiões do Azure no mundo todo. Observe que isso aumentará o custo de gerenciamento principalmente em torno de registros, então isso realmente não é recomendado e só deve ser feito se for realmente necessário.

###10\. Devemos fazer registros de back-end do aplicativo ou diretamente dispositivos?
Registros de back-end do aplicativo são úteis quando você precisa fazer uma autenticação de cliente antes de criar o registro ou quando há marcas que devem ser criadas ou modificadas pelo back-end do aplicativo, com base em alguma lógica de aplicativo. Encontre mais orientações aqui - [Diretrizes de registro de back-end] e [Diretrizes de registro de back-end - 2]

###11\. O que é o modelo de segurança?
Os Hubs de Notificação do Azure usam um modelo de segurança baseado em Assinatura de Acesso Compartilhado (SAS). Você pode usar os tokens SAS no nível do namespace raiz ou no nível de hubs de notificação granular. Esses tokens SAS podem ser definidos com diferentes regras de autorização por exemplo, as permissões para enviar mensagens, permissões para ouvir notificação etc. Mais detalhes aqui - [Modelo de segurança NH]

###12\. Como lidar com a carga confidencial nas notificações?
Todas as notificações são entregues aos dispositivos pelos Serviços de Notificações das plataformas (PNS). Quando um remetente enviar uma notificação para os Hubs de Notificação do Azure, processamos e transmitimos a notificação ao respectivo PNS. Todas as conexões do remetente para os Hubs de Notificações do Azure e para o PNS usam HTTPS. Os Hubs de Notificações do Azure não registram a carga da mensagem de forma alguma. Para o envio de cargas confidenciais no entanto, recomendamos que um padrão de Enviar por push seguro em que o remetente envia uma notificação 'ping' com um identificador de mensagem para o dispositivo sem a carga confidencial e quando o aplicativo no dispositivo recebe essa carga, então ele chama uma API de back-end do aplicativo seguro diretamente para buscar os detalhes da mensagem. Tutorial para implementar o padrão está aqui - [NH - Tutorial do Envio por push seguro]

##Operações
###1\. O que é a história de Recuperação de Desastres (DR)?
Fornecemos uma Recuperação de Desastres de metadados aqui (nome do Hub de Notificação, a cadeia de conexão etc). No caso de uma Recuperação de Desastres, os dados de registros são aqueles que serão perdidos, portanto, será necessário encontrar uma solução para preencher eles novamente em seu novo hub.

- *Etapa 1* - Criar um Hub de Notificação secundário em um controlador de domínio diferente. Você pode criá-lo imediatamente no momento do evento de Recuperação de Desastres ou você pode criá-lo do princípio. Não importa muito porque o provisionamento de NH é um processo rápido na ordem de alguns segundos. Tendo uma desde o início também protegerá você do evento de Recuperação de Desastres que afeta nossos recursos de gerenciamento, por isso, é recomendável.

- *Etapa 2* - Recupere o Hub de Notificação secundário com os registros do Hub de Notificação principal. Não é recomendável tentar manter os registros em ambos os hubs e tentar mantê-los em sincronia imediatamente, visto que os registros estão chegando, como não funcionou bem devido à natureza inerente de registros para expirar pelo PNS. Os Hubs de Notificação limpa-os enquanto recebemos comentários dos PNS sobre registros expirados ou inválidos.

Recomenda-se usar um back-end do aplicativo que:

- Mantenha que conjunto de registros no final para que ele possa fazer uma inserção em massa no hub de notificação secundário no caso de DR, OR

- Obtenha um despejo de registros do hub principal como um backup e depois faça uma inserção em massa no NH secundário.

(A funcionalidade de exportação/importação de registros disponível na camada Padrão está descrita aqui: [Importação/exportação de registros])

Se você não tiver um back-end quando o aplicativo é iniciado nos dispositivos, então você fará um novo registro no NH secundário e eventualmente o NH secundário terá todos os dispositivos ativos registrados, mas a desvantagem é que haverá um período de tempo quando os dispositivos onde os aplicativos ainda não abriram não receberão notificações.

###2\. Há algum recurso de log de auditoria?
Todas as operações de gerenciamento de Hubs de notificação vão para Logs de operação que são expostos no Portal de Gerenciamento do Azure.

##Monitoramento e Solução de Problemas
###1\. Quais recursos de solução de problemas estão disponíveis?
Os Hubs de Notificação do Azure fornecem vários recursos para executar solução de problemas comuns principalmente no cenário em torno de notificações removidas. Consulte detalhes neste white paper para solução de problemas - [NH - Solução de problemas]

###2\. Quais recursos de telemetria estão disponíveis?
Os Hubs de Notificação do Azure permitem a exibição de dados de telemetria no portal de gerenciamento do Azure. Detalhes de métricas disponíveis estão disponíveis aqui - [NH - Métricas]. Observe que notificações de sucesso significam apenas que as notificações foram entregues ao Serviço de Notificação por Push externo (por exemplo, APNS para Apple, GCM para Google etc) e então, depende do PNS para entregar a notificação para os dispositivos e o PNS não expõe essas métricas para nós. Ele também fornece o recurso de exportar a telemetria programaticamente (na camada Padrão). Veja este exemplo para obter detalhes - [NH - Exemplo de métricas]

[Preços de Hubs de Notificação]: http://azure.microsoft.com/pricing/details/notification-hubs/
[SLA de Hubs de Notificação]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - APIs REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Tutoriais de Introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Tutorial de aplicativos do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Preços de Serviços Móveis]: http://azure.microsoft.com/pricing/details/mobile-services/
[Diretrizes de registro de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Diretrizes de registro de back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - Tutorial do Envio por push seguro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - Solução de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - Métricas]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - Exemplo de métricas]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Importação/exportação de registros]: https://msdn.microsoft.com/library/dn790624.aspx

<!---HONumber=Oct15_HO3-->