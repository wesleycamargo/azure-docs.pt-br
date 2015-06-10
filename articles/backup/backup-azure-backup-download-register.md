<properties
	pageTitle="Agente de download, instalar e registrar o Azure Backup"
	description="Saiba como e onde baixar o agente de Backup do Azure, etapas de instalação e como registrar o agente de Backup do Azure usando as credenciais do cofre"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Baixar, instalar e registrar o agente de Backup do Azure

Depois de criar o Cofre de Backup do Azure, um agente deve ser instalado em cada servidor seu local (Windows Server, cliente ou servidor do System Center Data Protection Manager) que permite que você os dados de backup e o aplicativo no Azure. Este artigo aborda as etapas necessárias para configurar o agente de Backup do Azure em um computador de cliente do Windows ou o Windows Server.

1. Entrar para a [Portal de gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Serviços de Recuperação**, em seguida, selecione o cofre de backup que você quer registrar com um servidor. É exibida a página de início rápido para o Cofre de backup. <br/> ![Início rápido][1]

3. Na página início rápido, clique em **do Windows Server ou do cliente do System Center Data Protection Manager ou Windows** em **baixar o agente** opção. Clique em Salvar para copiá-lo para a máquina local. <br/> ![Salve o agente][2]

4. Depois que o agente está instalado, clique duas vezes em MARSAgentInstaller.exe para iniciar a instalação do agente de Backup do Azure. Escolha a pasta de instalação e a pasta de rascunho necessários para o agente. O local do cache especificado deve ter espaço livre, que é pelo menos 5% dos dados de backup.

5.	Se você usar um servidor proxy para se conectar à internet, no **configuração de Proxy** tela, insira os detalhes do servidor de proxy. Se você usar um proxy autenticado, insira os detalhes de nome e senha de usuário nesta tela.

6.	O Azure Backup agent instalar o .NET Framework 4.5 e Windows PowerShell (se já não estiver disponível) para concluir a instalação.

7.	Depois que o agente está instalado, clique no **prosseguir para o registro** botão para continuar com o fluxo de trabalho. <br/> ![Registrar][3]

8. Na tela de credenciais do cofre, navegue até e selecione o arquivo de credenciais do cofre que foi baixado anteriormente. <br/> ![Credenciais do cofre][4] <br/> <br/>
> [AZURE.NOTE]O arquivo de credenciais do cofre é válido somente para 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo "arquivo de credenciais do cofre fornecido expirou"), logon no portal do Azure e baixe o arquivo de credenciais do cofre novamente.
>
> Certifique-se de que o arquivo de credenciais do cofre está disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados de acesso, copie o arquivo de credenciais do cofre para um local temporário nessa máquina e repita a operação.
>
> Se você encontrar um erro de credenciais do Cofre inválido (por exemplo, "credenciais do Cofre inválido fornecidas". O arquivo está corrompido ou têm as últimas credenciais associadas com o serviço de recuperação", repita a operação após fazer o download de um novo arquivo de credencial do cofre do portal. Esse erro é geralmente visto se o usuário clicar na opção de credencial de Cofre de Download no portal do Azure, em uma rápida sucessão. Nesse caso, apenas o segundo arquivo de credencial de cofre é válido.

9. No **configuração de criptografia** tela, você poderia ou gerar uma senha ou forneça uma frase secreta (mínimo de 16 caracteres) e lembre-se de salvar a senha em um local seguro. <br/> ![Criptografia][5] <br/> <br/>
> [AZURE.WARNING]Se a senha for perdida ou esquecida; Microsoft não pode ajudar na recuperação de dados de backup. O usuário final possui a senha de criptografia e a Microsoft não tem nenhuma visibilidade a frase secreta que é usada pelo usuário final. Salve o arquivo em um local seguro, como seria necessário durante uma operação de recuperação.

10. Depois de clicar o **Concluir** botão, a máquina está registrada com êxito no cofre e agora você está pronto para iniciar o backup do Microsoft Azure. Você pode modificar as configurações especificadas durante o fluxo de trabalho de registro clicando no **alterar propriedades** opção no Azure Backup mmc snap in <br/> ![Alterar propriedades][6]

## Próximas etapas
+ Consulte a instalação e configuração do Backup do Azure [FAQ](backup-azure-backup-faq.md) dúvidas sobre o fluxo de trabalho.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir--> 