
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
    ms.date="11/04/2015"
    ms.author="elizapo" />



# Alterar o locatário do Active Directory do Azure no RemoteApp do Azure

O RemoteApp do Azure usa o Active Directory do Azure (Azure AD) para permitir o acesso do usuário. O único locatário do AD do Azure que você pode usar no Azure RemoteApp é aquele associado à assinatura do Azure. Você pode exibir a assinatura associada na página **Configurações** no portal. Examine a coluna **Diretório** na guia **Assinaturas**.

> [AZURE.NOTE]Para que essa alteração seja bem-sucedida, primeiro remova todos os usuários do locatário existente do Active Directory do Azure de todas as coleções do RemoteApp do Azure. Para isso, acesse o Portal do Azure, vá para a guia **Azure RemoteApp** e abra cada coleção do Azure RemoteApp. Vá para a guia **Usuários** e remova usuários que pertencem ao seu locatário atual do Active Directory do Azure. Repita para todas as coleções de RemoteApp do Azure existentes. Sem isso, você não poderá criar ou aplicar patches em coleções.

Se você quiser usar um locatário diferente, siga estas etapas para alterar a associação à sua assinatura:

1. No portal, remova os usuários do Azure AD para os quais você deu acesso aos serviços do RemoteApp. (Veja a observação acima para obter as etapas para fazer isso).


2. Defina uma conta da Microsoft (anteriormente chamada de Live ID) como o Administrador de serviços. (Não sabe se você já é o administrador de serviço? É possível descobrir isso clicando em **Configurações -> Administradores**). Veja como você pode alterar isso:
	1. Clique no usuário no canto superior direito e clique em **Exibir minha cobrança**.
	2. Clique na assinatura. Em seguida, na nova página, role para baixo e clique em **Editar detalhes da assinatura** no canto direito. (Classifique a partir da metade inferior direita, se isso ajudá-lo a encontrar).
	3. Digite a conta da Microsoft para o usuário deve ser o administrador de serviços.

3. Agora, saia do portal e entre novamente com a conta da Microsoft especificada na etapa anterior.


4. Clique em **Novo -> Serviços de Aplicativos -> Active Directory -> Diretório -> Criação Personalizada**.
5. Em **Diretório**, escolha **Usar diretório existente**. Precisaremos desconectá-lo do portal agora, portanto escolha **Estou pronto para sair agora**. 
6. Entre novamente no portal como administrador global do diretório que você deseja adicionar. (Se você ainda não era o administrador global, você será após uma rodada de entrada e saída).
7. Ao entrar, você será solicitado se deseja usar seu locatário do AD existente com a sua assinatura. Clique em **Continuar** e em **Sair agora**.
5. Entre novamente e volte para **Configurações -> Assinaturas**. Selecione sua assinatura e clique em **Editar Diretório**. Selecione o locatário do AD do Azure que deseja usar.



Agora, você pode usar o novo locatário do Azure AD para controlar o acesso à assinatura do Azure e configurar o acesso do usuário no RemoteApp do Azure.

<!---HONumber=Nov15_HO2-->