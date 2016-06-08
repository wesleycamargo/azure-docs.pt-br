<properties
	pageTitle="Solução de Gerenciamento de Capacidade no Log Analytics | Microsoft Azure"
	description="Você pode usar a solução de Planejamento de Capacidade no Log Analytics para ajudá-lo a compreender a capacidade dos servidores Hyper-V gerenciados pelo System Center Virtual Machine Manager"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="banders"/>

# Solução de Gerenciamento de Capacidade no Log Analytics


Você pode usar a solução de Planejamento de Capacidade no Log Analytics para ajudá-lo a compreender a capacidade dos servidores Hyper-V gerenciados pelo System Center Virtual Machine Manager. Essa solução requer o System Center Operations Manager e o System Center Virtual Machine Manager. O Planejamento de Capacidade não estará disponível se você usar apenas agentes conectados diretamente. Instale a solução para atualizar o agente do Operations Manager. A solução lê os contadores de desempenho no servidor monitorado e envia dados de uso para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados de uso e o serviço de nuvem registra os dados. Ao longo do tempo, padrões de uso são identificados e a capacidade é projetada com base no consumo atual.

Por exemplo, uma projeção pode identificar quando a núcleos do de processador adicionais ou memória adicional serão necessários para um servidor individual. Neste exemplo, a projeção pode indicar que o servidor precisará de mais memória em 30 dias. Isso pode ajudá-lo a planejar uma atualização de memória durante a próxima janela de manutenção do servidor, que pode ocorrer uma vez a cada duas semanas.

>[AZURE.NOTE] A solução de Gerenciamento de Capacidade não está disponível para ser adicionada a espaços de trabalho. Os clientes que possuem a solução de Gerenciamento de Capacidade instalada podem continuar a usá-la.

A solução de Planejamento de Capacidade está sendo atualizada para lidar com os seguintes desafios relatados pelos clientes:

- Requisito para usar o Virtual Machine Manager e o Operations Manager
- Incapacidade de personalizar/filtrar com base em grupos
- Agregação de dados por hora com frequência insuficiente
- Sem percepções de nível de VM
- Confiabilidade de dados

Benefícios da nova solução de capacidade:

- Suporte a coleta de dados granulares com maior confiabilidade e precisão
- Suporte para Hyper-V sem a necessidade do VMM
- Visualização de métricas no Power BI
- Informações sobre a utilização de nível de VM


## Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

- O Operations Manager é necessário para a solução de Gerenciamento de Capacidade.
- O Virtual Machine Manage é necessário para a solução de Gerenciamento de Capacidade.
- A conectividade do Operations Manager com o VMM (Virtual Machine Manager) é necessária. Para obter informações adicionais sobre como conectar os sistemas, consulte [Como conectar o VMM ao Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- O Operations Manager deve estar conectado ao Log Analytics.
- Adicione a solução de Gerenciamento de Capacidade ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md). Não é necessária nenhuma configuração.


## Detalhes de coleta de dados de Gerenciamento de Capacidade

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o gerenciamento de capacidade.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|
|Windows|![Não](./media/log-analytics-capacity/oms-bullet-red.png)|![Sim](./media/log-analytics-capacity/oms-bullet-green.png)|![Não](./media/log-analytics-capacity/oms-bullet-red.png)| ![Sim](./media/log-analytics-capacity/oms-bullet-green.png)|![Sim](./media/log-analytics-capacity/oms-bullet-green.png)| por hora|


## Página de Gerenciamento de Capacidade


 Após a instalação da solução de Planejamento de Capacidade, você pode exibir a capacidade dos seus servidores monitorados usando o bloco **Planejamento de Capacidade** na página **Visão Geral** do OMS.

![imagem do bloco Planejamento de Capacidade](./media/log-analytics-capacity/oms-capacity01.png)

O bloco abre o painel **Gerenciamento de Capacidade**, em que você pode exibir um resumo da capacidade do servidor. A página exibe os seguintes blocos em que você pode clicar:

- *Contagem de máquinas virtuais*: mostra o número de dias restantes para a capacidade das máquinas virtuais
- *Computação*: mostra os núcleos do processador e a memória disponível
- *Armazenamento*: mostra o espaço em disco usado e a latência média do disco
- *Pesquisa*: o Gerenciador de dados que você pode usar para pesquisar todos os dados no sistema OMS

![imagem do painel Gerenciamento de Capacidade](./media/log-analytics-capacity/oms-capacity02.png)


### Para exibir uma página de capacidade

- Na página **Visão Geral**, clique em **Gerenciamento de Capacidade** e, em seguida, clique em **Computação** ou **Armazenamento**.

## Página de computação

Você pode usar o painel **Computação** no Microsoft Azure OMS para exibir informações de capacidade sobre utilização, dias de capacidade projetados e a eficiência relacionada à sua infraestrutura. Você usa a área **Utilização** para exibir a utilização do núcleo e da memória da CPU em seus hosts de máquina virtual. Você pode usar a ferramenta de projeção para estimar quanta capacidade deve estar disponível para um determinado intervalo de datas. Você pode usar a área **Eficiência** para ver quão eficientes são os hosts da máquina virtual. Você pode exibir detalhes sobre itens vinculados clicando neles.

Você pode gerar uma pasta de trabalho do Excel para as seguintes categorias:

- Hosts principais com maior utilização de núcleo
- Hosts principais com maior utilização de memória
- Hosts principais com máquinas virtuais ineficientes
- Hosts principais por utilização
- Hosts com menor utilização

![imagem da página de Computação de Gerenciamento de Capacidade](./media/log-analytics-capacity/oms-capacity03.png)


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
4. Na área **Eficiência**, exiba informações de eficiência da capacidade sobre máquinas virtuais e hosts de máquina virtual.

## Página Armazenamento Anexado Direto

Você pode usar o painel **Armazenamento Anexado Direto** no Microsoft Azure OMS para exibir informações de capacidade sobre utilização do armazenamento, desempenho do disco e dias projetados da capacidade do disco. Use a área **Utilização** para exibir o uso de espaço em disco em seus hosts de máquina virtual. Use a área **Desempenho do Disco** para exibir a taxa de transferência e a latência do disco e nos hosts da máquina virtual. Você também pode usar a ferramenta de projeção para estimar quanto de capacidade deve estar disponível para um determinado intervalo de datas. Você pode exibir detalhes sobre itens vinculados clicando neles.

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

![imagem da página Armazenamento Anexado Direto de Gerenciamento de Capacidade](./media/log-analytics-capacity/oms-capacity04.png)

**Desempenho do Disco**

Usando o OMS, você pode exibir a tendência de uso histórico do espaço em disco. A funcionalidade de projeção usa um algoritmo para projetar o uso futuro. Para uso do espaço em particular, a capacidade de projeção permite projetar quando você poderá ficar sem espaço em disco. Isso ajudará a planejar o armazenamento adequado e saber quando você precisa adquirir mais armazenamento.

**Ferramenta de Projeção**

Usando a ferramenta de projeção, você pode exibir as tendências históricas para utilização do espaço em disco. A funcionalidade de projeção também permite projetar quando você ficará com pouco espaço em disco. Isso ajudará a planejar a capacidade adequada e a saber quando você precisa adquirir mais capacidade de armazenamento.

### Para trabalhar com itens na página Armazenamento Anexado Direto

1. No painel **Armazenamento Anexado Direto**, na área **Utilização**, você pode exibir as informações de utilização do disco.
2. Clique em um item vinculado para abri-lo na página de **Pesquisa** e exibir informações detalhadas sobre ele.
3. Na área **Desempenho do Disco**, você pode exibir informações de taxa de transferência e latência de disco.
4. Na **ferramenta Projeção**, mova o controle deslizante de data para exibir uma projeção da capacidade que será usada na data que você escolher.


## Próximas etapas

- Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados de gerenciamento de capacidade detalhados.

<!---HONumber=AcomDC_0518_2016-->