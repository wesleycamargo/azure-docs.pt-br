---
title: Exemplos do Azure PowerShell - Serviço de Aplicativo | Microsoft Docs
description: Exemplos do Azure PowerShell - Serviço de Aplicativo
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290732"
---
# <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell

A tabela a seguir inclui links para scripts do PowerShell compilados usando-se o Azure PowerShell.

| | |
|-|-|
|**Como criar o aplicativo**||
| [Criar um aplicativo com a implantação do GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure que recebe o código do GitHub. |
| [Criar um aplicativo com a implantação contínua do GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure que implanta continuamente o código do GitHub. |
| [Criar um aplicativo e implantar o código com FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure os carregue os arquivos de um diretório local usando FTP. |
| [Criar um aplicativo e implantar o código de um repositório Git local](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure e configura o push de código de um repositório Git local. |
| [Criar um aplicativo e implantar o código em um ambiente de preparo](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure com um slot de implantação para alterações de código de preparo. |
|**Como configurar o aplicativo**||
| [Mapear um domínio personalizado para um aplicativo](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure e mapeia um nome de domínio personalizado para ele. |
| [Associar um certificado SSL personalizado a um aplicativo](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure e associa o certificado SSL de um nome de domínio personalizado a ele. |
|**Como escalar um aplicativo**||
| [Dimensionar manualmente um aplicativo](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure e pode ser dimensionado em 2 instâncias. |
| [Escalar um aplicativo em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria dois aplicativos Web do Azure em duas regiões geográficas diferentes e os disponibiliza por meio de um único ponto de extremidade usando o Gerenciador de tráfego do Azure. |
|**Como conectar o aplicativo aos recursos**||
| [Conectar um aplicativo a um Banco de Dados SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure e um Banco de Dados SQL e, em seguida, adiciona a cadeia de conexão do banco de dados para as configurações do aplicativo. |
| [Conectar um aplicativo a uma conta de armazenamento](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo Web do Azure e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
|**Aplicativo de backup e restauração**||
| [Fazer backup de um aplicativo](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure e cria um backup único para ele. |
| [Criar um backup agendado para um aplicativo](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure e cria um backup agendado para ele. |
| [Excluir um backup de um aplicativo](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exclui um backup existente para um aplicativo. |
| [Restaurar um aplicativo de um backup](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo de um backup concluído anteriormente. |
| [Restaurar um backup entre assinaturas](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo Web de um backup em outra assinatura. |
|**Como monitorar o aplicativo**||
| [Monitorar um aplicativo com logs do servidor Web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo Web do Azure, habilita o registro em log para ele e baixa os logs em sua máquina local. |
| | |
