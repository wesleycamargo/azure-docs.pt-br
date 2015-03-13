<properties 
	pageTitle="O que é o RemoteApp?" 
	description="Saiba mais sobre o Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/17/2015" 
	ms.author="elizapo"/>

#O que é o RemoteApp do Azure?
 
O RemoteApp do Azure oferece a funcionalidade do programa RemoteApp Microsoft local, o suporte pelos Serviços de área de trabalho remota, para o Azure. O RemoteApp do Azure o ajudará a fornecer acesso remoto seguro aos aplicativos de vários dispositivos de usuário diferentes.

Quando você mover o RemoteApp para o Azure, você pode aproveitar o armazenamento, a escalabilidade e o alcance global do Azure sem precisar se preocupar sobre uma configuração complexa no local. A Microsoft fornece a manutenção do Azure, garantindo sua confiabilidade, liberando você para se concentrar em problemas mais importantes, como a criação de melhores aplicativos para a sua empresa usar. Outra vantagem do RemoteApp do Azure é a acessibilidade - os usuários podem acessar programas RemoteApp a partir de dispositivos iOS, Windows, Mac OS X e Android. Eles podem usar seus aplicativos no ambiente que preferirem, enquanto você usa o portal de gerenciamento do Azure para gerenciar esses aplicativos. 

Continue lendo para obter mais informações sobre o RemoteApp, ou, se nós já o convencemos, [experimente-o agora](http://azure.microsoft.com/services/remoteapp/).

Tem dúvidas sobre o Azure RemoteApp? Confira nossas [Perguntas mais frequentes](http://azure.microsoft.com/documentation/articles/remoteapp-faq/).

O RemoteApp Azure é parte do [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/pt-br/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Deseja saber mais sobre o RemoteApp do Azure? Ou esta pronto para validar o RemoteApp em escala? Ingresse em nosso [webinar semanal de perguntas aos especialistas](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Opções de implantação do RemoteApp
Há dois tipos de coleções de RemoteApp:


- Uma **coleção na nuvem** é hospedada e armazena todos os dados para os programas na nuvem do Azure. Os usuários podem acessar aplicativos ao efetuar logon com sua conta da Microsoft ou credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.
- Uma **coleção híbrida** é hospedada e armazena os dados na nuvem do Azure, mas também permite aos usuários acessarem dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos ao efetuar logon com suas credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.

###Coleção na nuvem

A [coleção de RemoteApp na nuvem](http://azure.microsoft.com/documentation/articles/remoteapp-create-cloud-deployment/) oferece uma maneira independente para hospedar aplicativos na nuvem. Uma coleção na nuvem existe apenas na nuvem do Azure, em vez de se conectar à sua rede local.

Como parte da avaliação do RemoteApp, oferecemos os aplicativos do Office 365 ProPlus ou Office 2013 já instalados e prontos para compartilhar com seus usuários. Se você optar por utilizar o software disponível, você pode provisionar rapidamente o seu serviço.

Uma vantagem adicional de usar a coleção na nuvem com os aplicativos do Office é que os aplicativos e o sistema operacional (no qual o serviço é criado) são sempre mantidos atualizados por meio de atualizações regulares e a proteção no ponto de extremidade do Microsoft Antimalware fornece proteção contínua. Os usuários finais usam suas contas da Microsoft ou credenciais corporativas para acessar os aplicativos. É tudo que você, o administrador, precisa se preocupar sobre descobrir quem deve ter acesso a quais aplicativos.

Você também pode criar uma coleção na nuvem para compartilhar um aplicativo personalizado ou um conjunto de aplicativos para seus usuários. Para fazer isso, você precisa [criar uma imagem de modelo personalizada](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/) (que é como podemos publicar aplicativos para o RemoteApp) e simplesmente escolher essa imagem (em vez da imagem do Office 2013) ao criar sua coleção. 

###Coleção híbrida
O [coleção híbrida do RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-hybrid-deployment/) permite que você forneça um conjunto personalizado de aplicativos para seus usuários e acesso a dados e recursos em sua rede local. Ao contrário de uma imagem personalizada usada com a coleção na nuvem, a imagem que você cria para uma coleção híbrida executa aplicativos em um ambiente de domínio, concedendo acesso total à sua rede e dados locais.

Com a integração do Active Directory com o Active Directory do Azure (usando o DirSync), seus usuários podem usar suas credenciais corporativas para acesso aos aplicativos e dados. Quando você usa uma conta de trabalho no Active Directory, você pode levar suas políticas corporativas para a nuvem para controlar os aplicativos que você oferece por meio do RemoteApp.

Assim que você criar sua imagem de modelo no Windows Server 2012 R2 com o serviço de função de Host de sessão de área de trabalho remota, existem alguns limites sobre os aplicativos que você pode publicar para seus usuários. Se os aplicativos funcionarem corretamente no ambiente de imagem desse modelo, os usuários finais podem acessá-los por meio do RemoteApp. 

###Atualizando sua coleção
Uma das principais diferenças entre as coleções híbrida e na nuvem é como as atualizações de software são tratadas. Com uma coleção na nuvem que usa a imagem pré-instalada do Office 365 ProPlus ou Office 2013, você não precisa se preocupar sobre qualquer atualização. O serviço se mantém e distribui atualizações de forma contínua, tanto para aplicativos quanto o sistema operacional.

Para coleções híbridas, bem como coleções na nuvem que usam uma imagem de modelo personalizada, você é responsável por manter a imagem e os aplicativos. Para imagens de domínio, você pode controlar atualizações usando ferramentas como o Windows Update, a Diretiva de grupo ou o System Center.

Depois de atualizar sua imagem de modelo personalizada, você carrega a nova imagem na nuvem do Azure e, em seguida, atualiza a coleção para usar a nova imagem. (Você pode fazer isso a partir da página de Início rápido do RemoteApp ou do Painel de controle.)

##Clientes com suporte do RemoteApp
O RemoteApp do Azure tem suporte em aplicativos cliente RemoteApp para Windows e Windows RT, bem como os aplicativos de área de trabalho remota Microsoft para Mac, iOS e Android. Os usuários podem usar esses aplicativos em seus dispositivos móveis para acessar os novos programas do RemoteApp.

##Próximas etapas
Vá! Experimente! Esses artigos ajudam a começar com o RemoteApp:

- [Como criar uma imagem do modelo personalizada para o RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/)
- [Como criar uma coleção na nuvem do RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-cloud-deployment/)
- [Como criar uma coleção híbrida do RemoteApp](http://azure.microsoft.com/documentation/articles/remoteapp-create-hybrid-deployment/)
- [Como o licenciamento funciona no RemoteApp?](http://azure.microsoft.com/documentation/articles/remoteapp-licensing/)
- [Práticas recomendadas para usar o RemoteApp do Azure](http://azure.microsoft.com/documentation/articles/remoteapp-bestpractices/)
- [Perguntas frequentes sobre o RemoteApp do Azure](http://azure.microsoft.com/documentation/articles/remoteapp-faq/)

<!--HONumber=35.2-->

<!--HONumber=46--> 
