<properties
	pageTitle="Planejar a capacidade de proteção da máquina virtual e do servidor físico no Azure Site Recovery | Microsoft Azure"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário." 
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
	ms.date="12/07/2015" 
	ms.author="raynew"/>

# Planejar a capacidade de proteção da máquina virtual e do servidor físico no Azure Site Recovery

A ferramenta Planejador de Capacidade ajuda você a descobrir os requisitos de capacidade para proteger VMs Hyper-V, VMs VMware e servidores físicos Windows/Linux com o Azure Site Recovery.


## Visão geral

Use a ferramenta para analisar o ambiente de origem e as cargas de trabalho a fim de descobrir as necessidades de largura de banda, de recursos de servidor necessários no local de origem e de recursos (máquinas virtuais e armazenamento, etc.) necessários no local de destino.

Você pode executar a ferramenta em dois modos:

- **Planejamento rápido**: execute a ferramenta nesse modo para obter as projeções de rede e de servidor baseadas no número médio de VMs, de discos, de armazenamento e da taxa de alteração.
- **Planejamento detalhado**: execute a ferramenta nesse modo e forneça detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade da VM e obtenha as projeções de rede e de servidor.

## Antes de começar

Antes de executar a ferramenta:

1. Reúna informações sobre seu ambiente, inclusive VMs, discos por VM e armazenamento por disco.
2. Identifique sua taxa de alteração (variação) diária de dados replicados. Para fazer isso:

	- Se você estiver replicando máquinas virtuais Hyper-V, baixe a [ferramenta Planejador de Capacidade do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre essa ferramenta. Recomendamos que você execute essa ferramenta durante uma semana para capturar as médias.
	- Se você estiver replicando máquinas virtuais VMware, use a [solução de planejamento de capacidade vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para calcular a taxa de variação.
	- Se você estiver replicando servidores físicos, precisará estimá-la manualmente.

## Execute o Planejador Rápido
1.	Baixe e abra a ferramenta [Planejador de Capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel). Você precisará executar macros; portanto, habilite a edição e o conteúdo quando solicitado. 
2.	Em **Selecionar um tipo de planejador**, selecione **Planejador Rápido** na caixa de listagem.

	![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3.	Na planilha **Planejador de Capacidade**, insira as informações necessárias. Preencha todos os campos envoltos em vermelho na captura de tela abaixo.

	- Em **Selecionar seu cenário**, escolha **Hyper-V para Azure** ou **VMware/físico para Azure**.
	- Em **Taxa média diária de alteração de dados**, coloque as informações reunidas usando a [ferramenta Planejador de capacidade do Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou a [solução de planejamento de capacidade vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
	- A configuração **Compactação** se aplica somente à compactação oferecida ao replicar VMs VMware ou servidores físicos para o Azure. Estimamos em 30% ou mais. Modifique a configuração de acordo com a necessidade. Para replicar VMs Hyper-V para compactação do Azure, você pode usar uma solução de terceiros, como o Riverbed. 
	-  Em **Número de retenção**, especifique por quanto tempo as réplicas devem ser retidas. Se você estiver replicando VMware ou servidores físicos, insira o valor em dias. Se você estiver replicando Hyper-V, especificar o tempo em horas.
	-  Em **Número de horas em que a replicação inicial para o lote de máquinas virtuais deve ser concluída** e em **Número de máquinas virtuais por lote de replicação inicial**, você insere as configurações usadas para calcular os requisitos de replicação inicial. Quando a instalação da Recuperação de Site é implantada, o conjunto inteiro de dados inicial deve ser carregado. 

	![Entradas](./media/site-recovery-capacity-planner/inputs.png)

2.	Depois de inserir os valores para o ambiente de origem, a saída exibida incluirá:

	- Requisitos de largura de banda de rede:
		- **Largura de banda necessária para a replicação delta** (MB/s). Isso é calculado baseado na taxa média de alteração diária de dados.
		- **Largura de banda necessária para a replicação inicial** (MB/s). Isso é calculado usando os valores de replicação inicial que você inseriu. 
	- Requisitos do Azure: fornece detalhes sobre armazenamento, IOPS, contas de armazenamento e discos necessários no Azure.
		- **Armazenamento necessário (em GB)** é o armazenamento do Azure total necessário.
		- **IOPS total nas contas de armazenamento padrão** é calculado baseado no tamanho da unidade IOPS 8K no total de contas de armazenamento padrão. Para o Planejador Rápido, o número é calculado baseado em todos os discos de VMs de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado baseado no número total de VMs mapeadas para as VMs padrão do Azure e na taxa diária de alteração dessas VMs. 
		- O **Número de contas de armazenamento padrão** fornece o número total de contas de armazenamento padrão necessárias para proteger as VMs. Observe que uma conta de armazenamento padrão pode conter até 20 mil IOPS em todas as VMs em um armazenamento padrão e dar suporte a um máximo de 500 IOPS por disco. 
		- O **Número de discos de blob necessários** retorna o número de discos que serão criados no armazenamento do Azure.
		- O **Número de contas de armazenamento premium necessárias** fornece o número total de contas de armazenamento premium necessárias para proteger as VMs. Observe que uma VM com IOPS alto (maior que 20 mil) precisa de uma conta de armazenamento premium. Uma conta de armazenamento premium pode armazenar até 80 mil IOPS.
		- O **IOPS total em contas de armazenamento premium** é calculado baseado no tamanho da unidade IOPS 256K no total de contas de armazenamento premium. Para o Planejador Rápido, o número é calculado baseado em todos os discos de VMs de origem e na taxa diária de alteração dos dados. Para o Planejador Detalhado, o número é calculado baseado no número total de VMs mapeadas para as VMs premium do Azure (séries DS e GS) e na taxa diária de alteração dessas VMs. 

	- Outros requisitos de infraestrutura:
		- Inclui requisitos adicionais para replicação de VMware/física para o Azure, incluindo servidores de configuração e de processo.
		- Número de servidores de processo adicionais necessários: mostra se há a necessidade de servidores de processo adicionais. Isso em relação a e além do servidor de processo que está configurado no servidor de configuração por padrão. N
		- Inclui requisitos adicionais na replicação Hyper-V para Azure, por exemplo, se é necessário armazenamento adicional na origem.
			
	![Saída](./media/site-recovery-capacity-planner/output.png)
 
## Executar o Planejador Detalhado


1.	Baixe e abra a ferramenta [Planejador de Capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel). Você precisará executar macros; portanto, habilite a edição e o conteúdo quando solicitado. 
2.	Em **Selecionar um tipo de planejador**, selecione **Planejador Detalhado** na caixa de listagem.

	![Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	Na planilha **Qualificação de Carga de Trabalho**, insira as informações necessárias. Preencha todos os campos marcados.

	- Em **Núcleos de processador**, especifique o número total de núcleos em um servidor de origem.
	- Em **Alocação de memória em MB**, especifique o tamanho da RAM de um servidor de origem. 
	- O **Número de NICs** especifica o número de adaptadores de rede em um servidor de origem. 
	-  Em **Total de armazenamento (em GB)**, especifique o tamanho do armazenamento da VM. Por exemplo, se o servidor de origem tiver três discos, cada um com 500 GB, o tamanho total de armazenamento será de 1500 GB.
	-  Em **Número de discos anexados**, especifique o número total de discos de um servidor de origem.
	-  Em **Utilização da capacidade do disco**, especifique a utilização média.
	-  Em **Taxa de alteração diária (%)**, especifique a taxa diária de alteração de dados de um servidor de origem.
	-  Em **Tamanho do mapeamento do Azure**, digite o tamanho da VM do Azure que você deseja mapear. Se não quiser fazer isso manualmente, clique em **Computar VMs IaaS**.

4.	Se você clicar em **Computar VMs IaaS**, eis o que essa opção faz:

	- Valida suas entradas e obtém a melhor correspondência de VM do Azure em relação a cada VMs qualificada para ser replicada para o Azure. Se não for possível detectar um tamanho adequado de VM do Azure, um erro será emitido. Por exemplo, se o número de discos anexados for 65, um erro será emitido, já que o maior tamanho de VM do Azure é 64.
	- Sugere uma conta de armazenamento que pode ser usada para uma VM do Azure.
	- Calcula quantas contas de armazenamento standard e premium são necessárias para a carga de trabalho. Role para baixo à direita a fim de exibir o tipo de armazenamento do Azure e a conta de armazenamento que podem ser usadas em um servidor de origem
	- Conclui e classifica o restante da tabela baseado no tipo de armazenamento necessário (standard ou premium) atribuído a uma máquina virtual e no número de discos anexados. Para todas as VMs que atendem aos requisitos para fazer backup no Azure, a coluna A (A VM está qualificada?) mostra Sim. Se uma máquina virtual não puder ter o backup feito no Azure, um erro é exibido.

	![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)


- **Exemplo**: para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência de VM do Azure e os requisitos de armazenamento do Azure.

	![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)

- Na saída, por exemplo, observe o seguinte:
	- São necessárias duas contas de armazenamento padrão e uma conta de armazenamento premium para cinco VMs. 
	-  A VM3 não se qualifica para proteção, pois um ou mais discos tem mais de 1 TB.
	-  A VM1 e a VM2 podem usar a primeira conta de armazenamento padrão
	-  A VM4 pode usar a segunda conta de armazenamento padrão.
	-  A VM5 e a VM6 precisam de uma conta de armazenamento premium e podem usar a mesma conta.

	![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

	>[AZURE.NOTE]O IOPS de armazenamento é calculado no nível da VM e não no nível do disco. Se um dos discos de IOPS VM de origem for maior que 500, mas o IOPS total da VM estiver dentro da faixa com suporte da VM do Azure padrão (e todos os outros valores, como número de discos, número de NICs, número de núcleos de CPU, tamanho de memória estiverem dentro do limite padrão da VM), a ferramenta selecionará uma VM padrão em vez do armazenamento premium. Você precisa atualizar manualmente a célula de tamanho do mapeamento do Azure com a VM série DS ou GS apropriada

	>[AZURE.NOTE]A primeira coluna é uma coluna de validação para VMs, discos e variação. O IOPS de armazenamento é calculado no nível da VM e não no nível do disco. Se um dos discos de IOPS VM de origem for maior que 500, mas o IOPS total da VM estiver dentro da faixa com suporte da VM do Azure padrão (e todos os outros valores, como número de discos, número de NICs, número de núcleos de CPU, tamanho de memória estiverem dentro do limite padrão da VM), a ferramenta selecionará uma VM padrão em vez do armazenamento premium. Você precisa atualizar manualmente a célula de tamanho do mapeamento do Azure com a VM série DS ou GS apropriada

5. Depois que todos os detalhes estão prontos, clique em **Enviar dados para a ferramenta de planejamento** a fim de abrir o **Planejador de Capacidade**. As cargas de trabalho são realçadas para mostrar se estão qualificadas para proteção ou não.


### Executar o Planejador de Capacidade

1.	Preencha a planilha **Planejador de Capacidade**. As palavras “Carga de trabalho” aparecem na célula de entrada de infraestrutura para mostrar a planilha de entrada **Qualificação de Carga de Trabalho**.  
2.	Modifique as configurações que você deseja alterar e clique em **Enviar dados para a ferramenta de planejamento**. 

	![Planejador de Capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1210_2015-->