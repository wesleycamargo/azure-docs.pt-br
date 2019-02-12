---
title: Backup e recuperação de dados para o Azure Stack com o serviço de Backup de infraestrutura | Microsoft Docs
description: Você pode fazer backup e restaurar a configuração e os dados de serviço usando o serviço de Backup de infraestrutura.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
mss.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 77493782802eeb29313b57d11442535f1734c12e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097378"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Backup e recuperação de dados para o Azure Stack com o serviço de Backup de infraestrutura

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode fazer backup e restaurar a configuração e os dados de serviço usando o serviço de Backup de infraestrutura. Cada instalação do Azure Stack contém uma instância do serviço. Você pode usar os backups criados pelo serviço para a reimplantação da nuvem do Azure Stack para restaurar dados do Azure Resource Manager, segurança e identidade.

Quando você estiver pronto para colocar sua nuvem em produção, você pode habilitar o backup. Não habilite o backup se você planeja executar testes e a validação por um longo período de tempo.

Antes de habilitar o serviço de backup, verifique se você tem [requisitos em vigor](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> O serviço de Backup de infraestrutura não inclui aplicativos e dados de usuário. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>O serviço de Backup de infraestrutura

Os serviços de contenham os seguintes recursos.

| Recurso                                            | DESCRIÇÃO                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Serviços de infraestrutura de backup                     | Coordene o backup em um subconjunto dos serviços de infraestrutura no Azure Stack. Se houver um desastre, os dados podem ser restaurados como parte da reimplantação. |
| Compactação e criptografia de dados exportados de Backup | Dados de backup são compactados e criptografados pelo sistema antes que ele seja exportado para o local de armazenamento externo fornecido pelo administrador.                |
| Monitoramento de trabalho de backup                              | Sistema notifica quando os trabalhos de backup falham e correção de etapas.                                                                                                |
| Experiência de gerenciamento de backup                       | RP de backup dá suporte à habilitação do backup.                                                                                                                         |
| Recuperação de nuvem                                     | Se houver uma perda de dados catastrófica, os backups podem ser usados para restaurar as informações do Azure Stack core como parte da implantação.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Verifique os requisitos para o serviço de Backup de infraestrutura

- **Localização de armazenamento**  
  Você precisa de um compartilhamento de arquivos acessíveis a partir do Azure Stack que pode conter sete backups. Cada backup é cerca de 10 GB. O compartilhamento deve ser capaz de armazenar 140 GB de backups. Para obter mais informações sobre como selecionar um local de armazenamento para o serviço de Backup de infraestrutura do Azure Stack, consulte [requisitos de controlador de Backup](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenciais**  
  Você precisa de uma conta de usuário de domínio e as credenciais, por exemplo, você pode usar as credenciais de administrador do Azure Stack.
- **Certificado de criptografia**  
  Arquivos de backup são criptografados usando a chave pública no certificado. Certifique-se de armazenar esse certificado em um local seguro. 


## <a name="next-steps"></a>Próximas etapas

Saiba como [habilitar o Backup para o Azure Stack a partir do portal de administração](azure-stack-backup-enable-backup-console.md).

Saiba como [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

Saiba como [fazer backup do Azure Stack](azure-stack-backup-back-up-azure-stack.md )

Saiba como [recuperar da perda de dados catastrófica](azure-stack-backup-recover-data.md)
