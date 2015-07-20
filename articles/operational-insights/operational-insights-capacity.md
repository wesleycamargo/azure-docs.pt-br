<properties
   pageTitle="Gerenciar a capacidade de infraestrutura"
   description="Aprenda a usar a solução de planejamento de capacidade no Insights Operacionais para ajudar a compreender a capacidade da sua infraestrutura de servidor"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Gerenciar a capacidade de infraestrutura

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode usar a solução Gerenciamento de Capacidade no Insights Operacionais do Microsoft Azure para ajudá-lo a compreender a capacidade da sua infraestrutura de servidor. Instale a solução para atualizar o agente do Operations Manager e o módulo de configuração básica no Insights Operacionais. A solução lê os contadores de desempenho no servidor monitorado e envia dados de uso para o serviço de informações operacionais na nuvem para processamento. Lógica é aplicada aos dados de uso e o serviço de nuvem registra os dados. Ao longo do tempo, padrões de uso são identificados e a capacidade é projetada com base no consumo atual.

Por exemplo, uma projeção pode identificar quando a núcleos do de processador adicionais ou memória adicional serão necessários para um servidor individual. Neste exemplo, a projeção pode indicar que o servidor precisará de mais memória em 30 dias. Isso pode ajudá-lo a planejar uma atualização de memória durante a próxima janela de manutenção do servidor, que pode ocorrer uma vez a cada duas semanas.

## Painel de gerenciamento de capacidade

Para poder usar o painel de gerenciamento de capacidade no Insights Operacionais do Microsoft Azure, você deve ter a solução instalada. Para saber mais sobre a instalação de soluções, consulte [Usar a Galeria de Soluções para adicionar ou remover soluções](operational-insights-add-solution.md). Após a instalação da solução de planejamento de capacidade, você pode exibir a capacidade dos seus servidores monitorados usando o bloco **Planejamento de Capacidade** na página **Visão Geral** do Insights Operacionais.

![imagem do bloco Planejamento de Capacidade](./media/operational-insights-capacity/overview-cap-plan.png)

O bloco abre o painel **Gerenciamento de Capacidade**, em que você pode exibir um resumo da capacidade do servidor. A página exibe os seguintes blocos em que você pode clicar:

- *Contagem de máquinas virtuais*: mostra o número de dias restantes para a capacidade das máquinas virtuais

- *Computação*: mostra os núcleos do processador e a memória disponível

- *Armazenamento*: mostra o espaço em disco usado e a latência média do disco

- *Pesquisa*: o Gerenciador de dados que você pode usar para pesquisar todos os dados no sistema do Insights Operacionais

>[AZURE.NOTE]Para exibir dados de gerenciamento de capacidade, habilite a conectividade do Operations Manager com o Virtual Machine Manager (VMM). Para obter informações adicionais sobre como conectar os sistemas, consulte Como conectar o VMM com o Operations Manager.

![imagem do painel Gerenciamento de Capacidade](./media/operational-insights-capacity/gallery-capacity-01.png)

### Para exibir uma página de capacidade

- Na página **Visão Geral**, clique em **Gerenciamento de Capacidade** e, em seguida, clique em **Computação** ou **Armazenamento**.

## Página de computação

Você pode usar o painel **Computação** no Insights Operacionais do Microsoft Azure para exibir informações de capacidade sobre utilização, dias de capacidade projetados e a eficiência relacionada à sua infraestrutura. Você usa a área **Utilização** para exibir a utilização do núcleo e da memória da CPU em seus hosts de máquina virtual. Você pode usar a ferramenta de projeção para estimar quanta capacidade deve estar disponível para um determinado intervalo de datas. Você pode usar a área **Eficiência** para ver quão eficientes são os hosts da máquina virtual. Você pode exibir detalhes sobre itens vinculados clicando neles.

Você pode gerar uma pasta de trabalho do Excel para as seguintes categorias:

- Hosts principais com maior utilização de núcleo

- Hosts principais com maior utilização de memória

- Hosts principais com máquinas virtuais ineficientes

- Hosts principais por utilização

- Hosts com menor utilização

![imagem da página de Computação de Gerenciamento de Capacidade](./media/operational-insights-capacity/gallery-capacity-02.png)


As seguintes áreas são mostradas no painel **Computação**:

**Utilização**: exiba a utilização de memória e núcleo da CPU em seus hosts da máquina virtual.

- *Núcleos usados*: soma de todos os hosts (% da CPU utilizada multiplicada pelo número de núcleos físicos no host).

- *Núcleos livre*: total de núcleos físicos menos núcleos usados.

- *Percentual de núcleos disponíveis*: núcleos físicos livres divididos pelo número total de núcleos físicos.

- *Núcleos virtuais por VM*: total de núcleos virtuais no sistema dividido pelo número total de máquinas virtuais no sistema.

- *Proporção de núcleos virtuais para núcleos físicos*: proporção do total de núcleos virtuais para núcleos físicos que são usados por máquinas virtuais no sistema.

- *Número de núcleos virtuais disponíveis*: proporção de núcleo virtual para núcleos físicos multiplicada pelos núcleos físicos disponíveis.

- *Memória utilizada*: soma da memória utilizada por todos os hosts.

- *Memória livre*: memória física total menos a memória usada.

- *Percentual de memória disponível*: memória física livre dividida pelo total de memória física.

- *Memória Virtual por VM*: memória virtual total no sistema dividida pelo número total de máquinas virtuais no sistema.

- *Proporção de memória virtual para memória física*: Memória virtual total no sistema dividida pela memória física total no sistema.

- *Memória virtual disponível*: proporção de memória virtual para memória física multiplicada pela memória física disponível.

**Ferramenta de projeção**

Usando a ferramenta de projeção, você pode exibir as tendências históricas para a utilização de recursos. Isso inclui as tendências de uso de máquinas virtuais, memória, núcleo e armazenamento. A funcionalidade de projeção usa um algoritmo de projeção para ajudá-lo a saber quando você está com pouco de cada um dos recursos. Isso ajuda a calcular o planejamento de capacidade adequado para que você possa saber quando precisa adquirir mais capacidade (por exemplo, memória, núcleos ou armazenamento).

**Eficiência**

- *VM ociosa*: usando menos de 10% da CPU e 10% da memória para o período de tempo especificado.

- *VM superutilizada*: Usando mais de 90% da CPU e 90% da memória para o período de tempo especificado.

- *Host ocioso*: usando menos de 10% da CPU e 10% da memória para o período de tempo especificado.

- *Host superutilizado*: usando mais de 90% da CPU e 90% da memória para o período de tempo especificado.

### Para trabalhar com itens na página Computação

1. No painel **Computação**, na área **Utilização**, exiba informações de capacidade sobre os núcleos e a memória da CPU em uso.

2. Clique em um item para abri-lo na página de **Pesquisa** e exiba informações detalhadas sobre ele.

3. Na ferramenta **Projeção**, mova o controle deslizante de data para exibir uma projeção da capacidade que será usada na data que você escolher.

3. Na área **Eficiência**, exiba informações de eficiência da capacidade sobre máquinas virtuais e hosts de máquina virtual.

##Página Armazenamento Anexado Direto

Você pode usar o painel **Armazenamento Anexado Direto** no Insights Operacionais do Microsoft Azure para exibir informações de capacidade sobre utilização do armazenamento, desempenho do disco e dias projetados da capacidade do disco. Use a área **Utilização** para exibir o uso de espaço em disco em seus hosts de máquina virtual. Use a área **Desempenho do Disco** para exibir a taxa de transferência e a latência do disco e nos hosts da máquina virtual. Você também pode usar a ferramenta de projeção para estimar quanto de capacidade deve estar disponível para um determinado intervalo de datas. Você pode exibir detalhes sobre itens vinculados clicando neles.

Você pode gerar uma pasta de trabalho do Excel com base nessas informações de capacidade para as seguintes categorias:

- Maior uso de espaço em disco por host

- Maior latência média por host

As seguintes áreas são mostradas na página **Armazenamento**:

- *Utilização*: exiba o uso de espaço em disco nos hosts da máquina virtual.

- *Espaço em disco total*: soma (espaço em disco lógico) para todos os hosts

- *Espaço em disco usado*: soma (espaço em disco lógico utilizado) para todos os hosts

- *Espaço disponível no disco*: espaço total em disco menos o espaço em disco usado

- *Percentual de disco usado*: espaço em disco utilizado dividido pelo espaço total em disco

- *Percentual em disco disponível*: espaço em disco disponível dividido pelo espaço total em disco

![imagem da página Armazenamento Anexado Direto de Gerenciamento de Capacidade](./media/operational-insights-capacity/gallery-capacity-03.png)

**Desempenho do Disco**

Usando Insights Operacionais, você pode exibir a tendência de uso histórico do espaço em disco. A funcionalidade de projeção usa um algoritmo para projetar o uso futuro. Para uso do espaço em particular, a capacidade de projeção permite projetar quando você poderá ficar sem espaço em disco. Isso ajudará a planejar o armazenamento adequado e saber quando você precisa adquirir mais armazenamento.

**Ferramenta de projeção**

Usando a ferramenta de projeção, você pode exibir as tendências históricas para utilização do espaço em disco. A funcionalidade de projeção também permite projetar quando você ficará com pouco espaço em disco. Isso ajudará a planejar a capacidade adequada e a saber quando você precisa adquirir mais capacidade de armazenamento.

### Para trabalhar com itens na página Armazenamento Anexado Direto

1. No painel **Armazenamento Anexado Direto**, na área **Utilização**, você pode exibir as informações de utilização do disco.

2. Clique em um item vinculado para abri-lo na página de **Pesquisa** e exibir informações detalhadas sobre ele.

3. Na área **Desempenho do Disco**, você pode exibir informações de taxa de transferência e latência de disco.

4. Na **ferramenta Projeção**, mova o controle deslizante de data para exibir uma projeção da capacidade que será usada na data que você escolher.

<!---HONumber=July15_HO2-->