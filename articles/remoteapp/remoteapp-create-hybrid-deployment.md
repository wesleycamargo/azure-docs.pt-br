---
title: "Como criar uma coleção híbrida para o Azure RemoteApp | Microsoft Docs"
description: "Aprenda a criar uma implantação de RemoteApp que se conecta à sua rede interna."
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
editor: 
ms.assetid: 08ea0ce3-3a2c-4ddf-9394-6d75c8030cb1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 79434166999d4f847f28cf6a05a21f1cd002d75f


---
# <a name="how-to-create-a-hybrid-collection-for-azure-remoteapp"></a>Como criar uma coleção híbrida para o Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Há dois tipos de coleções do Azure RemoteApp:

* Nuvem: reside completamente no Azure. Você pode optar por salvar todos os dados na nuvem (para uma coleção somente da nuvem) ou conectar sua coleção a uma VNET e salvar os dados lá.   
* Híbrido: inclui uma rede virtual para acesso local - isso requer o uso do Azure AD e um ambiente do Active Directory local.

Não sabe o que precisa? Confira [Que tipo de coleção é necessária para o Azure RemoteApp](remoteapp-collections.md)

Este tutorial explica o processo de criação de uma coleção híbrida. Há oito etapas:

1. Decida qual [imagem](remoteapp-imageoptions.md) usar para sua coleção. Você pode criar uma imagem personalizada ou usar uma das imagens da Microsoft incluídas em sua assinatura.
2. Configurar a sua rede virtual. Verifique as informações sobre [Planejamento VNET](remoteapp-planvnet.md) e [dimensionamento](remoteapp-vnetsizing.md).
3. Criar uma coleção.
4. Associe sua coleção a seu domínio local.
5. Adicionar uma imagem de modelo à sua coleção.
6. Configurar a sincronização do diretório. O Azure RemoteApp requer que você se integre ao Active Directory do Azure 1) configurando o Active Directory do Azure com a opção de sincronização de senha, ou 2) configurando o Active Directory do Azure sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS. Confira as [informações de configuração do Active Directory com o RemoteApp](remoteapp-ad.md).
7. Publicar aplicativos do RemoteApp.
8. Configurar o acesso do usuário.

**Antes de começar**

Você precisa fazer o seguinte antes de criar a coleção:

* [Inscreva-se](https://azure.microsoft.com/services/remoteapp/) no Azure RemoteApp.
* Crie uma conta de usuário no Active Directory para usar como conta de serviço do Azure RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio.
* Colete informações sobre a sua rede local: informações sobre endereço IP e detalhes do dispositivo VPN.
* Instale o módulo [PowerShell do Azure](../powershell-install-configure.md) .
* Colete informações sobre os usuários aos quais deseja conceder acesso. Você precisará do nome principal do Azure Active Directory (por exemplo, name@contoso.com) para cada usuário. Verifique se o UPN faz a correspondência entre o Azure AD e o Active Directory.
* Escolha sua imagem de modelo. Uma imagem de modelo do Azure RemoteApp contém aplicativos e programas que você quer publicar para os usuários. Consulte [Opções de imagem do Azure RemoteApp](remoteapp-imageoptions.md) para saber mais.
* Deseja usar a imagem do Office 365 ProPlus? Verifique as informações [aqui](remoteapp-officesubscription.md).
* [Configure o Active Directory para RemoteApp](remoteapp-ad.md).

## <a name="step-1-set-up-your-virtual-network"></a>Etapa 1: Configurar a sua rede virtual
Você pode implantar uma coleção híbrida que use uma rede virtual existente do Azure, ou pode criar uma nova rede virtual. Uma rede virtual permite que os seus usuários acessem os dados da sua rede local através dos recursos remotos do RemoteApp. Usar uma rede virtual do Azure fornece à sua coleção acesso direto à rede a outros serviços do Azure e máquinas virtuais implantadas naquela rede virtual.

Examine as informações sobre [planejamento da Rede Virtual](remoteapp-planvnet.md) e [tamanho da Rede Virtual](remoteapp-vnetsizing.md) antes de criar sua Rede Virtual.

### <a name="create-an-azure-vnet-and-join-it-to-your-active-directory-deployment"></a>Criar um VNET do Azure e associá-lo à sua implantação do Active Directory
Comece criando uma [rede virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Isso é feito na guia **Rede** no Portal do Azure. Você precisa se conectar à sua rede virtual para a implantação do Active Directory que é sincronizado ao seu locatário do Active Directory do Azure.

Para obter mais informações, consulte [Criar uma rede virtual usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) .

### <a name="make-sure-your-virtual-network-is-ready-for-azure-remoteapp"></a>Verifique se sua rede virtual está pronta para o Azure RemoteApp
Antes de criar sua coleção, verifique se sua nova rede virtual está pronta. Você pode validar isso fazendo o seguinte:

1. Crie uma máquina virtual do Azure dentro da sub-rede da rede virtual que você acabou de criar para o RemoteApp.
2. Use a área de trabalho remota para se conectar à máquina virtual. (Clique em **Conectar**.)
3. Associe-a à mesma implantação do Active Directory a qual você deseja usar para o RemoteApp.

Funcionou? Sua rede virtual e sub-rede estão prontas para o Azure RemoteApp!

Você pode encontrar mais informações sobre a criação de máquinas virtuais do Azure e conectar-se a elas com a área de trabalho remota [aqui](https://msdn.microsoft.com/library/azure/jj156003.aspx).

## <a name="step-2-create-an-azure-remoteapp-collection"></a>Etapa 2: Criar uma coleção do Azure RemoteApp
1. No [portal do Azure](http://manage.windowsazure.com), vá para a página do Azure RemoteApp.
2. Clique em **Novo > Criar com VNET**.
3. Digite um nome para a sua coleção.
4. Escolha o plano que você deseja usar - standard ou basic.
5. Escolha sua VNET na lista suspensa e, em seguida, sua sub-rede.
6. Escolha adicioná-la a seu domínio.
7. Clique em **Criar coleção de RemoteApp**.

Depois de criar sua coleção do Azure RemoteApp, clique duas vezes no nome da coleção. Isso abrirá a página **Início Rápido** - é nela que você terminará de configurar a coleção.

Algo deu errado? Confira as [informações de solução de problemas de coleção híbrida](remoteapp-hybridtrouble.md).

## <a name="step-3-link-your-collection-to-the-local-domain"></a>Etapa 3: Vincular sua coleção ao domínio local
1. Na página **Início Rápido**, clique em **ingressar em um domínio local**.
2. Adicione a conta de serviço do Azure RemoteApp ao domínio do Active Directory local. Serão necessários o nome do domínio, a unidade organizacional, o nome do usuário da conta de serviço e a senha.
   
    Estas são as informações reunidas se você tiver seguido as etapas em [Configurar o Active Directory para o RemoteApp do Azure](remoteapp-ad.md).

## <a name="step-4-link-to-an-azure-remoteapp-image"></a>Etapa 4: Criar uma imagem do Azure RemoteApp
Uma imagem do modelo do Azure RemoteApp contém programas que você quer compartilhar com os usuários. Você pode criar uma nova [imagem de modelo](remoteapp-imageoptions.md) ou vincular a uma imagem existente (uma já importada ou carregada no RemoteApp do Azure). Você também pode vincular a uma das [imagens de modelo](remoteapp-images.md) do Azure RemoteApp que contém os programas Office 365 ou Office 2013 (para uso de avaliação).

Se estiver fazendo o upload da nova imagem, será necessário inserir o nome e escolher o local para a imagem. Na próxima página do assistente, você verá um conjunto de cmdlets do PowerShell - copie e execute esses cmdlets em um prompt elevado do Windows PowerShell para fazer o upload da imagem específica.

Se estiver se conectando a uma imagem do modelo existente, especifique o nome da imagem, a localização e a assinatura do Azure associada.

## <a name="step-5-configure-active-directory-directory-synchronization"></a>Etapa 5: Configurar a sincronização de diretório do Active Directory
O Azure RemoteApp requer que você se integre ao Active Directory do Azure 1) configurando o Active Directory do Azure com a opção de sincronização de senha, ou 2) configurando o Active Directory do Azure sem a opção de sincronização de senha, mas usando um domínio federado ao AD FS.

Confira [AD Connect](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) - este artigo ajuda você a configurar a integração de diretórios em quatro etapas.

Consulte o [Roteiro de sincronização do diretório](http://msdn.microsoft.com//library/azure/hh967642.aspx) para as informações de planejamento e etapas detalhadas.

## <a name="step-6-publish-apps"></a>Etapa 6: Publicar aplicativos
Um aplicativo do Azure RemoteApp é o aplicativo ou programa fornecido aos usuários. Ele está localizado na imagem do modelo na qual foi carregada a coleção. Quando um usuário acessa um aplicativo, ele aparece para ser executado no seu ambiente local, mas ele está, de fato, em execução no Azure.

Antes que os usuários possam acessar aplicativos, você precisa publicá-los – isso permite que os usuários acessem os aplicativos por meio do cliente da Área de Trabalho Remota.

Você pode publicar vários aplicativos em sua coleção. Na página de publicação, clique em **Publicar** para adicionar um aplicativo. É possível publicar por meio do menu **Iniciar** da imagem do modelo ou especificando o caminho na imagem do modelo do aplicativo. Se você optar por adicionar por meio do menu **Iniciar** , escolha o programa a ser adicionado. Se você optar por fornecer o caminho para o aplicativo, forneça um nome para o aplicativo e o caminho para onde ele está instalado na imagem do modelo.

## <a name="step-7-configure-user-access"></a>Etapa 7: Configurar o acesso do usuário
Agora que você criou sua coleção, precisa adicionar os usuários que você quer que usem seus recursos remotos. Os usuários com acesso liberado precisam existir no locatário do Active Directory, associado à assinatura usada para criar esta coleção do Azure RemoteApp.

1. Na página Início Rápido, clique em **Configurar o acesso do usuário**.
2. Insira a conta de trabalho (a partir do Active Directory) ou a conta da Microsoft para a qual você deseja conceder acesso.
   
   **Observações:**
   
   Use o formato “user@domain.com”.
   
   Se você estiver usando o Office 365 ProPlus em sua coleção, você deve usar as identidades do Active Directory para os usuários. Isso ajuda a validar o licenciamento.
3. Assim que os usuários forem validados, clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
É isso – sua coleção híbrida do Azure RemoteApp foi criada e implantada com sucesso. A próxima etapa é fazer com que os seus usuários baixem e instalem o cliente da Área de Trabalho Remota. É possível encontrar a URL do cliente na página Início Rápido do Azure RemoteApp. Em seguida, os usuários podem fazer o logon no cliente e acessar os aplicativos que você publicou.

### <a name="help-us-help-you"></a>Ajude-nos a ajudar você
Você sabia que, além de classificar este artigo e fazer comentários, você pode alterar o próprio artigo? Falta alguma coisa? Há algo errado? Escrevi algo que não ficou muito claro? Role para cima e clique em **Editar no GitHub** para fazer alterações - elas serão enviadas para que as examinemos e, assim que elas forem desconectadas, você verá suas alterações e aprimoramentos bem aqui.




<!--HONumber=Nov16_HO3-->


