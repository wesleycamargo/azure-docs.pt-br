<properties
	pageTitle="Configurar o Backup do Azure para prepará-lo para o backup do Windows Server | Microsoft Azure"
	description="Use este tutorial para aprender a usar o serviço de backup na oferta de nuvem do Microsoft Azure para fazer o backup do Windows Server na nuvem."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="11/03/2015" ms.author="jimpark"; "aashishr"/>

# Preparar o seu ambiente para o backup de computadores com o Windows

Este artigo guiará você pela habilitação do recurso Backup do Azure. Para fazer backup do Windows Server ou do Windows Client para o Azure, você precisará de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
>[AZURE.NOTE]Anteriormente, você precisaria criar ou adquirir um certificado X.509 v3, a fim de registrar seu servidor de backup. Os certificados ainda têm suporte, mas agora para facilitar o registro do cofre do Azure em um servidor você pode gerar uma credencial diretamente da página Início Rápido.

## Antes de começar
Para fazer backup de arquivos e de dados do seu Windows Server no Azure, primeiro você deve:

- **Crie um cofre de Backup** — Crie um cofre no console do Backup do Azure.
- **Baixe credenciais do cofre** — No Backup do Azure, carregue o certificado de gerenciamento que você criou para o cofre.
- **Instalar o Agente de Backup do Azure e registrar o servidor** — no Backup do Azure, instale o agente e registre o servidor no cofre de backup.

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## Próximas etapas
- [Fazer backup do Windows Server ou do Windows Client](backup-azure-backup-windows-server.md)
- [Gerenciar o Windows Server ou o Windows Client](backup-azure-manage-windows-server.md)
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
- [Fórum de Backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=Nov15_HO3-->