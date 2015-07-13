<properties
   pageTitle="Baixar, instalar e registrar o agente de Backup do Azure"
   description="Saiba como e onde baixar o agente de Backup do Azure, as etapas de instalação e como registrar o agente de Backup do Azure usando as credenciais do cofre"
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

Depois de criar o cofre de Backup do Azure, um agente deve ser instalado em cada servidor local (Windows Server, cliente Windows ou servidor do System Center Data Protection Manager), que permite que você faça backup de dados e do aplicativo no Azure. Este artigo aborda as etapas necessárias para configurar o agente de Backup do Azure em um computador cliente Windows ou Windows Server.

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com/)

2. Clique em **Serviços de Recuperação**, em seguida, selecione o cofre de backup que você quer registrar com um servidor. A página Início Rápido desse cofre de backup será exibida. <br/> ![Início rápido][1]

3. Na página Início Rápido, clique em **Para Windows Server, System Center Data Protection Manager ou cliente Windows** na opção **Baixar Agente**. Clique em Salvar para copiá-lo para o computador local. <br/> ![Salvar agente][2]

4. Depois que o agente for instalado, clique duas vezes em MARSAgentInstaller.exe para iniciar a instalação do agente de Backup do Azure. Escolha a pasta de instalação e a pasta de rascunho necessárias para o agente. O local do cache especificado deve ter espaço livre, que é pelo menos 5% dos dados de backup.

5.	Se você usar um servidor proxy para se conectar à Internet, na tela **Configuração de Proxy**, insira os detalhes do servidor proxy. Se você usar um proxy autenticado, insira os detalhes de nome de usuário e senha nessa tela.

6.	O agente de Backup do Azure instala o .NET Framework 4.5 e o Windows PowerShell (se ele já não estiver disponível) para concluir a instalação.

7.	Depois de o agente ser instalado, clique no botão **Prosseguir com o Registro** para continuar com o fluxo de trabalho. <br/> ![Registrar][3]

8. Na tela de credenciais do cofre, navegue e selecione o arquivo de credenciais do cofre que foi baixado anteriormente. <br/> ![Credenciais do cofre][4] <br/> <br/>
> [AZURE.NOTE]O arquivo de credenciais do cofre é válido somente por 48 horas (após o download do portal). Se você encontrar qualquer erro nessa tela (por exemplo "o arquivo de credenciais do cofre fornecido expirou"), faça logon no Portal do Azure e baixe o arquivo de credenciais do cofre novamente.
>
> Certifique-se de que o arquivo de credenciais do cofre esteja disponível em um local que pode ser acessado pelo aplicativo de instalação. Se você encontrar erros relacionados a acesso, copie o arquivo de credenciais do cofre para um local temporário nesse computador e repita a operação.
>
> Se você encontrar um erro de credenciais inválidas do cofre (por exemplo, "Credenciais de cofre inválidas fornecidas. O arquivo está corrompido ou não têm as últimas credenciais associadas ao serviço de recuperação", repita a operação após fazer o download de um novo arquivo de credencial do cofre do portal. Esse erro geralmente aparece se o usuário clica na opção Baixar credencial de cofre no Portal do Azure em uma rápida sucessão. Nesse caso, apenas o segundo arquivo de credencial de cofre é válido.

9. Na tela **Configuração de criptografia**, você pode gerar uma senha ou fornecer uma senha (mínimo de 16 caracteres) e lembre-se de salvar a senha em um local seguro. <br/> ![Criptografia][5] <br/> <br/>
> [AZURE.WARNING]Se a senha for perdida ou esquecida, a Microsoft não poderá ajudar na recuperação dos dados de backup. O usuário final possui a senha de criptografia e a Microsoft não tem visibilidade da senha que é usada pelo usuário final. Salve o arquivo em um local seguro, como seria necessário durante uma operação de recuperação.

10. Depois de clicar no botão **Concluir**, o computador estará registrado com êxito no cofre e agora você estará pronto para iniciar o backup no Microsoft Azure. Você pode modificar as configurações especificadas durante o fluxo de trabalho de registro clicando na opção **Alterar Propriedades** no snap-in do MMC no Backup do Azure. <br/> ![Alterar propriedades][6]

## Próximas etapas
+ Consulte as [perguntas frequentes](backup-azure-backup-faq.md) sobre instalação e configuração do Backup do Azure em caso de dúvidas sobre o fluxo de trabalho.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png
 

<!---HONumber=62-->