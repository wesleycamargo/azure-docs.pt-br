<properties 
   pageTitle="Configurando a Automação do Azure"
   description="Descreve as etapas que você deve executar para configurar a Automação do Azure para uso inicial."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Configurando a Automação do Azure

Este artigo descreve as ações que você deve executar para começar a usar a Automação do Azure inicialmente.

## Contas de automação

Ao iniciar a Automação do Azure pela primeira vez, você deve criar pelo menos uma conta de Automação. As contas de Automação permitem isolar seus recursos de Automação (runbooks, ativos) dos recursos de Automação contidos em outras contas de Automação. Você pode usar contas de Automação para separar os recursos de Automação em ambientes lógicos separados. Por exemplo, você pode usar uma conta para desenvolvimento e outra para produção.

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas as contas de Automação podem gerenciar os serviços do Azure em qualquer região. O principal motivo para criar contas de Automação em regiões diferentes seria se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

Uma conta de Automação poderá ser suspensa se houver um problema com sua conta do Azure, como um pagamento vencido. Nesse caso, você não poderá acessar a conta, todos os trabalhos em execução serão suspensos e todas as agendas serão desabilitadas. Você poderá exibir a conta, mas não poderá ver recursos contidos nela. Depois que corrigir o problema e a conta de Automação for habilitada, você terá que habilitar suas agendas e reiniciar os runbooks que foram suspensos.


## Configurando a autenticação para recursos do Azure

Ao acessar recursos do Azure usando [cmdlets do Azure](http://msdn.microsoft.com/library/azure/jj554330.aspx), você precisa fornecer autenticação para sua assinatura do Azure. Na Automação do Azure, isso é feito com uma conta organizacional no Active Directory do Azure que você configura como um administrador para sua assinatura. Você pode então criar um [credencial](http://msdn.microsoft.com/library/dn940015.aspx) para essa conta de usuário e usá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) em seu runbook.

>[AZURE.NOTE]As contas da Microsoft, anteriormente conhecidas como LiveIDs, não podem ser usadas com a Automação do Azure.

## Criar um novo usuário do Active Directory do Azure para gerenciar uma assinatura do Azure

1. Faça logon no portal do Azure como administrador de serviço para a assinatura do Azure que você deseja gerenciar.
2. Selecione **Active Directory**
3. Selecione o nome do diretório associado à sua assinatura do Azure. Se necessário, você pode alterar essa associação em **Configurações > Assinaturas > Editar Diretório**.
4. [Criar um novo usuário do Active Directory](http://msdn.microsoft.com/library/azure/hh967632.aspx). Selecione **Novo usuário em sua organização** para o **Tipo de usuário** e não **Habilitar a autenticação multifator**.
5. Anote o nome completo do usuário e a senha temporária.
7. Selecione **Configurações > Administradores > Adicionar**.
8. Digite o nome de usuário completo do usuário que você criou.
9. Selecione a assinatura que você deseja que o usuário gerencie.
10. Faça logoff do Azure e, em seguida, faça logon novamente com a conta que você acabou de criar. Você será solicitado a alterar a senha do usuário.
11. Crie um novo [ativo de credencial da Automação do Azure](http://msdn.microsoft.com/library/dn940015.aspx) para a conta de usuário que você criou. O **Tipo de Credencial** deve ser **Credencial do Windows PowerShell**.


## Usar uma credencial em um runbook

Você pode recuperar a credencial em um runbook usando a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e, em seguida, usá-la com [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para se conectar à sua assinatura do Azure. Se a credencial for um administrador de várias assinaturas do Azure, você também deverá usar [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar a correta. Isso é mostrado no exemplo do Windows PowerShell abaixo que, normalmente, aparece na parte superior da maioria dos runbooks da Automação do Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Repita essas linhas após qualquer [ponto de verificação](automation-runbook-execution/#checkpoints) em seu runbook. Se o runbook for suspenso e reiniciado em outro trabalho, será necessário executar novamente a autenticação.

# Artigos relacionados
- [Autenticação do Azure: autenticando no Azure usando o Active Directory do Azure](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/) 

<!---HONumber=58-->