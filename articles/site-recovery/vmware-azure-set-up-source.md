---
title: Configurar o ambiente de origem para recuperação de desastre de VMs do VMware no Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar seu ambiente local para recuperação de desastre de VMs do VMware no Azure com o Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: 44b1cac461fa49b4bea19b7cf98f6038eb264492
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230719"
---
# <a name="set-up-the-source-environment-for-disaster-recovery-of-vmware-vms-to-azure"></a>Configurar o ambiente de origem para recuperação de desastre de VMs do VMware no Azure

Este artigo descreve como configurar seu ambiente local de origem para replicar máquinas virtuais VMware no Azure. Ele inclui etapas para selecionar o cenário de replicação, configuração de um computador local como o servidor de configuração de Recuperação de Site, e descobrir automaticamente as VMs locais. 

## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe que você já tenha:

- Planeje sua implantação com a ajuda do [Planejador de Implantações do Azure Site Recovery](site-recovery-deployment-planner.md). Isso ajuda você a alocar largura de banda suficiente, com base em sua taxa diária de alteração de dados, a fim de atender ao RPO (objetivo de ponto de recuperação) desejado.
- [Configurar recursos](tutorial-prepare-azure.md) no [portal do Azure](http://portal.azure.com).
- [Configurar o VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluindo uma conta dedicada para a descoberta automática.

## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. Em **Cofres dos Serviços de Recuperação**, selecione o nome do cofre. Estamos usando **ContosoVMVault** para este cenário.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Você pode configurar o servidor de configuração como uma VM do VMware local por meio de um modelo de OVA (Open Virtualization Application). [Saiba mais](concepts-vmware-to-azure-architecture.md) sobre os componentes que serão instalados na VM do VMware.

1. Saiba mais sobre os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) para implantação de servidor de configuração.
2. [Verificar números de capacidade](vmware-azure-deploy-configuration-server.md#capacity-planning) para implantação.
3. [Baixar](vmware-azure-deploy-configuration-server.md#download-the-template) e [importar](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVA para configurar um VM do VMware local que executa o servidor de configuração. A licença fornecida com o modelo OVA é uma licença de avaliação e tem validade de 180 dias. Após esse período, o cliente precisa ativar o Windows com uma licença adquirida.
4. Ativar a VM do VMware e [registre-o](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) no cofre dos Serviços de Recuperação.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Exclusões de pasta do Azure Site Recovery do programa Antivírus

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Se o software antivírus estiver ativo no computador de origem

Se o computador de origem tiver um software antivírus ativo, a pasta de instalação deverá ser excluída. Então, exclua a pasta *C:\ProgramData\ASR\agent* para ter uma replicação tranquila.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Se o software antivírus estiver ativo no servidor de configuração

Exclua as seguintes pastas do software antivírus para ter uma replicação tranquila e para evitar problemas de conectividade

 1. C:\Arquivos de Programas\Agente dos Serviços de Recuperação do Microsoft Azure.
 2. C:\Arquivos de Programas\Provedor do Microsoft Azure Site Recovery
 3. C:\Arquivos de Programas\Gerente de Configuração do Microsoft Azure Site Recovery 
 4. C:\Arquivos de Programas\Ferramenta de Coleta de Erro do Microsoft Azure Site Recovery 
 5. C:\thirdparty
 6. C:\Temp
 7. C:\strawberry
 8. C:\ProgramData\MySQL
 9. C:\Arquivos de Programas (x86)\MySQL
 10. C:\ProgramData\ASR
 11. C:\ProgramData\Microsoft Azure Site Recovery
 12. C:\ProgramData\ASRLogs
 13. C:\ProgramData\ASRSetupLogs
 14. C:\ProgramData\LogUploadServiceLogs
 15. C:\inetpub
 16. Diretório de instalação de servidor do ASR. Por exemplo: E:\Arquivos de Programas (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Se o software antivírus estiver ativo no servidor de processo/destino mestre em expansão

Exclua estas pastas do software antivírus

1. C:\Arquivos de Programas\Microsoft Azure Recovery Services Agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Diretório de instalação do servidor de processo, de balanceamento de carga de ASR, exemplo: C:\Arquivos de Programas (x86) \Microsoft Azure Site Recovery

## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Próximas etapas
[Configurar o ambiente de destino](./vmware-azure-set-up-target.md) no Azure.
