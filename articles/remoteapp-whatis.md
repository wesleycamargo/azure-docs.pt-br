<properties title="What is RemoteApp?" pageTitle="O que é o RemoteApp?" description="Saiba mais sobre o Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#O que é o RemoteApp do Azure?

O RemoteApp do Azure oferece a funcionalidade do programa Microsoft RemoteApp local, com suporte pelos Serviços de Área de Trabalho Remota, para o Azure. O RemoteApp do Azure permite fornecer acesso remoto seguro aos aplicativos de vários dispositivos de usuário diferentes.

Quando você move o RemoteApp para o Azure, você pode aproveitar o armazenamento, a escalabilidade e o alcance global do Azure sem precisar se preocupar sobre uma configuração complexa local. A Microsoft fornece a manutenção do Azure, garantindo sua confiabilidade, liberando você para se concentrar em problemas mais importantes, como a criação de melhores aplicativos para a sua empresa usar. Outra vantagem do RemoteApp do Azure é a acessibilidade - os usuários podem acessar programas do RemoteApp do Windows, iOS, Mac OS X e dispositivos Android. Eles podem usar seus aplicativos no ambiente em que preferirem, enquanto você usa o Portal de Gerenciamento do Azure para gerenciar esses aplicativos. 

Continue lendo para obter mais informações sobre o RemoteApp ou, se já estiver convencido, [experimente hoje](http://azure.microsoft.com/pt-br/services/remoteapp/).

O RemoteApp do Azure faz parte do [Microsoft Virtual Desktop Infrastructure](http://www.microsoft.com/pt-br/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Novo!** Quer saber mais sobre o RemoteApp do Azure? Ou está pronto para validar o RemoteApp em escala? Participe de nosso [webinar semanal "Pergunte aos especialistas"](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Opções de implantação do RemoteApp
Há dois tipos de implantação do RemoteApp:


- Uma **implantação de nuvem** é hospedada nos e armazena todos os dados para programas na nuvem do Azure. Os usuários podem acessar aplicativos fazendo logon com sua conta da Microsoft ou credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.
- Uma **implantação híbrida** é hospedada no e armazena dados na nuvem do Azure, mas também permite que usuários acessem dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos fazendo logon com suas credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure.

###Implantação de nuvem

A [implantação do RemoteApp na nuvem](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-cloud-deployment/) oferece uma maneira de autônoma de hospedar aplicativos na nuvem. Uma implantação de nuvem existe apenas na nuvem do Azure, em vez de conectar-se à sua rede local.

Como parte da visualização do RemoteApp, oferecemos os aplicativos do Office 2013 já instalados e prontos para compartilhar com seus usuários. Se você optar por aproveitar o software disponível, poderá provisionar rapidamente o seu serviço.

Uma vantagem adicional de usar a implantação de nuvem com os aplicativos do Office 2013 é que os aplicativos e o sistema operacional (no qual o serviço é criado) são sempre mantidos atualizados por meio de atualizações regulares, e a proteção a pontos de extremidade antimalware da Microsoft fornece proteção contínua. Os usuários finais usa suas contas da Microsoft ou credenciais corporativas para acessar os aplicativos. É tudo o que você, o administrador, precisa se preocupar sobre descobrir quem deve ter acesso a quais aplicativos.

Você também pode criar uma implantação de nuvem para compartilhar um aplicativo personalizado ou um conjunto de aplicativos para seus usuários. Para fazer isso, você precisa [criar uma imagem do modelo personalizada](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/) (que é como podemos publicar aplicativos para o RemoteApp) e simplesmente escolher essa imagem (em vez da imagem do Office 2013) ao criar sua implantação. 

###Implantação híbrida
A [implantação híbrida do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-hybrid-deployment/) permite fornecer um conjunto personalizado de aplicativos para seus usuários e acesso a dados e recursos em sua rede local. Ao contrário de uma imagem personalizada usada com a implantação de nuvem, a imagem que você cria para uma implantação híbrida executa aplicativos em um ambiente de domínio, concedendo acesso completo à sua rede local e dados.

Com a integração do Active Directory com o Active Directory do Azure (usando o DirSync), seus usuários podem usar suas credenciais corporativas para acessar aplicativos e dados. Quando você usa uma conta de trabalho no Active Directory, você pode levar suas políticas corporativas em nuvem para controlar os aplicativos que você oferece por meio do RemoteApp.

Desde que você crie sua imagem do modelo no Windows Server 2012 R2 com o serviço de função Host da Sessão RD, existem alguns limites sobre os aplicativos que você pode publicar para seus usuários. Se os aplicativos funcionam corretamente no ambiente de imagem desse modelo, os usuários finais podem acessá-los por meio do RemoteApp. 

###Atualizando sua implantação
Uma das principais diferenças entre as implantações de nuvem e híbrida é como as atualizações de software são tratadas. Com uma implantação de nuvem que usa a imagem pré-instalada do Office 2013, você não precisa se preocupar sobre todas as atualizações. O serviço se mantém e distribui atualizações de forma contínua, para aplicativos e o sistema operacional.

Para implantações híbridas, bem como implantações de nuvem que usam uma imagem de modelo personalizada, você é responsável por manter a imagem e os aplicativos. Para imagens de domínio, você pode controlar atualizações usando ferramentas como o Windows Update, a Política de Grupo ou o System Center.

Depois de atualizar sua imagem de modelo personalizada, carregue a nova imagem na nuvem do Azure e atualize a implantação para usar a nova imagem. (Você pode fazer isso da página Início Rápido do RemoteApp ou do Painel.)

##Clientes com suporte do RemoteApp
Como parte da versão de visualização do RemoteApp do Azure, lançamos um novo aplicativo cliente Microsoft RemoteApp para Windows e Windows RT, bem como atualizações para os aplicativos de área de trabalho remota da Microsoft para iOS e Android. Os usuários podem usar esses aplicativos em seus dispositivos móveis ou de computação para acessar os novos programas RemoteApp.

##Próximas etapas
Acesse! Experimente! Estes artigos ajudam a começar com o RemoteApp:

- [Como criar uma imagem do modelo personalizada para o RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/)
- [Como criar uma implantação de nuvem do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-cloud-deployment/)
- [Como criar uma implantação híbrida do RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-hybrid-deployment/)
