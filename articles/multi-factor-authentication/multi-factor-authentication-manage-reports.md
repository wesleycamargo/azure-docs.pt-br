<properties 
	pageTitle="Relatórios do Azure Multi-Factor Authentication" 
	description="Descreve como usar o recurso de relatórios do Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Relatórios no Azure Multi-Factor Authentication

O Azure Multi-Factor Authentication fornece vários relatórios que podem ser usados por você e sua organização. Esses relatórios podem ser acessados por meio do Portal de Gerenciamento de autenticação multifator. Veja a seguir uma lista dos relatórios disponíveis.

Você pode acessar relatórios por meio do Portal de Gerenciamento do Azure

Nome| Descrição
:------------- | :------------- | 
Uso | Os relatórios de uso exibem informações sobre o uso geral, resumo do usuário e detalhes do usuário.
Status do servidor|Este relatório exibe o status dos servidores da autenticação multifator associada à sua conta.
Histórico de usuário bloqueado|Esses relatórios mostram o histórico de solicitações para bloquear ou desbloquear usuários.
Histórico de usuário desviado|Mostra o histórico de solicitações para desviar da autenticação multifator para o número de telefone de um usuário.
Alerta de fraude|Mostra um histórico dos alertas de fraude apresentados durante o intervalo de datas especificado.
Em fila|Lista os relatórios em fila para processamento e seu status. Um link para baixar ou exibir o relatório é fornecido quando o relatório é concluído.

## Para exibir relatórios

1. Faça logon em [http://azure.microsoft.com](http://azure.microsoft.com)
2. Selecione Active Directory à esquerda.
3. Na parte superior, selecione os Provedores de autenticação multifator. Isso trará uma lista de seus provedores de autenticação multifator.
4. Se você tiver mais de um Provedor de autenticação multifator, selecione aquele em que você deseja ver o relatório de alertas de fraude e clique em Gerenciar na parte inferior da página. Se você tiver apenas um, clique em Gerenciar. Isso abrirá o Portal de Gerenciamento do Azure Multi-Factor Authentication.
5. No Portal de Gerenciamento do Azure Multi-Factor Authentication, à esquerda, você verá Exibir um relatório. A partir daqui, você pode selecionar os relatórios descritos acima.


 
<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionais**

* [Para usuários](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=July15_HO2-->