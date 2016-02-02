<properties
	pageTitle="Preparar o ambiente para fazer backup do Servidor Windows ou do Cliente Windows | Microsoft Azure"
	description="Prepare o ambiente para fazer backup do Windows criando um cofre de backup, baixando credenciais e instalando o agente de backup."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="cofre de backup; agente de backup; backup do Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# Preparar o seu ambiente para o backup de computadores com o Windows
Este artigo o ajudará a se preparar para fazer backup de um Servidor Windows ou de um Cliente Windows no Azure. Para fazer isso, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

Se você já fez isso, pode iniciar o [backup das máquinas Windows](backup-azure-backup-windows-server.md). Caso contrário, prossiga com as etapas abaixo para verificar se seu ambiente está preparado.

>[AZURE.NOTE] Anteriormente, você precisaria criar ou adquirir um certificado X.509 v3, a fim de registrar seu servidor de backup. Os certificados ainda têm suporte, mas agora para facilitar o registro do cofre do Azure em um servidor você pode gerar uma credencial diretamente da página Início Rápido.

## Antes de começar
Para fazer backup de arquivos e de dados do seu Windows Server no Azure, primeiro você deve:

- **Criar um cofre de Backup** — crie um cofre no [portal de gerenciamento do Backup do Azure](http://manage.windowsazure.com).
- **Baixar credenciais do cofre** — na página do painel no cofre do Backup do Azure, baixe as credenciais do cofre que serão usadas para registrar o computador com Windows no cofre de backup.
- **Instalar o agente de Backup do Azure e registrar o servidor** — na página Painel, clique no link para baixar o [agente de Backup do Azure](http://aka.ms/azurebackup_agent). Instale o agente e registre o servidor no cofre de backup usando as credenciais do cofre.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Próximas etapas
- [Fazer backup do Windows Server ou do Windows Client](backup-azure-backup-windows-server.md)
- [Gerenciar o Windows Server ou o Windows Client](backup-azure-manage-windows-server.md)
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
- [Fórum de Backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0128_2016-->