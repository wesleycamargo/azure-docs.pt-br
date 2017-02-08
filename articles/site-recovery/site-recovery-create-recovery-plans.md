---
title: "Criar planos de recuperação | Microsoft Docs"
description: "Crie planos de recuperação com o Azure Site Recovery para executar o failover e recuperar grupos de máquinas virtuais e servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3cc2aa0ade25417a1e2a8fb96fc3a059349afa99


---
# <a name="create-recovery-plans"></a>Criar planos de recuperação
O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

## <a name="overview"></a>Visão geral
Este artigo fornece informações sobre como criar e personalizar planos de recuperação. 

Os planos de recuperação são formados por um ou mais grupos ordenados que contêm máquinas virtuais ou servidores físicos dos quais você deseja realizar o failover juntos. Os planos de recuperação têm a seguinte função:

* Definir grupos de máquinas que fazem failover e são inicializadas juntas.
* Modelam dependências entre computadores agrupando-os em um grupo de planos de recuperação. Por exemplo, se você deseja fazer failover de um aplicativo específico e ativá-lo, agrupe as máquinas virtuais para esse aplicativo no mesmo grupo de planos de recuperação.
* Automatizam e estendem o failover. Você pode executar um failover de teste planejado ou não planejado em um plano de recuperação. É possível personalizar planos de recuperação com scripts, automação do Azure e ações manuais.

Os planos de recuperação são exibidos em **Planos de Recuperação** no portal de Recuperação de Site.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar
Observe o seguinte:

* Um plano de recuperação não deve misturar VMs com adaptadores de rede individuais e múltiplos. Isso porque a combinação dessas VMs não é permitida em um serviço de nuvem do Azure.
* Você pode ampliar os planos de recuperação com scripts e ações manuais. Observe o seguinte:
  
  * Escreva scripts usando o Windows PowerShell.
  * Certifique-se de que os scripts usem blocos try-catch para que as exceções sejam tratadas normalmente. Se houver uma exceção no script, ele será interrompido e a tarefa será mostrada como com falha.  Se ocorrer um erro, qualquer parte restante do script não será executada. Se isso ocorrer quando você estiver executando um failover não planejado, o plano de recuperação continuará. Se isso ocorrer quando você estiver executando um failover planejado, o plano de recuperação será interrompido. Se isso ocorrer, corrija o script, verifique se ele funciona conforme esperado e execute novamente o plano de recuperação.
  * O comando Write-Host não funciona em um script de plano de recuperação e o script falhará. Se deseja criar uma saída, crie um script de proxy que, por sua vez, execute seu script principal, e garanta que toda a saída seja direcionada usando o comando >>.
  * O script expira se não retornar em até 600 segundos.
  * Se nada for escrito em STDERR, o script será classificado como com falha. Essa informação será exibida nos detalhes de execução do script.
  * Se você estiver usando a VMM em sua implantação, observe o seguinte:
    
    * Os scripts em um plano de recuperação são executados no contexto da conta de Serviço VMM. Verifique se essa conta tem permissões de Leitura no compartilhamento remoto no qual o script está localizado e teste o script a ser executado no nível de privilégio da conta de serviço VMM.
    * Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo VMM do Windows PowerShell é instalado quando você instala o console do VMM. O módulo VMM pode ser carregado em seu script usando o comando a seguir no script: Import-Module-Name virtualmachinemanager. [Obtenha mais detalhes](hhttps://technet.microsoft.com/library/hh875013.aspx).
    * Verifique se você possui pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento da biblioteca de um servidor VMM pode ser encontrado localmente no servidor VMM com o nome de pasta MSCVMMLibrary.
    * Se o caminho de compartilhamento da biblioteca for remoto (ou local, mas não compartilhado com MSCVMMLibrary), configure o compartilhamento da seguinte forma (usando \\libserver2.contoso.com\share\ como um exemplo):
      * Abra o Editor do Registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
      * Edite o valor de ScriptLibraryPath e coloque o valor como \\libserver2.contoso.com\share\. Especifique o FQDN completo. Forneça permissões para o local de compartilhamento.
      * Não de esqueça de testar o script com uma conta de usuário que tenha as mesmas permissões que a conta de Serviço VMM, a fim de garantir que scripts autônomos testados sejam executados da mesma maneira que serão nos planos de recuperação. No servidor VMM, defina a política de execução a ser ignorada da seguinte maneira:
        * Abra o console do Windows PowerShell de 64 bits usando privilégios elevados.
        * Digite: **Set-executionpolicy bypass**. [Obtenha mais detalhes](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação
O modo no qual você cria um plano de recuperação depende da sua implantação de Recuperação de Site.

* **Replicando VMs VMware e servidores físicos**—Crie um plano e adicione grupos de replicação que contêm máquinas virtuais e servidores físicos a um plano de recuperação.
* **Replicando VMs Hyper-V (em nuvens da VMM)**—Crie um plano e adicione máquinas virtuais Hyper-V protegidas de uma nuvem VMM a um plano de recuperação.
* **Replicando VMs Hyper-V (não em nuvens da VMM)**—Crie um plano e adicione máquinas virtuais Hyper-V protegidas de um grupo de proteção a um plano de recuperação.
* **Replicação SAN**—Crie um plano e adicione um grupo de replicação que contenha máquinas virtuais ao plano de recuperação. Selecione um grupo de replicação em vez de máquinas virtuais específicas, pois todas as máquinas virtuais em um grupo de replicação devem fazer failover juntas (o failover ocorre primeiro na camada de armazenamento).

Crie um plano de recuperação da seguinte forma:

1. Clique na guia **Planos de Recuperação** > **Criar Plano de Recuperação**.
   Especifique um nome para o plano de recuperação, bem como uma origem e um destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação.
   
   * Se você estiver replicando de um VMM para outro VMM, selecione **Tipo de Origem** > **VMM** e os servidores VMM de origem e de destino. Clique em **Hyper-V** para ver as nuvens que estão configuradas para usar a réplica do Hyper-V. 
   * Se estiver replicando de um VMM para outro VMM usando SAN, selecione **Tipo de Origem** > **VMM** e os servidores VMM de origem e de destino. Clique em **SAN** para ver as nuvens que estão configuradas para a replicação SAN.
   * Se estiver replicando de um VMM para o Azure, selecione **Tipo de Origem** > **VMM**.  Selecione o servidor do VMM de origem e **Azure** como o destino.
   * Se estiver replicando de um site do Hyper-V, selecione **Tipo de Origem** > **Site do Hyper-V**. Selecione o site como a origem e **Azure **como o destino.
   * Se estiver replicando do site VMware ou de um servidor local físico no Azure, selecione um servidor de configuração como a origem e **Azure** como o destino.
2. Em **Selecionar máquinas virtuais** , selecione as máquinas virtuais (ou o grupo de replicação) que deseja adicionar ao grupo padrão (Grupo 1) no plano de recuperação.

## <a name="customize-recovery-plans"></a>Personalizar planos de recuperação
Depois de adicionar máquinas virtuais protegidas ou grupos de replicação ao grupo de planos de recuperação padrão e criar o plano, você pode personalizá-lo:

* **Adicionar novos grupos**: você pode incluir grupos de plano de recuperação adicionais. Os grupos adicionados são numerados na ordem em que você os adiciona. É possível adicionar até sete grupos. Você pode adicionar mais computadores ou grupos de replicação a esses novos grupos. Observe que uma máquina virtual ou um grupo de replicação pode ser incluído apenas em um grupo de planos de recuperação.
* **Adicionar um script **— você pode adicionar scripts antes ou depois de um grupo de planos de recuperação. Quando você adiciona um script, este adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas do Grupo 1 será criado com o nome: Grupo 1: pré-etapas. Todas as pré-etapas serão listadas dentro desse conjunto. Observe que só será possível adicionar um script no site primário se você tiver um servidor VMM implantado.
* **Adicionar uma ação manual**: você pode adicionar ações manuais que são executadas antes ou depois de um grupo de planos de recuperação. Quando o plano de recuperação é executado, ele é interrompido no ponto em que você inseriu a ação manual, e uma caixa de diálogo solicita que você especifique que a ação manual foi concluída.

## <a name="extend-recovery-plans-with-scripts"></a>Estender os planos de recuperação com scripts
Você pode adicionar um script ao seu plano de recuperação:

* Se você tiver um site de origem do VMM, será possível criar um script no servidor VMM e incluí-lo no plano de recuperação.
* Se estiver replicando no Azure, você poderá integrar runbooks de automação do Azure ao plano de recuperação

### <a name="create-a-vmm-script"></a>Criar um script do VMM
Crie o script da seguinte maneira:

1. Crie uma nova pasta no compartilhamento de biblioteca, por exemplo \<NomeDoServidorVMM> \MSSCVMMLibrary\RPScripts. Coloque-a nos servidores VMM de origem e de destino.
2. Crie o script (por exemplo, RPScript) e verifique se ele funciona conforme esperado.
3. Coloque o script no local \<NomeDoServidorVMM>\MSSCVMMLibrary nos servidores VMM de origem e de destino.

### <a name="create-an-azure-automation-runbook"></a>Criar um runbook da automação do Azure
Você pode estender o seu plano de recuperação executando um runbook da automação do Azure como parte do plano. [Leia mais](site-recovery-runbook-automation.md).

### <a name="add-custom-settings-to-a-recovery-plan"></a>Adicionar configurações personalizadas a um plano de recuperação
1. Abra o plano de recuperação que deseja personalizar.
2. Clique para adicionar máquinas virtuais ou um novo grupo.
3. Para adicionar um script ou ação manual, clique em qualquer item na lista **Etapa** e clique em **Script** ou **Ação Manual**. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Use os botões de comando **Mover para Cima** e **Mover para Baixo** para mover o script para cima ou para baixo.
4. Se estiver adicionando um script do VMM, selecione **Failover no script do VMM** e, em **Caminho do Script**, digite o caminho relativo para o compartilhamento. Portanto, em nosso exemplo, onde o compartilhamento está localizado em \\<VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
5. Se estiver adicionando um runboook da automação do Azure, especifique a **Conta de Automação do Azure** na qual o runbook está localizado e selecione o **Script de Runbook do Azure** apropriado.
6. Faça um failover do plano de recuperação para garantir que o script funciona conforme esperado.

## <a name="next-steps"></a>Próximas etapas
É possível executar tipos diferentes de failovers no de plano de recuperação, incluindo um failover de teste para verificar seu ambiente, bem como failovers planejados ou não planejados. [Saiba mais](site-recovery-failover.md).




<!--HONumber=Nov16_HO3-->


