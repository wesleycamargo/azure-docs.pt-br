---
title: Solucionar problemas do DPM (System Center Data Protection Manager) com Azure Backup | Microsoft Docs
description: Solucione problemas do System Center Data Protection Manager.
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Solucionar Problemas do System Center Data Protection Manager

Você pode encontrar as notas de versão mais recente do SC DPM [aqui](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016).
A matriz de suporte de proteção podem ser encontrada [aqui](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="replica-is-inconsistent"></a>A réplica está inconsistente

Esse erro pode ocorrer por vários motivos, como - falha do trabalho de criação de réplica, problemas com o diário de alterações, erros de bitmap de filtro de nível de volume, computador de origem foi desligado inesperadamente, estouro do log de sincronização ou a réplica está inconsistente realmente. Siga estas etapas para resolver esses problemas:
- Para remover o status inconsistente, execute a verificação de consistência manualmente ou agende a verificação de consistência diária.
- Verifique se você está usando a versão mais recente do servidor MAB ou System Center DPM
- Certifique-se de que a verificação de consistência automática está habilitada
- Tente reiniciar os serviços do Prompt de comando ("net stop dpmra" seguido de "net start dpmra")
- Verifique se os requisitos de largura de banda e conectividade de rede são atendidos
- Verifique se o computador de origem foi desligado inesperadamente
- Verifique se o disco está íntegro e há espaço suficiente para a réplica
- Certifique-se de que nenhum trabalho de backup duplicado é executado simultaneamente

## <a name="online-recovery-point-creation-failed"></a>Falha na Criação do Ponto de Recuperação Online

Siga estas etapas para resolver esses problemas:
- Verifique se você está na versão mais recente do Agente de Backup do Azure
- Tente criar manualmente um ponto de recuperação na área de tarefa proteção
- Certifique-se de que você execute a verificação de consistência na fonte de dados
- Verifique se os requisitos de largura de banda e conectividade de rede são atendidos
- Dados de réplica estão em estado inconsistente. Criar um ponto de recuperação de disco dessa fonte de dados
- Verifique se a réplica está presente e não ausente
- A réplica tem espaço suficiente para criar o diário de USN

## <a name="unable-to-configure-protection"></a>Não é possível configurar a proteção

Este erro aparece quando o servidor DPM não pode entrar em contato com o servidor protegido. Siga estas etapas para resolver esses problemas:
- Verifique se você está na versão mais recente do Agente de Backup do Azure
- Verifique se há conectividade (rede/firewall/proxy) entre o servidor DPM e o servidor protegido
- Se você estiver protegendo o SQL Server, certifique-se de que NT AUTHORITY\SYSTEM tenha sysadmin habilitado nas Propriedades de Logon

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Este servidor não está registrado no cofre especificado pelas credenciais de cofre

Este erro aparece quando o arquivo de credencial do cofre selecionado não pertence ao cofre dos Serviços de Recuperação associado ao Servidor de Backup do Azure / System Center DPM em que ocorre a tentativa de recuperação. Siga estas etapas para resolver esses problemas:
- Faça o download do arquivo de credencial de cofre do cofre dos Serviços de Recuperação em que o Servidor de Backup do Azure / System Center DPM está registrado.
- Tente registrar o servidor com o cofre usando o arquivo de credencial do cofre baixado mais recente.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor DPM
Este erro aparece quando não há outros Servidores de Backup do Azure registrados no cofre dos Serviços de Recuperação ou os servidores ainda não carregaram os metadados ou o servidor selecionado é não um Servidor de Backup do Azure / System Center DPM.
- Se houver outros Servidores de Backup do Azure / System Center DPM registrados no cofre dos Serviços de Recuperação, verifique se o agente do Backup do Azure mais recente está instalado.
- Se houver outros Servidores de Backup do Azure / System Center DPM registrados no cofre dos Serviços de Recuperação, aguarde um dia após a instalação para iniciar o processo de recuperação. O trabalho noturno carregará os metadados de todos os backups protegidos para a nuvem. Os dados estarão disponíveis para recuperação.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>A senha de criptografia fornecida não corresponde à senha associada ao seguinte servidor

> [!NOTE]
>Se você esqueceu/perder a senha de criptografia, há nenhuma opção para recuperar os dados. Sua única opção é regenerar a frase secreta e usar a criptografia de dados de backup futuros.
>
>

Este erro aparece quando a senha de criptografia usada no processo de criptografia de dados dos dados do Servidor de Backup do Azure / System Center DPM que estão sendo recuperados não corresponde à senha de criptografia fornecida. O agente não pode descriptografar os dados. Portanto, a recuperação falha. Siga estas etapas para resolver esses problemas:
- Forneça exatamente a mesma senha de criptografia associada ao Servidor de Backup do Azure / System Center DPM cujos dados serão recuperados. 
