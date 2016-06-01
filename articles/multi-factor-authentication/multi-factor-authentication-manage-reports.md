<properties 
	pageTitle="Relatórios do Azure Multi-Factor Authentication" 
	description="Descreve como usar o recurso de relatórios do Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="billmath"/>

# Relatórios no Azure Multi-Factor Authentication

O Azure Multi-Factor Authentication fornece vários relatórios que podem ser usados por você e sua organização. Esses relatórios podem ser acessados por meio do Portal de Gerenciamento Multi-Factor Authentication, que requer que você tenha uma licença do Provedor do Azure MFA ou um Azure MFA, Azure AD Premium ou Enterprise Mobility Suite. Veja a seguir uma lista dos relatórios disponíveis.

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

1.	Faça logon no http://azure.microsoft.com
2.	À esquerda, selecione Active Directory.
3.	Selecione uma das seguintes opções:
	- **Opção 1**: clique na guia Provedores de Multi-Factor Authentication. Selecione seu provedor de MFA e clique no botão Gerenciar na parte inferior.
	- **Opção 2**: selecione o diretório e clique na guia Configurar. Na seção multi-factor authentication, clique em Gerenciar configurações de serviço. Na parte inferior da página Configurações do Serviço de MFA, clique em Ir para o link para portal.
4.	No Portal de Gerenciamento do Azure Multi-Factor Authentication, na navegação à esquerda, você verá a seção Exibir um Relatório. A partir daqui, você pode selecionar os relatórios descritos acima.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionais**

* [Para usuários](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
 

<!---HONumber=AcomDC_0518_2016-->