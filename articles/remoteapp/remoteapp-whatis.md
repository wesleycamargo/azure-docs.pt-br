<properties 
	pageTitle="O que é o Azure RemoteApp? | Microsoft Azure" 
	description="Saiba mais sobre o Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="09/28/2015" 
	ms.author="elizapo"/>

# O que é o RemoteApp do Azure?

O RemoteApp do Azure oferece a funcionalidade do programa RemoteApp Microsoft local, o suporte pelos Serviços de área de trabalho remota, para o Azure. O RemoteApp do Azure o ajudará a fornecer acesso remoto seguro aos aplicativos de vários dispositivos de usuário diferentes.

Com o Azure RemoteApp, você pode compartilhar aplicativos e recursos com usuários em praticamente qualquer dispositivo. Hospedamos seus aplicativos na nuvem, o que significa que cuidamos do hardware e da escala para atender às demandas dos usuários. Tudo o que você precisa fazer é carregar os aplicativos que deseja compartilhar e fazer com que seus usuários usem esses aplicativos. [Os usuários mantêm seus próprios dispositivos](remoteapp-clients.md), enquanto você gerencia tudo por meio do portal do Azure. Você tem até a opção de usar suas credenciais corporativas, permitindo garantir a segurança de aplicativos e dados.

Continue lendo para obter mais informações sobre o RemoteApp ou, se nós já o convencemos, [experimente-o agora](http://azure.microsoft.com/services/remoteapp/).

Tem dúvidas sobre o Azure RemoteApp? Confira nossas [Perguntas frequentes](remoteapp-faq.md).

O RemoteApp do Azure é parte do [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Quer saber mais sobre o RemoteApp do Azure? Ou está pronto para validar o RemoteApp em escala? Participe do nosso webinar semanal [Pergunte aos especialistas](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Coleções do RemoteApp
Há dois tipos de coleções do [Azure RemoteApp](remoteapp-collections.md):


- Uma **coleção na nuvem** é hospedada e armazena dados dos programas na nuvem. Os usuários podem acessar aplicativos ao efetuar logon com sua conta da Microsoft ou credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.

	Escolha uma coleção de nuvem quando o aplicativo que você deseja compartilhar não requer uma conexão com nenhum recurso de rede privada da empresa (por exemplo, por meio de um dispositivo VPN). Se o aplicativo usa recursos na Internet, o OneDrive ou o Azure, uma coleção de nuvem funcionará para você. Além disso, ela é mais rápida de criar.

- Uma **coleção híbrida** é hospedada e armazena os dados na nuvem do Azure, mas também permite aos usuários acessarem dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos ao efetuar logon com suas credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.

	Escolha uma coleção híbrida se você precisar de uma conexão com recursos na rede privada da empresa. Por exemplo, se o aplicativo precisa acessar um dos seguintes:

	- Servidores de arquivos localizados em sua intranet
	- Quicken
	- Bancos de dados por trás de um firewall

	Isso geralmente é mais útil para empresas de grande porte com muitos recursos em suas redes privadas que não podem ser movidos para a nuvem.

As diferentes coleções têm opções diferentes, incluindo redes, por isso, descubra [qual coleção](remoteapp-collections.md) funciona melhor para você.


### Atualizando sua coleção
Uma das principais diferenças entre as coleções híbrida e na nuvem é como as atualizações de software são tratadas. Com uma coleção na nuvem que usa a imagem pré-instalada do Office 365 ProPlus ou Office 2013, você não precisa se preocupar sobre qualquer atualização. O serviço se mantém e distribui atualizações de forma contínua, tanto para aplicativos quanto o sistema operacional.

Para coleções híbridas, bem como coleções na nuvem que usam uma imagem de modelo personalizada, você é responsável por manter a imagem e os aplicativos. Para imagens de domínio, você pode controlar atualizações usando ferramentas como o Windows Update, a Diretiva de grupo ou o System Center.

Depois de atualizar sua imagem de modelo personalizada, você carrega a nova imagem na nuvem do Azure e, em seguida, atualiza a coleção para usar a nova imagem. (Você pode fazer isso na página **Início Rápido** do RemoteApp ou no Painel.)

Veja [Atualizar sua coleção](remoteapp-update.md) para obter mais informações.

## Clientes com suporte do RemoteApp
O RemoteApp do Azure tem suporte em aplicativos cliente RemoteApp para Windows e Windows RT, bem como os aplicativos de área de trabalho remota Microsoft para Mac, iOS e Android. Os usuários podem usar esses aplicativos em seus dispositivos móveis para acessar os novos programas do RemoteApp.

Veja [Acessando seus aplicativos no Azure RemoteApp](remoteapp-clients.md) para obter mais informações sobre os clientes.

## Próximas etapas
Vá! Experimente! Esses artigos ajudam a começar com o RemoteApp:

- [Que tipo de coleção é necessária para o Azure RemoteApp?](remoteapp-collections.md)
- [Criar uma imagem do RemoteApp](remoteapp-imageoptions.md)
- [Como criar uma coleção na nuvem do RemoteApp](remoteapp-create-cloud-deployment.md)
- [Como criar uma coleção híbrida do RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Como o licenciamento funciona no RemoteApp?](remoteapp-licensing.md)
- [Práticas recomendadas para usar o RemoteApp do Azure](remoteapp-bestpractices.md)
- [Perguntas frequentes sobre o RemoteApp do Azure](remoteapp-faq.md)
 

<!---HONumber=Oct15_HO1-->