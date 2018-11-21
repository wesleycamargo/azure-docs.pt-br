---
title: Solucionar problemas com o servidor de configuração durante a recuperação de desastres de VMs VMware e servidores físicos para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre solução de problemas para implantar o servidor de configuração para recuperação de desastres de VMs VMware e servidores físicos no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: ramamill
ms.openlocfilehash: b819783d127c51c0d5f33b2273a37a4180cb13a6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569729"
---
# <a name="troubleshoot-configuration-server-issues"></a>Solucionar problemas do servidor de configuração

Este artigo ajuda a solucionar problemas ao implantar e gerenciar o [Servidor de configuração do Azure Site Recovery](site-recovery-overview.md). O servidor de configuração é usado ao configurar a recuperação de desastres para VMs VMware locais e servidores físicos para o Azure usando o Site Recovery. 

## <a name="installation-failures"></a>Falhas de instalação

| **Mensagem de erro** | **Ação recomendada** |
|--------------------------|------------------------|
|ERRO   Falha ao carregar Contas. Erro: System.IO.IOException: não é possível ler dados da conexão de transporte ao instalar e registrar o servidor CS.| Verifique se TLS 1.0 está habilitado no computador. |

## <a name="registration-failures"></a>Falhas de registro

Falhas de registro podem ser depuradas usando o l < br/ogs na pasta %ProgramData%\ASRLogs.

As falhas de registro podem ser depuradas examinando os logs da pasta **%ProgramData%\ASRLogs**.

| **Mensagem de erro** | **Ação recomendada** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Mensagem: ACS50008: token SAML é inválido.<br>ID de rastreamento: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de correlação: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Carimbo de data/hora: **2016-12-12 14:50:08Z<br>** | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|**09:35:27** : DRRegistrationException ao tentar obter todo o cofre de recuperação de desastre para o certificado selecionado: : Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException lançado, Exception.Message: ACS50008: o token SAML é inválido.<br>ID de rastreamento: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de correlação: abe9deb8-3e64-464d-8375-36db9816427a<br>Carimbo de data/hora: **2016-05-19 01:35:39Z**<br> | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|06:28:45: Falha ao criar certificado<br>06:28:45: A instalação não pode continuar. Não é possível criar um certificado necessário para autenticar a Recuperação de Site. Executar a instalação novamente | Verifique se você está executando a instalação como um administrador local. |
