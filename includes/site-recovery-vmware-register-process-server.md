---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165844"
---
* Conecte-se à máquina virtual do Servidor de Processo usando a Conexão de Área de Trabalho Remota.
* Você pode iniciar o cspsconfigtool.exe clicando no atalho disponível na área de trabalho. (A ferramenta será iniciada automaticamente se essa for a primeira vez que estiver fazendo logon no servidor de processo).
  - Nome totalmente qualificado (FQDN) ou endereço IP do servidor de configuração
  - Porta na qual o servidor de configuração está escutando. O valor deve ser 443
  - Senha de Conexão para se conectar ao servidor de configuração.
  - Porta de Transferência de Dados a ser configurada para esse Servidor de Processo. Deixe o valor padrão como está a menos que você alterou-lo para um número de porta diferente em seu ambiente.

    ![Registrar o Servidor de Processo](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Clique no botão salvar para salvar a configuração e registrar o Servidor de Processo.
