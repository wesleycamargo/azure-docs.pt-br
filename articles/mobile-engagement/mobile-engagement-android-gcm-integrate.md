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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Como integrar o GCM ao Mobile Engagement

> [AZURE.IMPORTANT]Você deve seguir o procedimento de integração descrito no documento Como Integrar o Engagement no Android, antes de seguir este guia.
>
> Este documento é útil apenas se você integrou o módulo de Alcance para sempre dar suporte à campanha. Para integrar campanhas de Alcance em seu aplicativo, leia primeiro Como Integrar o Engagement Reach no Android.

##Introdução

A integração do GCM permite que seu aplicativo seja enviado mesmo quando não está em execução.

Nenhum dado de campanha é realmente enviado por meio do GCM, é apenas um sinal em segundo plano informando ao aplicativo para buscar o push do Engagement. Se o aplicativo não está em execução ao receber um push GCM, ele dispara uma conexão com os servidores do Engagement para buscar o envio por push, a conexão com o Engagement permanece ativa por cerca de um minuto caso o usuário inicie o aplicativo em resposta ao envio.

Para sua informação, o Engagement usa somente mensagens [Enviar para sincronização] com a chave de recolhimento `engagement.tickle`.

> [AZURE.IMPORTANT]Somente os dispositivos que executam o Android 2.2 ou acima, tendo o Google Play instalado e tendo conexão de segundo plano do Google habilitada podem ser despertados pelo GCM; no entanto, você pode integrar esse código com segurança em versões mais antigas do SDK do Android e em dispositivos que não dão suporte a GCM (ele apenas utiliza intenções). Se o aplicativo não pode ser ativado pelo GCM, a notificação do Engagement será recebida na próxima vez que o aplicativo for iniciado.


> [AZURE.WARNING]Se seu próprio código de cliente gerencia os identificadores de registro C2DM enquanto o SDK do Engagement é configurado para usar GCM, ocorre um conflito nos identificadores de registro; use o GCM em Engagement somente se o seu próprio código não usar C2DM.

##Inscrever-se para o GCM e habilitar o serviço GCM

Se não tiver feito isso ainda, você deve habilitar o serviço GCM em sua conta do Google.

No momento da gravação deste documento (5 de fevereiro de 2014), você pode seguir o procedimento em: [< http://developer.android.com/guide/google/gcm/gs.html>].

Siga esse procedimento apenas para habilitar o GCM em sua conta. Quando você chegar na seção **Obter uma Chave de API** não leia a mesma e volte a esta página em vez de continuar o procedimento do Google.

O procedimento explica que o **Número do Projeto** é usado como o **ID de remetente do GCM**; você precisará do mesmo mais tarde neste procedimento.

> [AZURE.IMPORTANT]O **Número do Projeto** não deve ser confundido com a **ID do Projeto**. A ID do projeto agora pode ser diferente (é um nome em novos projetos). O que você precisa para integrar no SDK do Engagement é o **Número do Projeto** e o mesmo é exibido no menu **Visão geral** no [Console de Desenvolvedor do Google].

##Integração do SDK

### Gerenciando registros de dispositivo

Cada dispositivo deve enviar um comando de registro aos servidores do Google, caso contrário eles não poderão ser alcançados.

Um dispositivo também pode cancelar o registro de notificações do GCM (o registro do dispositivo será cancelado automaticamente se o aplicativo for desinstalado).

Se você usa a [Biblioteca de cliente GCM], pode ler diretamente o android-sdk-gcm-receive.

Se você ainda não enviou a intenção de registro por conta própria, você pode fazer com que o Engagement registre o dispositivo automaticamente para você.

Para habilitar isso, adicione o seguinte ao seu arquivo `AndroidManifest.xml` dentro da marca`<application/>`:

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Comunicar a ID de registro para o serviço de envio por push do Engagement e receber notificações

Para comunicar a ID de registro do dispositivo para o serviço de envio por push do Engagement e receber notificações, adicione o seguinte ao seu arquivo `AndroidManifest.xml` dentro da marca `<application/>` (mesmo que você gerencie registros de dispositivo por conta própria):

			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
			    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Certifique-se de ter as seguintes permissões em seu `AndroidManifest.xml` (após a marca `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Conceder acesso ao Engagement para uma chave de API de servidor

Caso não tenha feito, crie uma **Chave de API de Servidor** no [Console de Desenvolvedor do Google].

A chave do servidor **NÃO deve ter restrição de IP**.

No momento da gravação deste documento (5 de fevereiro de 2014), o procedimento é o seguinte:

-   Para fazer isso, abra o [Console de Desenvolvedor do Google].
-   Selecione o mesmo projeto que anteriormente no procedimento (aquele com o **Número do Projeto** integrado por você em `AndroidManifest.xml`).
-   Vá para APIs & auth-\\ > Credenciais, clique em "CRIAR NOVA CHAVE" na seção "Acesso a API pública".
-   Selecione "Chave do servidor".
-   Na tela seguinte, deixe em branco **(nenhuma restrição de IP)**, em seguida, clique em Criar.
-   Copie a **chave de API** gerada.
-   Vá para $/\\#application/YOUR\\_ENGAGEMENT\\_APPID/native-push.
-   Na seção GCM, edite a chave de API com aquela que você acabou de gerar e copiar.

Agora você será capaz de selecionar "Qualquer hora" durante a criação de pesquisas e anúncios do Reach.

> [AZURE.IMPORTANT]O Engagement precisa na verdade de uma **Chave de Servidor**. Uma chave Android não pode ser usada pelos servidores do Engagement.

##Teste

Agora, verifique sua integração lendo Como testar a integração do Engagement em Android.


[Enviar para sincronização]: http://developer.android.com/google/gcm/adv.html#collapsible
[< http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[Biblioteca de cliente GCM]: http://developer.android.com/guide/google/gcm/gs.html#libs
[Console de Desenvolvedor do Google]: https://cloud.google.com/console

 

<!---HONumber=Oct15_HO3-->