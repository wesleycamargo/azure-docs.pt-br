<properties 
	pageTitle="Operações de Azure AD Connect Health." 
	description="Esta é a página de Azure AD Connect Health que descreve as operações adicionais que podem ser executadas depois da implantação do Azure AD Connect Health." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>

# Operações de Azure AD Connect Health

O tópico a seguir descreve as várias operações que podem ser executadas usando o Azure AD Connect Health.

## Notificações por email
Você pode configurar o serviço do Azure AD Connect Health para enviar notificações por email quando os alertas são gerados indicando que sua infraestrutura de serviço de Federação não está íntegra. Isso ocorrerá quando um alerta for gerado, bem como quando ele é marcado como resolvido. Siga as instruções abaixo para configurar notificações por email. Observe que, por padrão, notificações por email estão desabilitadas.


### Para habilitar notificações por Email do Azure AD Connect Health

1. Abra a folha de alertas para o farm para o qual você deseja receber notificação por email.
1. Clique no botão "Configurações de notificação" na barra de ação.
1. Ative a opção de Notificação por email como ON.
1. Marque a caixa de seleção para configurar todos os administradores de locatários globais para receber notificações por email.
1. Se você deseja receber notificações por email em qualquer outro endereço de email, pode especificá-los nas caixas de destinatários de Email adicionais. Para remover um endereço de email da lista, clique com o botão direito na entrada e selecione Excluir.
1. Para finalizar as alterações clique em "Salvar". Todas as alterações entrarão em vigor somente depois que você selecionar "Salvar".






## Excluir um servidor do serviço de Azure AD Connect Health

Em alguns casos, você poderá remover um servidor que está sendo monitorado. Siga as instruções abaixo para remover um servidor do serviço de Azure AD Connect Health.

Ao excluir um servidor, esteja ciente das seguintes opções:

- Esta ação irá PARAR de coletar todos os demais dados desse servidor. Esse servidor será removido do serviço de monitoramento. Após essa ação, você não poderá exibir os novos alertas, monitoramento ou o uso de dados de análise para esse servidor.
- Essa ação NÃO irá desinstalar ou remover o agente de integridade do servidor. Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, será possível ver os eventos de erro no servidor relacionados ao agente de integridade.
- Essa ação NÃO excluirá os dados já coletados deste servidor. Esses dados serão excluídos de acordo com a política de retenção de dados do Microsoft Azure. 
- Depois de executar essa ação, se você quiser iniciar o monitoramento do mesmo servidor novamente, será necessário desinstalar e reinstalar o agente de integridade neste servidor. 


	### Para excluir um servidor do serviço do Azure AD Connect Health
<ol>
1. Abra a folha do servidor da folha da lista do servidor, selecionando o nome do servidor a ser removido. 
1. Na folha do servidor, clique no botão "Excluir" na barra de ação.
1. Confirme a ação para excluir o servidor digitando o nome do servidor na caixa de confirmação.
1. Clique no botão "Excluir".







## Exclua uma instância de serviço do serviço do Azure AD Connect Health Service

Em alguns casos, será possível remover uma instância do serviço. Siga as instruções abaixo para remover uma instância de serviço do Azure AD Connect Health.

Ao excluir uma instância de serviço, esteja ciente do seguinte:

- Esta ação irá remover a instância do serviço atual do serviço de monitoramento. 
- Essa ação NÃO irá desinstalar ou remover o agente de integridade de nenhum um dos servidores que foram monitorados como parte desta instância de serviço. Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, poderá ver os eventos de erro no(s) servidor(es) relacionado(s) ao agente de integridade. 
- Todos os dados dessa instância do serviço serão excluídos de acordo com a política de retenção de dados do Microsoft Azure. 
- Depois de executar essa ação, se você quiser iniciar o serviço de monitoramento, desinstale e reinstale o agente de integridade em todos os servidores que serão monitorados. Depois de executar essa ação, se você quiser iniciar o monitoramento do mesmo servidor novamente, será necessário desinstalar e reinstalar o agente de integridade neste servidor.







	### Para excluir uma instância de serviço do Azure AD Connect Health Service
<ol>
1. Abra uma folha de serviço na folha da lista do serviço, selecionando o identificador de serviço (nome do farm) que você deseja remover. 
1. Na folha do servidor, clique no botão "Excluir" na barra de ação.
1. Confirme o nome do serviço digitando-o na caixa de confirmação. (por exemplo: sts.contoso.com) 
1. Clique no botão "Excluir".

<!---HONumber=July15_HO4-->