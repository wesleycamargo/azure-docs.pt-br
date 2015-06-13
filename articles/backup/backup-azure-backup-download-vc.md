<properties
	pageTitle="Baixar credenciais do cofre no Backup do Azure"
	description="Saiba como usar as credenciais do cofre para autenticar o computador com o Cofre de Backup e o serviço de Backup do Azure"
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
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Usando as credenciais do cofre para autenticar com o serviço Backup do Azure

O servidor local (Windows cliente ou servidor do Windows Server ou SCDPM) precisa ser autenticado com um cofre de backup antes que ele pode fazer backup de dados no Azure. A autenticação é obtida usando o "cofre credenciais". O conceito de credenciais do cofre é semelhante ao conceito de um arquivo de "configurações de publicação" que é usado no Azure PowerShell.

## O que é o arquivo de credencial do cofre?

O arquivo de credenciais do cofre é um certificado que é gerado pelo portal para cada Cofre de backup. O portal, em seguida, carrega a chave pública para o serviço de controle de acesso (ou ACS). A chave privada do certificado é disponibilizada para o usuário como parte do fluxo de trabalho que é fornecido como uma entrada no fluxo de trabalho de registro da máquina. Isso autentica o computador para enviar os dados de backup para um cofre identificado no serviço de Backup do Azure.

Vale a pena destacar que a credencial do cofre é usada somente durante o fluxo de trabalho de registro. É responsabilidade do usuário para garantir que o arquivo de credenciais do Cofre não seja comprometido. Terem caído em mãos de qualquer usuário não autorizado, o arquivo de credenciais do cofre pode ser usado para registrar outras máquinas no cofre do mesmo. No entanto, como os dados de backup são criptografados usando uma senha que pertence ao cliente, dados de backup existentes não podem ser comprometidos. Para atenuar esse problema, as credenciais do cofre é um conjunto de expirar em 48hrs. Você pode baixar as credenciais do Cofre de um cofre de backup a qualquer número de vezes – mas apenas o arquivo mais recente da credencial cofre é aplicável durante o fluxo de trabalho de registro.

## Baixe o arquivo de credencial do cofre

O arquivo de credencial do cofre é baixado por meio de um canal seguro no portal do Azure. O serviço de Backup do Azure não tem conhecimento da chave privada do certificado e a chave privada não é mantida no portal ou o serviço. Use as seguintes etapas para baixar credenciais do cofre para um computador local.

1.  Entrar para a [Portal de gerenciamento](https://manage.windowsazure.com/)
2.  Clique em **Serviços de recuperação** no painel de navegação esquerdo e selecione o Cofre de backup que você criou. Clique no ícone de nuvem para ir para a exibição de início rápido do Cofre de backup. <br/> ![Visão geral][1]

3.  Na página início rápido, clique em **as credenciais do cofre Download**. O portal gera o arquivo de credencial de cofre que está disponível para download. <br/> ![Baixar][2]

4.  O portal gerará uma credencial de cofre usando uma combinação do nome do cofre e a data atual. Clique em **Salvar** para baixar as credenciais do cofre na pasta downloads da conta local, ou selecione Salvar como no menu Salvar para especificar um local para as credenciais do cofre.

## Observação
+ Desde março de 2015, os usuários não têm uma programação (por exemplo: PowerShell) maneira de baixar as credenciais do cofre.

+ Certifique-se de que as credenciais do cofre é salvo em um local que pode ser acessado do computador. Se ele é armazenado em um compartilhamento de arquivo/SMB, verifique as permissões de acesso.

+ O arquivo de credenciais do cofre é usado somente durante o fluxo de trabalho de registro.

+ O arquivo de credenciais do cofre expira após 48hrs e pode ser baixado a partir do portal.

+ Consulte o Azure Backup [FAQ](backup-azure-backup-faq.md) para todas as questões sobre o fluxo de trabalho.


<!--Image references-->
[1]: ./media/backup-azure-backup-download-vc/quickview.png
[2]: ./media/backup-azure-backup-download-vc/downloadvc.png

<!---HONumber=GIT-SubDir--> 