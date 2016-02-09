<properties 
	pageTitle="Como criar uma coleção na nuvem do Azure RemoteApp | Microsoft Azure" 
	description="Saiba como criar uma implantação de nuvem do RemoteApp do Azure que salva dados na nuvem do Azure." 
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
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="elizapo"/>

# Como criar uma coleção na nuvem do Azure RemoteApp

Há dois tipos de coleções do [Azure RemoteApp](remoteapp-collections.md):

- Nuvem: reside completamente no Azure. Você pode optar por salvar todos os dados na nuvem (para uma coleção somente da nuvem) ou conectar sua coleção a uma VNET e salvar os dados lá.   
- Híbrido: inclui uma rede virtual para acesso local - isso requer o uso do Azure AD e um ambiente do Active Directory local.

Este tutorial explica o processo de criação de uma implantação de nuvem. Há quatro etapas:

1.	Criar uma coleção do Azure RemoteApp
2.	Opcionalmente, configure a sincronização do diretório. Se você estiver usando o AD do Azure + Active Directory, precisa sincronizar usuários, contatos e senhas por meio do Active Directory local com o locatário do AD do Azure.
5.	Publicar aplicativos.
6.	Configurar o acesso do usuário.


**Antes de começar**

Você precisa fazer o seguinte antes de criar a coleção:

- [Inscreva-se](https://azure.microsoft.com/services/remoteapp/) no RemoteApp do Azure. 
- Colete informações sobre os usuários aos quais deseja conceder acesso. Podem ser informações da conta da Microsoft ou da conta corporativa do Active Directory para usuários ou grupos.
- Este procedimento pressupõe que usará ou uma das imagens de modelo fornecidas como parte de sua assinatura ou que você já carregou a imagem do modelo que deseja usar. Se desejar fazer o upload da imagem do modelo, é possível fazer isso na página Imagens do modelo. Apenas clique em **Fazer o upload de uma imagem do modelo** e siga as etapas do assistente. 
- Deseja usar a imagem do Office 365 ProPlus? Verifique as informações [aqui](remoteapp-officesubscription.md).
- Deseja fornecer aplicativos personalizados ou programas LOB? Crie uma nova [imagem](remoteapp-imageoptions.md) e use-a em sua coleção de nuvem.
- Descubra se você precisa conectar-se a uma VNET. Se você escolher conectar-se a uma VNET, verifique se ela atende às [diretrizes de dimensionamento](remoteapp-vnetsizing.md) e se [pode se conectar ao RemoteApp](remoteapp-vnet.md). Confira o [artigo sobre planejamento da VNET](remoteapp-planvnet.md) para saber mais.
- Se você estiver usando uma VNET, decida se deseja colocá-la em seu domínio do Active Directory local.

## Etapa 1: criar uma coleção na nuvem, com ou sem uma VNET##


Use as etapas a seguir para **criar uma coleção somente na nuvem**:

1. No portal de gerenciamento, vá para a página do RemoteApp.
2. Clique em **Novo > Criação Rápida**.
3. Digite um nome para a coleção e selecione sua região.
4. Escolha o plano que você deseja usar - standard ou basic.
5. Escolha o modelo a ser usado para esta coleção. 

	**Dica:** sua assinatura do RemoteApp vem com [imagens de modelo](remoteapp-images.md) que contêm os programas Office 365 ou Office 2013 (para uso de avaliação), alguns publicados (como o Word) e outros prontos para publicar. Você também pode criar uma nova [imagem](remoteapp-imageoptions.md) e usá-la em sua coleção de nuvem.


1. Clique em **Criar coleção de RemoteApp**.
	
	**Importante:** pode levar até 30 minutos para provisionar sua coleção.

Depois de criar sua coleção do Azure RemoteApp, clique duas vezes no nome da coleção. Isso abrirá a página **Início Rápido**, na qual você terminará de configurar a coleção.

Use as seguintes etapas para criar uma **coleção VNET + nuvem**:

1. No Portal de gerenciamento, vá para a página RemoteApp do Azure.
2. Clique em **Novo** > **Criar com VNET**.
3. Digite um nome para a sua coleção.
4. Escolha o plano que você deseja usar - standard ou basic.
5. Escolha a VNET que você já criou. Não sabe como fazer isso? Por enquanto, as etapas estão no tópico [híbrido](remoteapp-create-hybrid-deployment.md).
6. Decida se você deseja associar sua coleção ao seu domínio. Em caso afirmativo, você precisará usar a AD Connect para integrar o Azure AD e o ambiente do Active Directory. Isso será explicado abaixo na **Etapa 2**.
6. Clique em **Criar coleção de RemoteApp**.


## Etapa 2: Configurar a sincronização de diretório do Active Directory (opcional) ##

Para usar o Active Directory, o Azure RemoteApp exige uma sincronização de diretório entre o Active Directory do Azure e o Active Directory local para sincronizar usuários, grupos, contatos e senhas com o locatário do Active Directory do Azure. Consulte [Configurando o Active Directory para o RemoteApp do Azure](remoteapp-ad.md) para obter informações de planejamento. Você também pode ir diretamente para o [AD Connect](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) para saber mais.

## Etapa 3: Publicar aplicativos ##

Um aplicativo do Azure RemoteApp é o aplicativo ou programa fornecido aos usuários. Ele está localizado na imagem do modelo na qual foi carregada a coleção. Quando um usuário acessa um aplicativo, ele aparece para ser executado no seu ambiente local, mas ele está, de fato, em execução no Azure.

Antes que os usuários possam acessar aplicativos, você precisa publicá-los para o feed do usuário final – uma lista dos aplicativos disponíveis que os usuários acessam por meio do cliente da área de trabalho remota.
 
Você pode publicar vários aplicativos em sua coleção do RemoteApp. Na página de publicação, clique em **Publicar** para adicionar um programa. É possível publicar no menu **Iniciar** da imagem do modelo ou especificar o caminho na imagem do modelo do aplicativo. Se você optar por adicionar por meio menu **Iniciar**, escolha o aplicativo a ser publicado. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## Etapa 4: Configurar o acesso do usuário ##

Agora que você criou sua coleção, precisa adicionar os usuários que você quer que usem seus recursos remotos. Se estiver usando o Active Directory, os usuários ou grupos que têm acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar esta coleção.

1.	Na página Início Rápido, clique em **Configurar o acesso do usuário**. 
2.	Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft para a qual você deseja conceder acesso.

	**Observações:**

	Certifique-se de usar o formato “user@domain.com”.

	Se você estiver usando o Office 365 ProPlus em sua coleção, você deve usar as identidades do Active Directory para os usuários. Isso ajuda a validar o licenciamento.

3.	Depois que os usuários são validados, clique em **Salvar**.


## Próximas etapas ##

É isso – sua implantação na nuvem do Azure RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL do cliente na página Início Rápido do Azure RemoteApp. Em seguida, os usuários podem fazer o logon no cliente e acessar os aplicativos que você publicou.

### Ajude-nos a ajudar você 
Você sabia que, além de classificar este artigo e fazer comentários, você pode alterar o próprio artigo? Falta alguma coisa? Há algo errado? Escrevi algo que não ficou muito claro? Role para cima e clique em **Editar no GitHub** para fazer alterações – elas serão enviadas para que as examinemos e depois de aprovadas, você verá as alterações e os aprimoramentos.

<!---HONumber=AcomDC_0128_2016-->