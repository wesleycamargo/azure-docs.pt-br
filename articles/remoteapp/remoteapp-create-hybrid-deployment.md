<properties 
	pageTitle="Como criar uma coleção híbrida para o Azure RemoteApp" 
	description="Aprenda a criar uma implantação de RemoteApp que se conecta à sua rede interna." 
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
	ms.date="08/12/2015" 
	ms.author="elizapo"/>

# Como criar uma coleção híbrida para o Azure RemoteApp

Há dois tipos de coleções de RemoteApp:

- Nuvem: reside completamente no Azure e é criada usando a opção **Criação rápida** no Portal de Gerenciamento do Azure.  
- Híbrido: inclui uma rede virtual para acesso local e é criado usando a opção **Criar com VNET** no Portal de Gerenciamento.

Este tutorial explica o processo de criação de uma coleção híbrida. Há oito etapas:

1.	Decida qual [imagem](remoteapp-imageoptions.md) usar para sua coleção. Você pode criar uma imagem personalizada ou usar uma das imagens da Microsoft incluídas em sua assinatura.
2. Configurar a sua rede virtual.
2.	Criar uma coleção de RemoteApp.
2.	Vincular a sua coleção à sua rede virtual.
3.	Adicionar uma imagem de modelo à sua coleção.
4.	Configurar a sincronização do diretório. O RemoteApp requer que você se integre ao Active Directory do Azure 1) configurando o Active Directory do Azure com a opção de sincronização de senha, ou 2) configurando o Active Directory do Azure sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS. Confira as [informações de configuração do Active Directory com o RemoteApp](remoteapp-ad.md).
5.	Publicar aplicativos do RemoteApp.
6.	Configurar o acesso do usuário.

**Antes de começar**

Você precisa fazer o seguinte antes de criar a coleção:

- [Inscrever-se](http://azure.microsoft.com/services/remoteapp/) no RemoteApp. 
- Crie uma conta de usuário no Active Directory para usar como a conta de serviço do RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio.
- Colete informações sobre a sua rede local: informações sobre endereço IP e detalhes do dispositivo VPN.
- Instale o módulo [PowerShell do Azure](../install-configure-powershell.md).
- Colete informações sobre os usuários aos quais deseja conceder acesso. Você precisará do nome UPN do Active Directory do Azure (por exemplo, name@contoso.com) para cada usuário.
- Escolha sua imagem de modelo. Uma imagem de modelo do RemoteApp contém os aplicativos e programas que você deseja publicar para seus usuários. Consulte [Opções de imagem do RemoteApp](remoteapp-imageoptions.md) para obter mais informações. 
- [Configure o Active Directory para RemoteApp](remoteapp-ad.md).



## Etapa 1: Configurar a sua rede virtual
Você pode implantar uma coleção RemoteApp híbrida que usa uma rede virtual existente do Azure, ou você pode criar uma nova rede virtual. Uma rede virtual permite que os seus usuários acessem os dados da sua rede local através dos recursos remotos do RemoteApp. Usar uma rede virtual do Azure fornece à sua coleção acesso direto à rede a outros serviços do Azure e máquinas virtuais implantadas naquela rede virtual.

### Criar um VNET do Azure e associá-lo à sua implantação do Active Directory

Comece criando uma [rede virtual](https://msdn.microsoft.com/library/azure/dn631643.aspx). Isso é feito na guia **Rede** no Portal de Gerenciamento do Azure. Você precisa se conectar à sua rede virtual para a implantação do Active Directory que é sincronizado ao seu locatário do Active Directory do Azure.

Consulte [Sobre as configurações de Rede Virtual no Portal de Gerenciamento](https://msdn.microsoft.com/library/azure/jj156074.aspx) para obter mais informações.

### Verifique se a sua rede virtual está pronta para o RemoteApp
Antes de criar a sua coleção de RemoteApp, vamos garantir que a sua nova rede virtual esteja pronta. Você pode validar isso fazendo o seguinte:

1. Crie uma máquina virtual do Azure dentro da sub-rede da rede virtual que você acabou de criar para o RemoteApp.
2. Use a área de trabalho remota para se conectar à máquina virtual. (Clique em **Conectar**.)
3. Associe-a à mesma implantação do Active Directory a qual você deseja usar para o RemoteApp.

Funcionou? Sua rede virtual e sub-rede estão prontas para o RemoteApp!

Você pode encontrar mais informações sobre a criação de máquinas virtuais do Azure e conectar-se a elas com a área de trabalho remota [aqui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## Etapa 2: Criar uma coleção de RemoteApp ##



1. No [Portal de Gerenciamento do Azure](http://manage.windowsazure.com), vá para a página do RemoteApp.
2. Clique em **Novo > Criar com VNET**.
3. Digite um nome para a sua coleção.
4. Escolha o plano que você deseja usar - standard ou basic.
5. Clique em **Criar coleção de RemoteApp**.

Após sua coleção do RemoteApp ter sido criada, clique duas vezes no nome da coleção. Isso abrirá a página **Início Rápido** - é nela que você terminará de configurar a coleção.

## Etapa 3: Vincular a sua coleção à rede virtual ##

 
1. Na página **Início rápido**, clique em **vincular uma rede virtual**.
2. Escolha a rede virtual que você deseja usar na lista suspensa.
3. Escolha a região que você deseja usar e verifique se a assinatura correta aparece no campo. 
5. Voltando à página **Início rápido**, clique em **ingressar no domínio local**. Adicione a conta de serviço RemoteApp ao domínio do Active Directory local. Serão necessários o nome do domínio, a unidade organizacional, o nome do usuário da conta de serviço e a senha. 

	Estas são as informações reunidas se você tiver seguido as etapas em [Configurar o Active Directory para o RemoteApp do Azure](remoteapp-ad.md).


## Etapa 4: Link para uma imagem do RemoteApp ##

Uma imagem do modelo do RemoteApp contém os programas que deseja compartilhar com os usuários. Você pode criar uma nova [imagem de modelo](remoteapp-imageoptions.md) ou vincular a uma imagem existente (uma já importada ou carregada no RemoteApp do Azure). Você também pode vincular a uma das [imagens de modelo](remoteapp-images.md) do RemoteApp que contêm os programas Office 365 ou Office 2013 (para uso de avaliação).

Se estiver fazendo o upload da nova imagem, será necessário inserir o nome e escolher o local para a imagem. Na próxima página do assistente, você verá um conjunto de cmdlets do PowerShell - copie e execute esses cmdlets em um prompt elevado do Windows PowerShell para fazer o upload da imagem específica.

Se estiver conectando-se a uma imagem do modelo existente, especifique o nome da imagem, o local e a assinatura do zure associada.



## Etapa 5: Configurar a sincronização de diretório do Active Directory ##

O RemoteApp requer que você se integre ao Active Directory do Azure 1) configurando o Active Directory do Azure com a opção de sincronização de senha, ou 2) configurando o Active Directory do Azure sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS. Consulte o [Roteiro de sincronização do diretório](http://msdn.microsoft.com//library/azure/hh967642.aspx) para as informações de planejamento e etapas detalhadas.

## Etapa 6: Publicar aplicativos do RemoteApp ##

Um aplicativo do RemoteApp é o aplicativo ou programa fornecido aos seus usuários. Ele está localizado na imagem do modelo na qual foi carregada a coleção. Quando um usuário acessa um aplicativo, ele aparece para ser executado no seu ambiente local, mas ele está, de fato, em execução no Azure.

Antes que os usuários possam acessar os aplicativos RemoteApp, você precisa publicá-los no feed do usuário final – uma lista dos aplicativos disponíveis que os usuários acessam por meio do cliente da área de trabalho remota.
 
Você pode publicar vários aplicativos em sua coleção de RemoteApp. Na página de publicação do RemoteApp, clique em **Publicar** para adicionar um aplicativo. É possível publicar no menu Iniciar da imagem do modelo ou especificar o caminho na imagem do modelo para o aplicativo. Se optar por adicionar pelo menu Iniciar, escolha o programa para o aplicativo. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## Etapa 7: Configurar o acesso do usuário ##

Agora que você criou sua coleção de RemoteApp, você precisa adicionar os usuários que você deseja que usem seus recursos remotos. Os usuários com acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar esta coleção do RemoteApp.

1.	Na página Início Rápido, clique em **Configurar o acesso do usuário**. 
2.	Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft para a qual você deseja conceder acesso.

	**Observações:**

	Certifique-se de usar o formato “user@domain.com”.

	Se você estiver usando o Office 365 ProPlus em sua coleção, você deve usar as identidades do Active Directory para os usuários. Isso ajuda a validar o licenciamento.


3.	Assim que os usuários forem validados, clique em **Salvar**.


## Próximas etapas ##
É isso - a sua coleção híbrida do RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL para o cliente na página Início Rápido do RemoteApp. Em seguida, os usuários podem fazer o logon no cliente e acessar os aplicativos que você publicou.


 

<!---HONumber=August15_HO7-->