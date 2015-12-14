<properties
	pageTitle="Planejador de Capacidade do Site Recovery | Microsoft Azure" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos locais para o Azure ou para um site local secundário." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Planejador de Capacidade do Site Recovery

Este documento explica o uso da ferramenta de planejamento de capacidade do Microsoft ASR, o que oferece diretrizes sobre os recursos que precisam ser provisionados para operações contínuas do Site Recovery. A ferramenta de planejamento de capacidade pode ser usada para analisar o ambiente de origem (cargas de trabalho), os requisitos de largura de banda, os requisitos de recursos (VMs e armazenamento) no destino e recursos de servidor adicionais que serão necessários na origem (VMMs SC, Servidores de Configuração, Servidores de Processo etc.) Baixe a ferramenta [Planejador de Capacidade do Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel)
 
Há dois modos de usar o planejador de capacidade:
 
- **Planejamento rápido**: obtenha as projeções de rede e de servidor baseadas no número médio de VMs, de discos, da taxa de alteração e de armazenamento. 
- **Planejamento detalhado**: forneça os detalhes de cada carga de trabalho no nível da VM. Analise a compatibilidade no nível da VM e obtenha também as projeções da rede e dos servidores.
     
Este documento presume que o usuário esteja familiarizado com o Azure Site Recovery. Consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md)

## Introdução
###Pré-requisitos
Dependendo do modo de planejador desejado, os detalhes necessários para continuar serão diferentes. Além dos detalhes de infraestrutura, como VMs, Discos por VM, Armazenamento por disco, há mais alguns detalhes necessários. O principal é a taxa de alteração diária ou taxa de variação. Se o ambiente de origem for o Hyper-V, use a [ferramenta de planejamento de capacidade do Hyper-V](https://www.microsoft.com/en-in/download/details.aspx?id=39057) para obter a taxa de variação. Leia as instruções para usar a [ferramenta de planejamento de capacidade do Hyper-v](site-recovery-capacity-planning-for-hyper-v-replication.md). Para o VMWare, use a [ferramenta de dispositivo Planejamento de Capacidade do VMware](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)

##Planejador Rápido
1.	Abra o arquivo **ASR Capacity Planner.xlsm**. Isso requer a execução de macros. Portanto, selecione **"habilitar edição"** e **"habilitar conteúdo"** quando solicitado 
1.	Selecione **Planejador Rápido** na caixa de listagem. Isso abrirá outra planilha denominada **Planejador de Capacidade**

	![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

1.	Na planilha "Planejador de Capacidade", insira as informações como necessário. Todos os campos circulados são os campos de entrada obrigatórios.
	1.	A caixa de listagem Selecionar seu cenário pode ser usada para alterar o ambiente de origem entre 'Hyper-V para Azure' e 'VMware/Físico para Azure'.
	1. 	A taxa média de Alteração Diária precisa ser medida. Em ambientes do Hyper-V, a ferramenta de planejamento de capacidade do Hyper-V pode ser usada. No caso do VMWare, a ferramenta de planejamento de capacidade do VMWare pode ser usada. Geralmente, é aconselhável executar a ferramenta pelo menos uma vez por semana para que os picos possam ser capturados e uma média possa ser estabelecida. 
	1. 	Compactação – é a compactação oferecida pelo ASR no cenário VMWare/Físico para Azure. No Hyper-V para Azure, isso pode ser obtido usando dispositivo de terceiros, como o Riverbed. 
	1. No cenário VMWare/Físico para Azure, a entrada do número de retenção deve feita ser em dias. No cenário do Hyper-V, a entrada será feita em horas. 
	1. As duas últimas entradas são usadas para computar a RI (replicação inicial). Quando a instalação do ASR é implantada, o conjunto inteiro de dados inicial deve ser carregado. O Número de horas em que a replicação inicial para o lote de máquinas virtuais deve ser concluída e o Número de máquinas virtuais por lote de replicação inicial – são as entradas ideais. Por outro lado, esses números podem ser regulados para ajustar a largura de banda existente. 

	![Entradas](./media/site-recovery-capacity-planner/inputs.png)

1. Depois de inserir os detalhes do ambiente de origem, a saída exibida terá as diretrizes, que incluem
	1.	Requisitos de largura de banda de rede
		1. Largura de banda necessária para a replicação delta (Megabits/s) Isso é calculado baseado na taxa média de alteração diária de dados. 
		1. A largura de banda necessária para a replicação inicial (em Megabits/s) também é apresentada. Isso é calculado com base em entradas de replicação inicial apresentadas (duas últimas linhas) nas entradas. 
	1.	Requisitos do Azure
		1. 	Esta seção detalha o Armazenamento, o IOPS, as Contas de Armazenamento e os Discos exigidos no Azure. 
	1. 	Outros requisitos de infraestrutura 
		1. Os requisitos adicionais no cenário VMware/físicos para Azure, como os requisitos de servidores de configuração e os servidores de processo. 
		1. 	Os requisitos adicionais no cenário do Hyper-V para Azure, como o armazenamento adicional exigido na origem.
			
	![Saída](./media/site-recovery-capacity-planner/output.png)
 
##Planejador Detalhado

1.	Abra o arquivo **ASR Capacity Planner.xlsm**. Isso requer a execução de macros. Portanto, selecione **"habilitar edição"** e **"habilitar conteúdo"** quando solicitado 
1.	Selecione **Planejador Detalhado** na caixa de listagem. Isso abrirá outra planilha denominada **Qualificação de Carga de Trabalho**

	![Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Na planilha Qualificação de Carga de Trabalho, insira as informações como necessário. Todas as colunas marcadas em vermelho são campos obrigatórios. Outras colunas são opcionais.
	1.	Núcleos de processo: fornecem o número total de núcleos de um servidor de origem
	1. Alocação de memória em MB: fornece o tamanho da RAM de um servidor de origem.
	1.	Número de NICs: fornece o número de NICS de um servidor de origem.
	1. Total de armazenamento (em GB): fornece o tamanho total do armazenamento da VM. Por exemplo, se o servidor de origem tiver três discos, cada um com 500 GB, o tamanho total de armazenamento será de 1500 GB.
	1. Número de discos anexados: fornece o número total de discos de um servidor de origem.
	1. Utilização da capacidade do disco: fornece a utilização média 
	1. Taxa de alteração diária (%): fornece a taxa diária de alteração de dados de um servidor de origem.
	1. Mapeamento do tamanho do Azure: você pode inserir o tamanho da VM do Azure que deseja mapear ou usar o botão Computar VMs IaaS para calcular a melhor correspondência possível. 

	![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. Clicar no botão **Computar VMs IaaS** valida as entradas acima e sugere a melhor correspondência de VM do Azure possível. Se a opção não localizar o tamanho apropriado da VM do Azure para um servidor de origem, ela informará um erro para o servidor. Por exemplo, quando o Número de discos anexados a uma VM de origem for 65, ela retornará um erro, já que o número máximo de discos que podem ser anexados à VM do Azure de maior tamanho é 64


**Computar VMs IaaS** também calcula se uma VM precisa de conta de armazenamento standard ou premium do Azure. Ela também sugere quantas contas de armazenamento standard e premium são necessárias para a carga de trabalho. Role para baixo e para a direita a fim de exibir o tipo de armazenamento do Azure e a conta de armazenamento que podem ser usadas em um servidor de origem
 
**Exemplo**: para cinco VMs com os valores a seguir, a ferramenta calculou e atribuiu a melhor correspondência de tamanho de VM do Azure e sugeriu o armazenamento padrão ou o premium para a VM de acordo com a necessidade

![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)

Este exemplo precisa de duas contas de armazenamento padrão e de uma conta de armazenamento premium para cinco VMs. A VM1 e a VM2 podem usar a primeira conta de armazenamento padrão; a VM3 pode usar a segunda conta de armazenamento padrão. A VM4 e a VM5 precisam de contas de armazenamento premium e podem ser acomodadas em uma única conta de armazenamento premium.

![Qualificação de Carga de Trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]O IOPS é calculado no nível da VM e não em nível de disco. Se um dos discos de IOPS VM de origem for > 500, mas o IOPS total da VM estiver dentro do que tem suporte pela VM Azure padrão, e todos os outros valores (número de discos, número de NICs, número de núcleos de CPU, tamanho de memória) estiverem dentro de um padrão de limite da VM, a ferramenta selecionará uma VM padrão em vez do armazenamento premium. O usuário precisa atualizar manualmente a célula de tamanho do mapeamento do Azure com a VM série DS ou GS apropriada


1.	A primeira coluna é uma coluna de validação para VMs, discos e variação. 
1.	Depois que todos os detalhes estiverem prontos, pressione o botão **Enviar dados para a ferramenta de planejamento** na parte superior. Isso abrirá a planilha **Planejador de Capacidade** com as médias preenchidas automaticamente, como mostrado na figura abaixo. 
1.	Essa ação também realçará quais cargas de trabalho são qualificadas para proteção e quais não são.


###Planejador de Capacidade

1.	Na planilha **Planejador de Capacidade**, a **Carga de trabalho** na primeira linha de Entradas de Infraestrutura sugere que as informações de entrada são populadas usando a planilha **Qualificação de Carga de Trabalho**.  
1.	Sempre que houver a necessidade de alterações, faça as alterações necessárias na planilha **Qualificação de carga de trabalho** e pressione o botão **Enviar dados para a ferramenta de planejamento**. 

	![Planejador de Capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->