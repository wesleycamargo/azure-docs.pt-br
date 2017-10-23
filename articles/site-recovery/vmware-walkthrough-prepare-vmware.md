---
title: "Preparar recursos do VMware locais para replicação no Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para replicar as cargas de trabalho em execução em VMs VMware no armazenamento do Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>Etapa 6: Preparar a replicação da VMware local para o Azure

Use as instruções neste artigo para preparar servidores VMware locais para interagir com o Azure Site Recovery e preparar as máquinas virtuais VMWare para a instalação do serviço Mobilidade. O agente de serviço Mobilidade deve ser instalado em todas as VMs locais que você deseja replicar no Azure.

## <a name="prepare-for-automatic-discovery"></a>Preparar para a descoberta automática

O Site Recovery detecta automaticamente as VMs em execução em hosts ESXi vSphere (com ou sem um servidor do vCenter). Para a descoberta automática, o Site Recovery precisa de uma conta para acessar os hosts e servidores:

1. Para usar uma conta dedicada, crie uma função (no nível do vCenter com as permissões, conforme descrito na tabela abaixo. Use um nome como **Azure_Site_Recovery**.
2. Em seguida, crie um usuário no host vSphere/servidor vCenter e atribua a função ao usuário. Você especifica essa conta de usuário durante a implantação de Site Recovery.


### <a name="vmware-account-permissions"></a>Permissões de conta do VMware

A Site Recovery precisa de acesso ao VMware para que o servidor de processo descubra automaticamente as VMs e para failover e failback de VMs.

- **Migrar**: se você quiser migrar VMs VMware para o Azure, sem nunca realizar failback novamente, poderá usar uma conta do VMware com uma função somente leitura. Essa função pode executar o failover, mas não pode desligar máquinas de origem protegidas. Isso não é necessário para a migração.
- **Replicação/recuperação**: se você desejar implantar a replicação completa (replicar, failover e failback) a conta deverá ser capaz de executar operações como criar e remover discos, ativar VMs etc.
- **Descoberta automática**: é necessário ter pelo menos uma conta somente leitura.


**Tarefa** | **Conta/função necessária** | **Permissões** | **Detalhes**
--- | --- | --- | ---
**Servidor de processo descobre automaticamente as VMs VMware** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Failover** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.
**Failover e failback** | Sugerimos que você crie uma função (Azure_Site_Recovery) com as permissões necessárias e atribua a função a um usuário ou grupo do VMware | Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>Preparar para instalação do Serviço de Mobilidade por push

O serviço de Mobilidade deve ser instalado em todas as VMs que você deseja replicar. Há várias maneiras de instalar o serviço, incluindo a instalação manual, a instalação por push do servidor de processo do Site Recovery e a instalação usando métodos como o System Center Configuration Manager.

Se você quiser usar a instalação por push, precisará preparar uma conta que o Site Recovery possa usar para acessar a VM.

- Você pode usar uma conta local ou de domínio
- Para Windows, se não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
- Se deseja adicionar a entrada do Registro do Windows de uma CLI, digite: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, a conta deve ser a raiz no servidor Linux de origem.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 7: Criar um cofre](vmware-walkthrough-create-vault.md)
