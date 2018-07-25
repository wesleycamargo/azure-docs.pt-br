---
title: Migrar servidores locais do Windows Server 2008 no Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar computadores locais do Windows Server 2008 para o Azure usando o Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: bsiva
ms.openlocfilehash: 0d3f28f0a9f1e9862fabb6ce5e96597f1534abd8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011394"
---
# <a name="migrate-servers-running-windows-server-2008-2008-r2-to-azure"></a>Migrar servidores que executam o Windows Server 2008 e 2008 R2 para o Azure

Este tutorial mostra como migrar servidores locais executando o Windows Server 2008 ou 2008 R2 para o Azure usando o Azure Site Recovery. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar o ambiente local para a migração
> * Configurar o ambiente de origem
> * Configurar uma política de replicação
> * Habilitar a replicação
> * Execute uma migração de teste para verificar se tudo está funcionando como esperado
> * Fazer o failover para o Azure e concluir a migração

A seção Limitações e problemas conhecidos lista algumas das limitações e soluções alternativas para problemas conhecidos que você pode encontrar ao migrar computadores do Windows Server 2008 para o Azure. 


## <a name="supported-operating-systems-and-environments"></a>Sistemas operacionais e ambientes compatíveis


|Sistema operacional  | Ambiente local  |
|---------|---------|
|Windows Server 2008 SP2 – 32 e 64 bits (IA-32 e x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMs VMware, VMs Hyper-V e servidores físicos    |
|Windows Server 2008 R2 SP1 – 64 bits</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMs VMware, VMs Hyper-V e servidores físicos|

> [!WARNING]
> - A migração de servidores que executam o Server Core não é um procedimento compatível.
> - Verifique se você tem o Service Pack e as atualizações do Windows mais recentes instalados antes de migrar.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, vale a pena examinar a arquitetura do Azure Site Recovery para [migração de servidores VMware e físicos](vmware-azure-architecture.md) ou [migração de máquinas virtuais Hyper-V](hyper-v-azure-architecture.md) 

Para migrar máquinas virtuais Hyper-V que executam o Windows Server 2008 ou o Windows Server 2008 R2, siga as etapas no tutorial [migrar máquinas locais para o Azure](migrate-tutorial-on-premises-azure.md).

O restante deste tutorial mostra como você pode migrar máquinas virtuais de VMware locais e servidores físicos que executam o Windows Server 2008 ou 2008 R2.


## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- O servidor de configuração, os servidores de processo adicionais e o serviço de mobilidade usados para migrar os servidores do Windows Server 2008 SP2 devem estar executando a versão 9.18.0.1 do software Azure Site Recovery. A instalação unificada para a versão 9.18.0.1 do Servidor de Configuração e o servidor de processo podem ser baixadas de [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup).

- Um Servidor de Configuração existente ou servidor de processo existente não pode ser usado para migrar servidores que executam o Windows Server 2008 SP2. Um novo Servidor de Configuração deve ser provisionado com a versão 9.18.0.1 do software Azure Site Recovery. Esse Servidor de Configuração só deve ser usado para migração de servidores do Windows para o Azure.

- Pontos de recuperação consistentes com o aplicativo e o recurso de consistência de várias VMs não são compatíveis com a replicação de servidores que executam o Windows Server 2008 SP2. Servidores do Windows Server 2008 SP2 devem ser migrados para um ponto de recuperação consistente com a falha. Os pontos de recuperação consistentes com a falha são gerados a cada cinco minutos por padrão. Usar uma política de replicação com uma frequência de instantâneos consistente com o aplicativo configurada fará com que a integridade de replicação se torne crítica devido à falta de pontos de recuperação consistentes com o aplicativo. Para evitar falsos positivos, defina a frequência de instantâneos consistentes com o aplicativo na política de replicação como "Desativada".

- Os servidores que estão sendo migrados devem ter o .NET Framework 3.5 Service Pack 1 para que o serviço de mobilidade funcione.


- Se o servidor tem discos dinâmicos, você pode perceber, em determinadas configurações, que esses discos no servidor com falha são marcados como offline ou mostrados como discos externos. Você também pode observar que o status do conjunto espelhado para volumes espelhados em discos dinâmicos é marcado como "Falha de redundância". Você pode corrigir esse problema em diskmgmt.msc importando esses discos e reativando-os, manualmente.

- Os servidores que estão sendo migrados devem ter o driver vmstorfl.sys. O failover pode falhar se o driver não está presente no servidor que está sendo migrado. 
  > [!TIP]
  >Verifique se o driver está presente em "C:\Windows\system32\drivers\vmstorfl.sys". Se o driver não for encontrado, você poderá contornar o problema criando um arquivo fictício em lugar dele. 
  >
  > Abra o prompt de comando (Executar > cmd) e execute o seguinte: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Talvez você não seja capaz de usar o RDP para servidores do Windows Server 2008 SP2 que executem o sistema operacional de 32 bits imediatamente após fazer failover ou failover de teste deles para o Azure. Do portal do Azure, reinicie a máquina virtual cujo failover foi realizado e tente se conectar novamente. Se você ainda não consegue se conectar, verifique se o servidor está configurado para permitir conexões de área de trabalho remota e verifique se há alguma regra de firewall ou grupos de segurança de rede bloqueando a conexão. 
  > [!TIP]
  > Um failover de teste é altamente recomendável antes de migrar servidores. Verifique se você executou pelo menos um failover de teste bem-sucedido em cada servidor que você está migrando. Como parte do failover de teste, conecte-se ao computador em que o failover de teste foi realizado e assegure-se de que as coisas funcionem conforme o esperado.
  >
  >A operação de failover de teste não causa interrupções e ajuda a testar migrações, criando máquinas virtuais em uma rede isolada de sua escolha. Ao contrário a operação de failover, durante a operação de failover de teste, a replicação de dados continua em andamento. Antes de estar pronto para migrar, você pode executar quantos failovers de teste desejar. 
  >
  >


## <a name="getting-started"></a>Introdução

Execute as tarefas a seguir para preparar o ambiente de VMware/físico local e de assinatura do Azure:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. Preparar o [VMware](vmware-azure-tutorial-prepare-on-premises.md) local


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **W2K8-migration**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **w2k8migrate**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Preparar o ambiente local para a migração

- Baixar o arquivo instalador da atualização no servidor de configuração (configuração unificada) de [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup)
- [Configure](physical-azure-disaster-recovery.md#set-up-the-source-environment) o ambiente de origem usando o arquivo do instalador baixado na etapa anterior.

> [!IMPORTANT]
> Verifique se você usa o arquivo de instalação baixado na primeira etapa acima para instalar e registrar o Servidor de Configuração. Não baixe o arquivo de instalação do portal do Azure. O arquivo de instalação disponível em [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) é a única versão que dá suporte à migração do Windows Server 2008.
>
> Você não pode usar um Servidor de Configuração existente para migrar as os computadores que executam o Windows Server 2008. Você precisará configurar um novo servidor de configuração usando o link fornecido acima.

 ![Baixar a chave do registro do cofre](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 
 

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação do Gerenciador de Recursos.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO (objetivo de pontos de recuperação). Um alerta será gerado se o RPO de replicação exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique qual será a duração (em horas) da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência de instantâneos consistentes com o aplicativo**, especifique **Desativado**. Clique em **OK** para criar a política.

A política é associada automaticamente ao servidor de configuração.

> [!WARNING]
> Verifique se você especificou **DESATIVADO** na definição frequência de instantâneos consistente com o aplicativo na política de replicação. Somente os pontos de recuperação consistentes com a falha são compatíveis ao replicar servidores que executam o Windows Server 2008. Especificar qualquer outro valor para a frequência de instantâneos consistentes com o aplicativo resultará em alertas falsos ao tornar crítica a integridade da replicação do servidor crítica devido à falta de pontos de recuperação consistentes com o aplicativo.

   ![Criar política de replicação](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Habilitar a replicação

[Habilitar a replicação](physical-azure-disaster-recovery.md#enable-replication) para o servidor do Windows Server 2008 SP2/Windows Server 2008 R2 SP1 a ser migrado.
   
   ![Adicionar servidor físico](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Habilitar a replicação](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Execute um teste de migração

Você poderá executar um failover de teste de servidores em replicação após a replicação inicial ser concluída e o status do servidor mudar para **Protegido**.

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.

   ![Failover de Teste](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará desligar o servidor antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
5. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso conclui o processo de migração, interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

   ![Migração completa](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Não cancelar um failover em andamento**: a replicação da VM é interrompida antes do início do failover. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.
