<properties
	pageTitle="Notas de versão do SDK do iOS no Mobile Engagement do Azure"
	description="Atualizações e procedimentos mais recentes para o SDK do iOS para Mobile Engagement do Azure"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="MehrdadMzfr" />

#Notas de versão

##3\.2.3 (07/06/2016)

-   Foi corrigido o bug em que comentários de entrega não eram registrados quando o aplicativo estava em segundo plano.
-   Envio de logs técnicos otimizado.

##3\.2.2 (07/04/2016)

-   Correção de um bug no cancelamento da solicitação HTTP que, por vezes, leva a uma falha.

##3\.2.1 (11/12/2015)

-   Corrigido o atraso quando uma nova instância do aplicativo é disparada por uma notificação com links profundos

##3\.2.0 (10/08/2015)

-   Bitcode no SDK habilitado para fazê-lo funcionar com **Xcode 7**.
-   Bugs corrigidos relacionados a notificações no aplicativo.
-   Feitas notificações no aplicativo mais confiáveis no caso de pouca bateria e outros cenários.
-   Removidos logs do console extra gerados pela biblioteca de terceiros.

##3\.1.0 (26/08/2015)

-   Corrija o bug de compatibilidade do iOS 9 com uma biblioteca de terceiros. Ele estava causando falhas ao enviar resultados de pesquisas, informações do aplicativo ou dados adicionais.

##3\.0.0 (06/19/2015)

-   O Mobile Engagement usa notificações por push silenciosas.
-   Suporte removido para iOS 4.X. A partir de esta versão, o destino da implantação do seu aplicativo deve ter pelo menos o iOS 6.

##2\.2.0 (05/21/2015)

-   A ID do dispositivo Mobile Engagement para dispositivos < iOS 6 agora se baseia em um GUID gerado no momento da instalação

##2\.1.0 (24/04/2015)

-   Compatibilidade com Swift adicionada.
-   Ao clicar em uma notificação, a URL de ação agora é executada logo após o aplicativo ser aberto.
-   Arquivo de cabeçalho ausente adicionado no pacote SDK.
-   Corrigiu um problema quando o gerador de relatórios de falha do Mobile Engagement foi desabilitado.

##2\.0.0 (17/02/2015)

-   Versão Inicial do Mobile Engagement do Azure
-   A configuração appId/sdkKey é substituída por uma configuração de cadeia de conexão.
-   Removida a API para enviar e receber mensagens XMPP arbitrárias de entidades XMPP arbitrárias.
-   Removida a API para enviar e receber mensagens entre dispositivos.
-   Aprimoramentos de segurança.
-   Controle SmartAd removido.

<!---HONumber=AcomDC_0608_2016-->