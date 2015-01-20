<properties title="How to create a cloud collection of RemoteApp" pageTitle="Como criar uma coleção na nuvem do RemoteApp" description="Saiba como criar uma implantação de nuvem do RemoteApp que salva dados na nuvem do Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="kathyw" />

#Como criar uma coleção na nuvem do RemoteApp

Há dois tipos de coleções de RemoteApp: 

- Nuvem: reside completamente no Azure e é criada usando a opção **Criação rápida** no Portal de Gerenciamento do Azure.  
- Híbrida: inclui uma rede virtual para acesso local e é criada usando a opção **Criar com VPN** no Portal de Gerenciamento.

Este tutorial explica o processo de criação de uma implantação de nuvem. Há quatro etapas: 

1.	Criar uma coleção de RemoteApp.
2.	Opcionalmente, configure a sincronização do diretório. O RemoteApp exige esta sincronização de usuários, grupos, contatos e senhas do seu Active Directory local para o seu locatário do Active Directory do Azure.
5.	Publicar aplicativos do RemoteApp.
6.	Configurar o acesso do usuário.

**Antes de começar**

Você precisa fazer o seguinte antes de criar a coleção:

- Inscrever-se no RemoteApp. É possível fazer isso em [http://azure.microsoft.com/pt-br/services/remoteapp/](http://azure.microsoft.com/pt-br/services/remoteapp/).
- Colete informações sobre os usuários aos quais deseja conceder acesso. Podem ser informações da conta da Microsoft ou da conta corporativa do Active Directory para usuários ou grupos.
- Este procedimento pressupõe que usará ou uma das imagens de modelo fornecidas como parte de sua assinatura ou que você já carregou a imagem do modelo que deseja usar. Se desejar fazer o upload da imagem do modelo, é possível fazer isso na página Imagens do modelo. Apenas clique em **Carregar uma imagem do modelo** e siga as etapas do assistente. 
- Deseja fornecer aplicativos personalizados ou programas LOB? Crie uma nova [imagem de modelo personalizada](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/) e use-a na sua implantação na nuvem.

## **Etapa 1: Criar uma coleção de RemoteApp** ##



1. No [Portal de Gerenciamento do Microsoft Azure](http://manage.windowsazure.com), vá para a página do RemoteApp.
2. Clique em **Novo > Criação Rápida**.

3. Digite um nome para a coleção e selecione sua região.
4. Escolha o plano que você deseja usar - standard ou basic.
5. Escolha o modelo a ser usado para esta coleção. 

	**Dica:** sua assinatura do RemoteApp vem com [imagens de modelo](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-images/) que contêm programas do Office 365 ou do Office 2013 (para uso de avaliação), alguns publicados (como Word) e outros prontos para publicação. Você também pode criar uma nova [imagem de modelo personalizada](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-create-custom-image/) e usá-la em sua coleção na nuvem.


1. Clique em **Criar coleção de RemoteApp**.
	
	**Importante:** pode levar até 30 minutos para provisionar sua coleção.

Após a criação da coleção de RemoteApp, vá para a página **Início Rápido** do RemoteApp para continuar as etapas de configuração.


## **Etapa 2: Configurar a sincronização do diretório do Active Directory (opcional)** ##

Se desejar usar o Active Directory, o RemoteApp exige a sincronização de diretório entre o Active Directory do Azure e o Active Directory local para sincronizar usuários, grupos, contatos e senhas com o seu locatário do Active Directory do Azure. Consulte [Configurando o Active Directory para o RemoteApp do Azure](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-ad/) para obter informações de planejamento.

## **Etapa 3: Publicar aplicativos do RemoteApp** ##

Um aplicativo do RemoteApp é o aplicativo ou programa fornecido aos seus usuários. Ele está localizado na imagem do modelo na qual foi carregada a coleção. Quando um usuário acessa um aplicativo do RemoteApp, o aplicativo aparenta ser executado no seu ambiente local, mas, na realidade, ele está em execução no Azure. 

Antes que os usuários possam acessar aplicativos, você precisa publicá-los para o feed do usuário final - uma lista dos aplicativos disponíveis que os usuários acessam por meio do cliente da área de trabalho remota.
 
Você pode publicar vários aplicativos em sua coleção de RemoteApp. Na página de publicação de RemoteApp, clique em **Publicar** para adicionar um programa. É possível publicar no menu Iniciar da imagem do modelo ou especificar o caminho na imagem do modelo para o aplicativo. Se você optar por adicionar a partir do menu Iniciar, escolha o aplicativo para publicar. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## **Etapa 4: Configurar o acesso de usuário** ##

Agora que você criou sua coleção de RemoteApp, você precisa adicionar os usuários que você deseja que usem seus recursos remotos. Se estiver usando o Active Directory, os usuários ou grupos que possuem acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar este serviço do RemoteApp.

1.	Na página Início Rápido, clique em **Configurar o acesso do usuário**. 
2.	Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft para a qual você deseja conceder acesso.

	**Observações:** 

	Certifique-se de usar o formato "user@domain.com".

	Se você estiver usando o Office 365 ProPlus em sua coleção, você deve usar as identidades do Active Directory para os usuários. Isso ajuda a validar o licenciamento. 

3.	Depois que os usuários são validados, clique em **Salvar**.


## Próximas etapas ##

É isso - a sua implantação na nuvem do RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL para o cliente na página Início Rápido do RemoteApp. Em seguida, os usuários podem fazer o logon no cliente e acessar os aplicativos que você publicou.


<!--HONumber=35.2-->
