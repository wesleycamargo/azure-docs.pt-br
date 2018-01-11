---
title: "Backup e recuperação de dados para a pilha do Azure com o serviço de Backup de infraestrutura | Microsoft Docs"
description: "Você pode fazer backup e restaurar dados de configuração e serviço usando o serviço de Backup de infraestrutura."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 84ce0d72ff826ecb3f5deff165db00a1e50ae89d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Backup e recuperação de dados para a pilha do Azure com o serviço de Backup de infraestrutura

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode fazer backup e restaurar dados de configuração e serviço usando o serviço de Backup de infraestrutura. Cada instalação de pilha do Azure contém uma instância do serviço. Você pode usar os backups criados pelo serviço de reimplantação da pilha de nuvem do Azure para restaurar dados do Gerenciador de recursos do Azure, segurança e identidade.

Você pode habilitar o backup quando você estiver pronto para colocar sua nuvem em produção. Não habilite o backup se você planeja executar os testes e a validação por um longo período de tempo.

Antes de habilitar o serviço de backup, verifique se você tem [requisitos in-loco](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> A infraestrutura de serviço de Backup não inclui aplicativos e dados de usuário. Consulte os seguintes artigos para obter instruções sobre como fazer backup e restauração [serviços de aplicativos](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), e [MySQL](https://aka.ms/azure-stack-mysql) provedores de recursos e dados de usuário associado.

## <a name="the-infrastructure-backup-service"></a>O serviço de Backup de infraestrutura

Os serviços contém os seguintes recursos.

| Recurso                                            | DESCRIÇÃO                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Serviços de infraestrutura de backup                     | Coordene o backup em um subconjunto de serviços de infraestrutura na pilha do Azure. Se houver um desastre, os dados podem ser restaurados como parte da reimplantação. |
| Compactação e criptografia de dados exportados de Backup | Dados de backup são compactados e criptografados pelo sistema antes de ser exportado para o local de armazenamento externo fornecido pelo administrador.                |
| Monitoramento de trabalho de backup                              | Sistema notifica quando etapas de falhas e correção de trabalhos de backup.                                                                                                |
| Experiência de gerenciamento de backup                       | RP backup oferece suporte à habilitação de backup.                                                                                                                         |
| Recuperação de nuvem                                     | Se houver uma perda de dados catastrófica, os backups podem ser usados para restaurar as informações de pilha do Azure core como parte da implantação.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Verificar os requisitos para o serviço de Backup de infraestrutura

- **Local de armazenamento**  
  Você precisa de um compartilhamento de arquivos acessível da pilha do Azure que pode conter sete backups. Cada backup é cerca de 10 GB. O compartilhamento deve ser capaz de armazenar 140 GB de backups. Para obter mais informações sobre como selecionar um local de armazenamento para o serviço de Backup de infraestrutura de pilha do Azure, consulte [requisitos de Backup controlador](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenciais**  
  Você precisa de uma conta de usuário de domínio e as credenciais, por exemplo, você pode usar as credenciais de administrador de pilha do Azure.
- **Chave de criptografia**  
  Arquivos de backup são criptografados usando essa chave. Certifique-se de armazenar essa chave em um local seguro. Depois que você definir essa chave pela primeira vez ou gira a chave no futuro, você não pode exibir essa chave desta interface. Para obter mais instruções gerar uma chave pré-compartilhada, execute os scripts em [habilitar o Backup para a pilha do Azure com o PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [habilitar o Backup para a pilha do Azure no portal de administração](azure-stack-backup-enable-backup-console.md).
- Saiba como [habilitar o Backup para a pilha do Azure com o PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Saiba como [pilha do Azure](azure-stack-backup-back-up-azure-stack.md )
- Saiba como [recuperação da perda de dados catastrófica de](azure-stack-backup-recover-data.md)
