<properties
    pageTitle="Adicionar um usuário à sua coleção do RemoteApp do Azure | Microsoft Azure"
    description="Saiba como adicionar usuários à sua coleção do RemoteApp do Azure"
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
    ms.date="05/18/2016"
    ms.author="elizapo" />

# Como adicionar um usuário à sua coleção do RemoteApp do Azure

Antes que os usuários possam ver e usar seus aplicativos no RemoteApp do Azure, você precisa conceder acesso à sua coleção a eles. Essa é a parte fácil: na guia **Acesso de Usuário**, insira as informações da conta para o usuário e depois clique na marca de seleção.

Quais informações da conta são necessárias? Isso depende do tipo de coleção criada (nuvem ou híbrida) e se você estiver usando Office 365 ProPlus nessa coleção.

## Identidades de usuário com suporte

Os tipos de coleção diferentes (nuvem versus híbridos) são compatíveis com o uso de identidades de usuário diferentes para acesso aos aplicativos.

Para obter uma coleção híbrida do RemoteApp, você precisa configurar uma infraestrutura de domínio do Active Directory local e um locatário do Active Directory do Azure com a Integração de diretório (e opcionalmente logon único). Além disso, você precisa criar alguns objetos do Active Directory no diretório local.

Para uma coleção de nuvem do RemoteApp, qualquer usuário que tenha o Active Directory do Azure que dá suporte a identidades pode receber acesso de usuário para o RemoteApp para incluir as Contas da Microsoft. Consulte a tabela abaixo.

Os usuários do Office 365 são usuários do Active Directory do Azure. Se eles tiverem o Active Directory do Azure híbrido, as contas sincronizadas do diretório, eles poderão receber acesso de usuário em uma implantação híbrida do RemoteApp.

Você pode usar essa tabela como uma referência rápida sobre qual identidade tem suporte em sua coleção e quais são os requisitos do Active Directory.

|Contas de usuário |Nuvem |Híbrido|
|--------------|--------|------|
|Conta da Microsoft| 	Sim|	Não|
|Active Directory do Azure (Azure AD)| | |
|Somente nuvem do Azure AD |Sim |Não |
|ADsync com sincronização de senha |Sim |Sim |
|ADsync sem sincronização de senha|	Sim |Não |
|ADsync com o AD FS |Sim |Sim |
|[Provedores de identidade de terceiros com suporte do Azure](https://msdn.microsoft.com/library/azure/jj679342.aspx) (por exemplo, Ping) |Sim |Sim|
|Multi-Factor Authentication |Sim |Sim |

Verifique [obter mais informações](remoteapp-ad.md) sobre a configuração do Active Directory para o RemoteApp.


> [AZURE.NOTE] Os usuários do Active Directory do Azure devem ser do locatário que está associado à sua assinatura. (Você pode exibir e modificar a sua assinatura na guia **Configurações** no portal. Consulte [Alterar o locatário do Active Directory do Azure usado pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)

## Informações de conta de usuário do Office 365 ProPlus
Se você estiver usando a imagem do modelo do Office 365 ProPlus em sua coleção *ou* se você criou uma imagem personalizada que usa o Office 365, é possível adicionar apenas usuários do Active Directory do Azure que têm assinaturas do Office 365 para o domínio padrão da sua assinatura. Consulte [Usando o Office 365 com o Azure RemoteApp](remoteapp-o365.md) para obter mais informações.

<!---HONumber=AcomDC_0525_2016-->