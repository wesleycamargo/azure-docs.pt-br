<properties
	pageTitle="Criando planos de recuperação" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores locais no Azure ou em um armazenamento de dados secundário." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/07/2015" 
	ms.author="raynew"/>

# Criando planos de recuperação

O serviço Recuperação de Site contribui para uma solução robusta de BCDR (continuidade dos negócios e recuperação de desastres) que protege servidores físicos locais e máquinas virtuais, administrando e automatizando a replicação e o failover no Azure ou em um datacenter local secundário. Para obter uma introdução aos cenários de implantação de Recuperação de Site, leia [Visão geral da Recuperação de Site](site-recovery-overview.md).

## Sobre este artigo

O artigo fornece informações sobre como criar e personalizar planos de recuperação.

Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Visão geral

Os planos de recuperação consistem em um ou mais grupos ordenados que contêm máquinas virtuais ou grupos de replicação (para replicação de SAN) protegidos. Os computadores fazem failover de acordo com o grupo em que estão. As máquinas virtuais em um determinado grupo fazem failover paralelamente. Os planos de recuperação têm a seguinte função:

- Definem grupos de computadores que fazem failover e são inicializados juntos.
- Modelam dependências entre computadores agrupando-os em um grupo de planos de recuperação. Por exemplo, se você deseja fazer failover de um aplicativo específico e ativá-lo, agrupe as máquinas virtuais para esse aplicativo no mesmo grupo de planos de recuperação.
- Automatizam e estendem o failover. Você pode executar um failover de teste planejado ou não planejado em um plano de recuperação. É possível personalizar planos de recuperação com scripts, automação do Azure e ações manuais.

Os planos de recuperação são exibidos em **Planos de Recuperação** no portal de Recuperação de Site.


## Criar planos de recuperação

O modo no qual você cria um plano de recuperação depende da sua implantação de Recuperação de Site.

- **Replicação do Hyper-V (VMM)**: se você estiver replicando de um site do VMM em um site local secundário ou no Azure usando a replicação do Hyper-V, adicione máquinas virtuais do Hyper-V protegidas de uma nuvem VMM a um plano de recuperação.
- **Replicação do Hyper-V (site do Hyper-V)**: se você estiver replicando de um site do Hyper-V (sem um servidor VMM) no Azure, adicione máquinas virtuais do Hyper-V protegidas de um grupo de proteção a um plano de recuperação.
- **Replicação SAN**: se você estiver replicando em um site local secundário usando a replicação SAN, adicione um grupo de replicação que contenha máquinas virtuais ao plano de recuperação. Selecione um grupo de replicação em vez de máquinas virtuais específicas, pois todas as máquinas virtuais em um grupo de replicação devem fazer failover juntas (o failover ocorre primeiro na camada de armazenamento).
- **Replicação VMware**: se estiver replicando máquinas virtuais VMware no Azure, adicione grupos de replicação que contenham máquinas virtuais a um plano de recuperação.

Crie um plano de recuperação da seguinte forma:

1. Na guia Planos de Recuperação, clique em Criar Plano de Recuperação. Especifique um nome para o plano de recuperação, bem como uma origem e um destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação.

	- Se estiver replicando do VMM no VMM, selecione VMM em **Tipo de Origem** e os servidores VMM de origem e de destino. Clique em **Hyper-V** para ver as nuvens que estão configuradas para usar a réplica do Hyper-V. 
	- Se estiver replicando do VMM no VMM usando SAN, selecione VMM em **Tipo de Origem** e os servidores VMM de origem e de destino. Clique em **SAN** para ver as nuvens que estão configuradas para a replicação SAN.
	- Se estiver replicando do VMM no Azure, selecione VMM em **Tipo de Origem**. Selecione o servidor do VMM de origem e **Azure** como o destino.
	- Se estiver replicando de um site do Hyper-V, selecione site do Hyper-V em Tipo de Origem. Selecione o site como a origem e **Azure **como o destino.
	- Se estiver replicando do site VMware ou de um servidor local físico no Azure, selecione um servidor de configuração como a origem e **Azure** como o destino.

2\. Em **Selecionar máquinas virtuais**, selecione as máquinas virtuais (ou o grupo de replicação) que deseja adicionar ao grupo padrão (Grupo 1) no plano de recuperação.

## Personalizar planos de recuperação

Depois de adicionar máquinas virtuais protegidas ou grupos de replicação ao grupo de planos de recuperação padrão e criar o plano, você pode personalizá-lo:

- **Adicionar novos grupos**: você pode incluir grupos de plano de recuperação adicionais. Os grupos adicionados são numerados na ordem em que você os adiciona. É possível adicionar até sete grupos. Você pode adicionar mais computadores ou grupos de replicação a esses novos grupos. Observe que uma máquina virtual ou um grupo de replicação pode ser incluído apenas em um grupo de planos de recuperação.
- **Adicionar um script **: você pode adicionar scripts antes ou depois de um grupo de planos de recuperação. Quando você adiciona um script, este adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas do Grupo 1 será criado com o nome: Grupo 1: pré-etapas. Todas as pré-etapas serão listadas dentro desse conjunto. Observe que só será possível adicionar um script no site primário se você tiver um servidor VMM implantado.
- **Adicionar uma ação manual**: você pode adicionar ações manuais que são executadas antes ou depois de um grupo de planos de recuperação. Quando o plano de recuperação é executado, ele é interrompido no ponto em que você inseriu a ação manual, e uma caixa de diálogo solicita que você especifique que a ação manual foi concluída.

### Estender os planos de recuperação com scripts

Você pode adicionar um script ao seu plano de recuperação:

Se você tiver um site de origem do VMM, será possível criar um script no servidor VMM e incluí-lo no plano de recuperação. Se estiver replicando no Azure, você poderá integrar runbooks de automação do Azure ao plano de recuperação

#### Criar um script do VMM

Antes de começar, observe o seguinte:

- Escreva scripts usando o Windows PowerShell.
- Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo VMM do Windows PowerShell é instalado quando você instala o console do VMM. O módulo VMM pode ser carregado em seu script usando o comando a seguir no script: Import-Module-Name virtualmachinemanager. [Obtenha mais detalhes](hhttps://technet.microsoft.com/library/hh875013.aspx).
- Verifique se você possui pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento da biblioteca de um servidor VMM pode ser encontrado localmente no servidor VMM com o nome de pasta MSCVMMLibrary.
- Se o caminho de compartilhamento da biblioteca for remoto (ou local, mas não compartilhado com MSCVMMLibrary, configure o compartilhamento como se segue (usando \\libserver2.contoso.com\\share\\ como um exemplo):
	- Abra o Editor do Registro.
	- Navegue até HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft System Center Virtual Machine Manager Server\\DRAdapter\\Registration.
	- Edite o valor ScriptLibraryPath.
	- Coloque o valor como \\libserver2.contoso.com\\share. Especifique o FQDN completo.
	- Forneça permissões para o local de compartilhamento.

- Os scripts em um plano de recuperação são executados no contexto da conta de Serviço VMM. Verifique se essa conta tem permissões de Leitura no compartilhamento remoto no qual o script está localizado e teste o script a ser executado no nível de privilégio da conta de serviço VMM.
- 	Não de esqueça de testar o script com uma conta de usuário que tenha as mesmas permissões que a conta de Serviço VMM, a fim de garantir que scripts autônomos testados sejam executados da mesma maneira que serão nos planos de recuperação.
- 	No servidor VMM, defina a política de execução a ser ignorada da seguinte maneira:
	- Abra o console do Windows PowerShell de 64 bits usando privilégios elevados.
	- Digite: **Set-executionpolicy bypass**. [Obtenha mais detalhes](https://technet.microsoft.com/library/ee176961.aspx).
- Use blocos try-catch para que as exceções sejam tratadas normalmente. Se houver uma exceção no script, ele será interrompido e a tarefa será mostrada como com falha. Se ocorrer um erro, qualquer parte restante do script não será executada. Se isso ocorrer quando você estiver executando um failover não planejado, o plano de recuperação continuará. Se isso ocorrer quando você estiver executando um failover planejado, o plano de recuperação será interrompido. Se isso ocorrer, corrija o script, verifique se ele funciona conforme esperado e execute novamente o plano de recuperação.
- O comando Write-Host não funciona em um script de plano de recuperação e o script falhará. Se deseja criar uma saída, crie um script de proxy que, por sua vez, execute seu script principal, e garanta que toda a saída seja direcionada usando o comando >>.
- O script expira se não retornar em até 600 segundos.
- Se nada for escrito em STDERR, o script será classificado como com falha. Essa informação será exibida nos detalhes de execução do script.

Crie o script da seguinte maneira:

1. Crie uma nova pasta no compartilhamento de biblioteca, por exemplo <NomeDoServidorVMM> \\MSSCVMMLibrary\\RPScripts. Coloque-a nos servidores VMM de origem e de destino.
2. Crie o script (por exemplo, RPScript) e verifique se ele funciona conforme esperado.
3. Coloque o script no local <NomeDoServidorVMM>\\MSSCVMMLibrary nos servidores VMM de origem e de destino.

#### Criar um runbook da automação do Azure

Você pode estender o seu plano de recuperação executando um runbook da automação do Azure como parte do plano. [Leia mais](site-recovery-runbook-automation.md).


### Adicionar configurações personalizadas a um plano de recuperação

1. Abra o plano de recuperação que deseja personalizar.
2. Clique para adicionar máquinas virtuais ou um novo grupo.
3. Para adicionar um script ou ação manual, clique em qualquer item na lista **Etapa** e clique em **Script** ou **Ação Manual**. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Use os botões de comando **Mover para Cima** e **Mover para Baixo** para movimentar o script para cima ou para baixo.
4. Se estiver adicionando um script do VMM, selecione **Failover no script do VMM** e, no **Caminho do Script**, digite o caminho relativo para o compartilhamento. Portanto, em nosso exemplo, onde o compartilhamento está localizado em \<VMMServerName>\\MSSCVMMLibrary\\RPScripts, especifique o caminho: \\RPScripts\\RPScript.PS1.
5. Se estiver adicionando um runboook da automação do Azure, especifique a **Conta de Automação do Azure** na qual o runbook está localizado e selecione o **Script de Runbook do Azure** apropriado.
5. Faça um failover do plano de recuperação para garantir que o script funciona conforme esperado.


## Executar um failover

É possível executar diferentes tipos de plano de recuperação de failovers, incluindo um failover de teste para verificar seu ambiente, bem como um failover planejado ou não planejado. Leia mais sobre failover e obtenha instruções de como executar diferentes tipos de failover [aqui](site-recovery-failover.md).


 

<!----HONumber=Oct15_HO3-->