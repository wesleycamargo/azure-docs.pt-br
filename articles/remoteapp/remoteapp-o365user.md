
<properties 
    pageTitle="Como usar o Azure RemoteApp com as contas de usuário do Office 365 | Microsoft Azure"
	description="Saiba como usar o Azure RemoteApp com minhas contas de usuário do Office 365"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# Como usar o Azure RemoteApp com minhas contas de usuário do Office 365

> [AZURE.IMPORTANT]
O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se você tiver uma assinatura do Office 365, terá um Azure Active Directory que armazena os nomes de usuário e senhas usados para acessar os serviços do Office 365. Por exemplo, quando os usuários ativam o Office 365 ProPlus, eles se autenticam no Azure AD para verificar se há licenças. A maioria dos clientes gostaria de usar o mesmo diretório do Azure RemoteApp.

Se você estiver implantando o Azure RemoteApp, provavelmente estará usando uma assinatura do Azure que está associada a um Azure AD diferente. Para usar o diretório do Office 365, você precisará mover a assinatura do Azure para esse diretório.

Para obter informações sobre como implantar os aplicativos de cliente do Office 365, consulte [Como usar sua assinatura do Office 365 com o Azure RemoteApp](remoteapp-officesubscription.md).
 
## Fase 1: registrar sua assinatura gratuita do Azure Active Directory do Office 365
Se você está usando o portal clássico do Azure, use as etapas em [Registrar sua assinatura gratuita do Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) para obter acesso administrativo ao Azure AD por meio do Portal de Gerenciamento. Como resultado desse processo, você poderá fazer logon no portal do Azure e ver seu diretório lá – neste ponto, você não verá muito porque a assinatura completa do Azure usada com o Azure RemoteApp está em um diretório diferente.

Lembre-se do nome e da senha da conta de administrador que você criou nesta etapa – eles serão necessários na Fase 2.

Se você está usando o portal do Azure, confira [Como registrar e ativar Azure Active Directory gratuito usando o portal do Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## Fase 2: altere o Azure AD associado à sua assinatura do Azure.
Iremos alterar sua assinatura do Azure do seu diretório atual para o diretório do Office 365, com o qual trabalhamos na Fase 1.

Siga as instruções descritas em [Alterar o locatário do Active Directory do Azure no Azure RemoteApp](remoteapp-changetenant.md). Você deve prestar uma atenção especial nas etapas a seguir:

- Etapa 1: se você implantou o Azure RemoteApp (ARA) nesta assinatura, verifique se removeu todas as contas de usuário do Azure AD de todas as coleções ARA primeiro, antes de tentar qualquer outra coisa. Como alternativa, você pode considerar a exclusão de todas as coleções existentes.
- Etapa 2: Esta é uma etapa crítica. Você precisa usar uma conta da Microsoft (por exemplo, @outlook.com) como um Administrador de Serviços na assinatura; isso ocorre porque não podemos ter nenhuma conta de usuário do Azure AD existente anexada à assinatura – se tivermos, não conseguiremos movê-la para um Azure AD diferente.
- Etapa 4: Ao adicionar um diretório existente, o sistema pedirá para você entrar com a conta de administrador desse diretório. Use a conta de administrador da Fase 1.
- Etapa 5: altere o diretório-pai da assinatura para o diretório do Office 365. O resultado final deve ser que em Configurações -> Assinaturas sua assinatura lista o diretório do Office 365. ![Alterar o diretório-pai da assinatura](./media/remoteapp-o365user/settings.png)
 

Neste ponto, sua assinatura do Azure RemoteApp está associada ao Azure AD do Office 365. Você pode usar as contas de usuário existentes do Office 365 com o Azure RemoteApp!

<!---HONumber=AcomDC_0817_2016-->