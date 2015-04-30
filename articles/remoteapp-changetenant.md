
<properties 
    pageTitle="Alterar o locatário do Active Directory do Azure no RemoteApp"
    description="Saiba como alterar o locatário do Active Directory do Azure associado com o RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# Alterar o locatário do Active Directory do Azure no RemoteApp

O RemoteApp usa o Active Directory do Azure (AD do Azure) para permitir o acesso do usuário. O único locatário do AD do Azure que você pode usar é aquele associado à assinatura do Azure. Você pode exibir a assinatura associada na página Configurações no portal. Examine a coluna Diretório na guia Assinaturas. 

Se você quiser usar um locatário diferente, siga estas etapas para alterar a associação à sua assinatura:

1. No portal, remova quaisquer usuários do AD do Azure para o qual você deu acesso aos serviços do RemoteApp.


2. Defina uma conta da Microsoft (anteriormente chamada de Live ID) como o administrador de serviços. (Consulte **Configurações -> Administradores**.)
	1. Clique no usuário conectado no momento no canto superior direito e, em seguida, clique em **Exibir minha cobrança**.
	2. Selecione sua assinatura e, em seguida, clique em **Editar detalhes da assinatura**.
	3. Faça as alterações necessárias.



3. Saia do portal e entre novamente com a conta da Microsoft especificada na etapa anterior.


4. Clique em **Novo -> Serviços de Aplicativos -> Active Directory -> Criação Personalizada -> Usar Diretório Existente**. Adicione o locatário do AD do Azure que deseja associar a essa assinatura.


5. Em **Assinaturas -> Configurações**, selecione sua assinatura e, em seguida, clique em **Editar Diretório**. Selecione o locatário do AD do Azure que deseja usar.



Agora você pode usar o novo locatário do AD do Azure para controlar o acesso à assinatura do Azure e configurar o acesso do usuário no RemoteApp.

<!--HONumber=52-->