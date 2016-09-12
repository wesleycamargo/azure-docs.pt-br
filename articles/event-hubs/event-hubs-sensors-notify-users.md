<properties 
   pageTitle="Notificar os usuários de dados recebidos de sensores ou outros sistemas | Microsoft Azure"
   description="Descreve como usar os Hubs de Eventos para notificar os usuários sobre dados de sensor."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# Notificar os usuários de dados recebidos de sensores ou outros sistemas

Suponha que você tenha um aplicativo que monitore os dados em tempo real ou produza relatórios em uma agenda. Se você olhar para o site no qual os gráficos em tempo real ou relatórios são exibidos, poderá ver algo que requer uma ação. E se você precisar ser alertado para essas situações, em vez de depender de lembrar-se de consultar o site? Imagine que você tem uma luz de crescimento em uma estufa, e precisa saber imediatamente se a luz apagar. Uma maneira de fazer isso seria com um sensor de luz na estufa configurado para enviar um email no caso de a luz apagar.

![][1]

Em outro cenário, imagine que você executa uma instalação de embarque animais de estimação e precisa ser alertado sobre níveis de fornecimento de estoque baixo. Por exemplo, você pode providenciar o envio de uma mensagem de texto do sistema ERP se o estoque do armazém de ração para cães cair para um nível crítico.

![][2]

O problema é como obter informações críticas quando determinadas condições são atendidas, e não quando você realiza a verificação de um relatório estático. Se você estiver usando um [Hub de Eventos do Azure][] ou o [Hub IoT do Azure][] para receber dados de dispositivos ou aplicativos corporativos, como [Dynamics AX][], há várias opções para processá-los. Você pode exibi-los em um site, analisá-los, armazená-los e usá-los para disparar comandos para fazer algo. Para fazer isso, você pode usar ferramentas avançadas como [Sites do Azure][], [SQL Azure][], [HDInsight][], [Cortana Intelligence Suite][], [IoT Suite][], [Aplicativos Lógicos][] ou [Hubs de Notificação do Azure][]. Mas, às vezes, tudo o que você deseja fazer é enviar esses dados para alguém com um mínimo de sobrecarga. Para mostrar como fazer isso com apenas um pouco de código, apresentamos uma nova amostra, [AppToNotifyUsers][]. As opções incluídas são email (SMTP), SMS e telefone.

## Estrutura de aplicativo

O aplicativo é escrito em C# e o arquivo Leiame no exemplo contém todas as informações necessárias para modificar, criar e publicar o aplicativo. As seções a seguir fornecem uma visão geral do que o aplicativo faz.

Vamos começar com a suposição de que você tenha eventos críticos sendo enviados por push a um Hub de eventos do Azure ou ao Hub IoT. Qualquer hub serve, desde que você tenha acesso a ele e conheça a cadeia de conexão.

Se você ainda não tiver um Hub IoT ou um Hub de Eventos, você pode configurar facilmente um ambiente de teste com um escudo Arduino e um Raspberry Pi seguindo as instruções no projeto [Conectar os Pontos](https://github.com/Azure/connectthedots). O sensor de luz no escudo Arduino envia os níveis de luz por meio de Pi para um [Hub de Eventos do Azure][] (**ehdevices**) e um trabalho do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) envia alertas para um segundo hub de eventos (**ehalerts**) se os níveis de luz recebidos caírem abaixo de um determinado nível.

Quando **AppToNotify** é iniciado, ele lê um arquivo de configuração (App.config) para obter a URL e as credenciais para o Hub de Eventos que está recebendo os alertas. Em seguida, ele gera um processo para monitorar continuamente esse Hub de Eventos para qualquer mensagem que chegue – desde que você tenha acesso à URL para o Hub de Eventos ou Hub IoT e credenciais válidas, esse código de leitor de Hubs de eventos lerá continuamente o que chega. Durante a inicialização, o aplicativo também lê a URL e credenciais para o serviço de mensagens (email, SMS, telefone) que você deseja usar e o nome/endereço do remetente e uma lista de destinatários.

Depois que o monitor de Hub de Eventos detecta uma mensagem, ele dispara um processo que envia a mensagem usando o método especificado no arquivo de configuração. Observe que ele envia todas as mensagens que detecta. Se você definir o monitor para apontar para um Hub de eventos que recebe dez mensagens por segundo, o remetente enviará dez mensagens por segundo – dez emails por segundo, mensagens SMS de dez por segundo, dez chamadas telefônicas por segundo. Por esse motivo, certifique-se de monitorar um Hub de Eventos que receba apenas os alertas que precisam ser enviados, não um Hub de Eventos que receba os dados brutos dos seus aplicativos ou sensores.

## Aplicabilidade

O código neste exemplo apenas mostra como monitorar os Hubs de Eventos e como chamar serviços de mensagens externos no evento em que você deseja adicionar essa funcionalidade ao seu aplicativo. Observe que essa solução é apenas um exemplo DIY, focado no desenvolvedor. Ele não aborda requisitos corporativos como redundância, failover, reiniciar após a falha, etc. Para obter mais soluções de produção e mais abrangentes, consulte o seguinte:

- Usando conectores ou notificações por push usando o serviço de [Aplicativos Lógicos do Azure](../app-service-logic/app-service-logic-connectors-list.md).
- Usando [Hubs de Notificação do Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), conforme descrito o blog [Transmitir notificações de envio por push a milhões de dispositivos móveis usando os Hubs de Notificação do Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs).

## Próximas etapas

É fácil criar um serviço simples de notificação que envie emails ou mensagens de texto para os destinatários, ou faça ligações telefônicas a eles, para retransmitir dados recebidos por um Hub de Eventos ou Hub IoT. Para implantar a solução para notificar usuários com base nos dados recebidos por esses hubs, visite [AppToNotifyUsers][].

Para obter mais informações sobre esses hubs, consulte os seguintes artigos:

- [Hubs de eventos do Azure]
- [Hub IoT do Azure]
- Introdução com um [Tutorial de Hubs de Evento].
- Um [aplicativo de exemplo completo que usa os Hubs de Evento].
- Uma [solução de mensagens na fila] usando filas do Barramento de Serviço.

Para implantar a solução para notificar usuários com base nos dados recebidos por esses hubs, visite:

- [AppToNotifyUsers][]

[Tutorial de Hubs de Evento]: event-hubs-csharp-ephcs-getstarted.md
[Hub IoT do Azure]: https://azure.microsoft.com/services/iot-hub/
[Hubs de eventos do Azure]: https://azure.microsoft.com/services/event-hubs/
[Hub de Eventos do Azure]: https://azure.microsoft.com/services/event-hubs/
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solução de mensagens na fila]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sites do Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Intelligence Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Aplicativos Lógicos]: https://azure.microsoft.com/services/app-service/logic/
[Hubs de Notificação do Azure]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_0831_2016-->