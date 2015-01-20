<properties title="Add a user in RemoteApp" pageTitle="Adicionar um usuário no RemoteApp" description="Aprensa como adicionar usuários no RemoteApp" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Como adicionar um usuário no RemoteApp
 
Antes que os usuários possam ver e usar os aplicativos no RemoteApp, você precisa conceder acesso a eles. Essa é a parte fácil: Na guia **Acesso de usuário**, insira as informações da conta do usuário para conceder acesso a este serviço.

Quais informações da conta são necessárias? Isso depende do tipo de coleção criada (nuvem ou híbrida) e se você estiver usando Office 365 ProPlus nessa coleção.

##Informações de conta na Nuvem x híbrida
A coleção na nuvem oferece suporte a contas da Microsoft e sincronização de diretório das contas de trabalho do Active Directory do Azure (que também são contas do Office 365). 

A coleção híbrida suporta somente as contas do Active Directory do Azure que foram sincronizadas (usando uma ferramenta como DirSync) de uma implantação do Active Directory do Windows Server. Especificamente, ou sincronizado com a opção de sincronização de senha ou sincronizado com federação de serviços de Federação do Active Directory (AD FS) configurada. Consulte [Configurando o Active Directory para o RemoteApp do Azure](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-ad/) para obter mais informações sobre os requisitos do AD do Azure.

**Observação:** os usuários do Active Directory do Azure devem ser do locatário associado a sua assinatura. (Você pode exibir e modificar sua assinatura na guia **Configurações** no portal. Consulte [Alterar o locatário do Active Directory do Azure usado pelo RemoteApp](http://msdn.microsoft.com/pt-br/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obter mais informações.)

##Informações de conta de usuário do Office 365 ProPlus
Se você estiver usando a imagem do modelo do Office 365 ProPlus em sua coleção *ou* se você criou uma imagem personalizada que usa o Office 365, você pode adicionar apenas usuários do Active Directory do Azure que têm assinaturas do Office 365 para o domínio padrão da sua assinatura. Consulte [usando o Office 365 com o Azure RemoteApp](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-o365/) para obter mais informações.

<!--HONumber=35.2-->
