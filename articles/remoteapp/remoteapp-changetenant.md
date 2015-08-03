
<properties
    pageTitle="Alterar o locatário do Active Directory do Azure no RemoteApp do Azure"
    description="Saiba como alterar o locatário do Active Directory do Azure associado ao RemoteApp do Azure"
    services="remoteapp"
	documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2015"
    ms.author="elizapo" />



# Alterar o locatário do Active Directory do Azure no RemoteApp do Azure

O RemoteApp do Azure usa o Active Directory do Azure (Azure AD) para permitir o acesso do usuário. O único locatário do AD do Azure que você pode usar é aquele associado à assinatura do Azure. Você pode exibir a assinatura associada na página Configurações no portal. Examine a coluna Diretório na guia Assinaturas.

> [AZURE.NOTE]Para que a alteração do Active Directory do Azure possa ser bem-sucedida, primeiro você deve remover todos os usuários do locatário existente do Active Directory do Azure de todas as coleções de RemoteApp do Azure. Para isso, acesse o Portal do Azure, vá para a guia RemoteApp do Azure e abra cada coleção de RemoteApp do Azure. Vá para a guia **Usuários** e remova usuários que pertencem ao seu locatário atual do Active Directory do Azure. Repita para todas as coleções de RemoteApp do Azure existentes. Sem isso, você não poderá criar ou aplicar patches em coleções.

Se você quiser usar um locatário diferente, siga estas etapas para alterar a associação à sua assinatura:

1. No portal, remova os usuários do Azure AD para os quais você deu acesso aos serviços do RemoteApp.


2. Defina uma conta da Microsoft (anteriormente chamada de Live ID) como o administrador de serviços. (Consulte **Configurações -> Administradores**.)
	1. Clique no usuário conectado no momento no canto superior direito e clique em **Exibir minha cobrança**.
	2. Selecione sua assinatura e clique em **Editar detalhes da assinatura**.
	3. Faça as alterações necessárias.



3. Saia do portal e entre novamente com a conta da Microsoft especificada na etapa anterior.


4. Clique em **Novo -> Serviços de Aplicativos -> Active Directory -> Criação Personalizada -> Usar Diretório Existente**. Adicione o locatário do AD do Azure que deseja associar a essa assinatura.


5. Em **Assinaturas -> Configurações**, selecione sua assinatura e clique em **Editar Diretório**. Selecione o locatário do AD do Azure que deseja usar.



Agora, você pode usar o novo locatário do Azure AD para controlar o acesso à assinatura do Azure e configurar o acesso do usuário no RemoteApp do Azure.

<!---HONumber=July15_HO4-->