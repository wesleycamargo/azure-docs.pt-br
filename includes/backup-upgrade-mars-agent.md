---
title: Upgrade do agente de Backup do Azure
description: Essas informações explicam por que você deve fazer upgrade do agente de Backup do Azure e onde baixar o upgrade.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750323"
---
## <a name="upgrade-the-mars-agent"></a>Fazer upgrade do agente MARS
As versões do Agente MARS (Serviço de Recuperação do Microsoft Azure) abaixo de 2.0.9083.0 dependem do ACS (Serviço de Controle de Acesso do Microsoft Azure). Em 2018, o Azure [ACS (Serviço de Controle de Acesso do Microsoft Azure)](../articles/active-directory/develop/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 experimentarão falhas de backup. Para evitar ou resolver falhas de backup, [faça upgrade do seu Agente MARS para a última versão](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar os servidores que exigem uma atualização do Agente MARS, siga as etapas no [Blog de backup para fazer upgrade dos agentes de Backup do Azure](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). O agente MARS é utilizado para fazer backup dos seguintes tipos de dados no Azure:
- Arquivos 
- Dados de estado do sistema
- fazer backup do System Center DPM para o Azure
- Servidor de Backup do Azure (MABS)
