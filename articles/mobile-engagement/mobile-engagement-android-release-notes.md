<properties
	pageTitle="Integração do SDK do Android do Azure Mobile Engagement"
	description="Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="piyushjo" />


#Notas de versão

##4\.2.2 (17/05/2016)

- Aprimoramentos de estabilidade.

##4\.2.1 (10/05/2016)

- Segurança: desabilite o acesso de arquivo local de exibição na Web.
- Segurança: remova a classe `EngagementPreferenceActivity` que estende a classe `PreferenceActivity` obsoleta e não segura.
- Segurança: atividades de alcance agora estão documentadas para usar `exported="false"`; esse sinalizador também pode ser usado em versões anteriores do SDK.

##4\.2.0 (03/11/2016)

- O SDK está licenciado sob MIT.
- Permita a especificação de um identificador de dispositivo personalizado no momento de inicialização do SDK.

##4\.1.5 (01/02/2016)

- Aprimoramentos de estabilidade.

##4\.1.4 (26/01/2016)

- Aprimoramentos de estabilidade.

##4\.1.3 (09/12/2015)

- Aprimoramentos de estabilidade.

##4\.1.2 (25/11/2015)

- Aprimoramentos de estabilidade.

##4\.1.1 (04/11/2015)

- Aprimoramentos de estabilidade.

##4\.1.0 (25/08/2015)

- Lidar com o novo modelo de permissão para Android M.
- Agora é possível configurar os recursos de localização no tempo de execução em vez de usar o `AndroidManifest.xml`.
- Corrija um bug de permissão: se você usar `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` não será mais necessário.
- Aprimoramentos de estabilidade.

##4\.0.0 (07/06/2015)

-   Alterações de protocolo interno para tornar a análise e o envio mais confiáveis.
-   O push nativo (GCM/ADM) agora também é usado nas notificações de aplicativo para que você deve configurar as credenciais por push nativo para qualquer tipo de campanha de envio.
-   Corrigir a notificação de visão geral: foram exibidas somente 10s depois de enviadas.
-   Corrigir um bug no modo de exibição da web: ao clicar em um link também estava sendo executada a URL da ação padrão.
-   Corrigir uma falha rara relacionada ao gerenciamento de armazenamento local.
-   Corrigir o gerenciamento de cadeia de caracteres de configuração dinâmica.
-   Atualizar EULA.

##3\.0.0 (17/02/2015)

-   Versão Inicial do Mobile Engagement do Azure
-   A configuração do appId é substituída por uma configuração de cadeia de conexão.
-   Removida a API para enviar e receber mensagens XMPP arbitrárias de entidades XMPP arbitrárias.
-   Removida a API para enviar e receber mensagens entre dispositivos.
-   Aprimoramentos de segurança.
-   Acompanhamento do Google Play e SmartAd removido.

<!---HONumber=AcomDC_0518_2016-->