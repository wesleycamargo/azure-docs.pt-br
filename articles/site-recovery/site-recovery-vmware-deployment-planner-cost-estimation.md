---
title: Examinar o relatório de estimativa de custo no Planejador de Implantações do Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como examinar o relatório de estimativa de custo no Planejador de Implantações do Azure Site Recovery para VMware para recuperação de desastre do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/14/2019
ms.author: mayg
ms.openlocfilehash: 8a36a80903a47bb4163666baf86ed8dac13a00de
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093830"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Examine o relatório de estimativa de custo no Planejador de Implantações do Azure Site Recovery para recuperação de desastres do VMware para o Azure

O relatório do Planejador de Implantações fornece o resumo de estimativa de custo nas planilhas de [Recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) e a análise de custo detalhada na planilha Estimativa de Custo. Ele tem a análise de custo detalhada por VM. 

>[!Note]
>A versão atual da ferramenta de Planejador de implantação não fornece uma estimativa de custo para VMs replicando para o Managed Disks.
>* As estimativas de custo de recuperação de Desastre Drill são o mesmo para contas de armazenamento e discos gerenciados, quando o parâmetro 'Usar discos gerenciados' é definido como "Sim" na folha de "Computação e rede".
>* Para obter uma estimativa aproximada de custo anual de replicação, faça as seguintes configurações temporárias na **estimativa de custo** folha:
>    * Defina o parâmetro "Duração de custo" no **configurações** tabela para "Ano"
>    * Na **detalhado, análise de custo** da tabela, definir a coluna "Número de análises de recuperação de Desastre em um ano" como 12 e "duração de cada análise recuperação de Desastre (dias)" para 30 
>    * O custo de replicação será semelhante ao custo preenchido no custo de armazenamento de 'R' ou seja, análise de recuperação de Desastre de coluna por ano em **custo de análise de DR por ano** subseção.

### <a name="cost-estimation-summary"></a>Resumo de estimativa de custo 
O gráfico mostra a exibição de resumo do custo total de recuperação de desastre (DR) estimado para o Azure da sua região de destino escolhida e da moeda que você especificou para a geração de relatórios.
Resumo de estimativa de custo

![Resumo de estimativa de custo](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

O resumo ajuda a entender o custo que precisa ser pago para armazenamento, computação, rede e licença ao proteger todas as suas VMs compatíveis para o Azure usando o Azure Site Recovery. O custo é calculado sobre as VMs compatíveis e não todas as VMs com criação de perfil.  
 
Você pode exibir o custo mensal ou anual. Saiba mais sobre [regiões de destino com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) e [moedas com suporte](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Custo por componentes** O custo total de DR é dividido em quatro componentes: Custo de licença do Azure Site Recovery, Rede, Armazenamento e Computação. O custo é calculado com base no consumo incorrido durante a replicação e em tempo de análise de recuperação de desastre para computação, armazenamento (premium e standard), ExpressRoute/VPN configurado entre o site local e o Azure, e a licença do Azure Site Recovery.

**Custo por estados** O custo total da recuperação de desastre (DR) se baseia em categorias em dois estados diferentes - Replicação e análise de recuperação de desastre. 

**Custo de replicação**:  O custo que será incorrido durante a replicação. Abrange o custo de armazenamento, de rede e de licença do Azure Site Recovery. 

**Custo de Análise de DR**: O custo que será incorrido durante failovers de teste. O Azure Site Recovery gira máquinas virtuais durante o failover de teste. O custo de análise de análise de risco abrange os custos de computação e armazenamento das VMs em execução. 

**Custo de armazenamento do Azure por mês/ano** Mostra o custo total de armazenamento incorrido no armazenamento standard e premium para replicação e análise de recuperação de desastre.

## <a name="detailed-cost-analysis"></a>Análise de custo detalhada
Os preços do Azure para computação, armazenamento, rede etc. varia entre as regiões do Azure. Você pode gerar um relatório de estimativa de custo com os preços do Azure mais recentes com base em sua assinatura, a oferta associada à sua assinatura e para a região do Azure de destino especificada na moeda especificada. Por padrão, a ferramenta usa a região Oeste dos EUA 2 do Azure e a moeda dólar norte-americano (USD). Se você tiver usado qualquer região e moeda, na próxima vez em que gerar um relatório sem ID da assinatura, ID da oferta, região de destino e moeda, ele usará os preços da última região de destino utilizada e a moeda usada pela última vez para a estimativa de custo.
Esta seção mostra a ID da assinatura e a ID da oferta que você usou para geração de relatórios.  Se não usado, ele estará em branco.

Em todo o relatório, as células marcadas em cinza são somente leitura. As células em branco podem ser modificadas de acordo com seus requisitos.

![Detalhes da estimativa de custo1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Custo geral de recuperação de desastre por componentes
A primeira seção mostra o custo geral da recuperação de desastre pelos componentes e o custo de recuperação de desastre por estados. 

**Computação**: Custo das VMs de IaaS executadas no Azure para as necessidades de DR. Ele inclui as VMs criadas pelo Azure Site Recovery durante as análises de recuperação de desastre (failovers de teste) e as VMs em execução no Azure, como o SQL Server com Grupos de Disponibilidade AlwaysOn/Servidores de Nome de Domínio.

**Armazenamento**: Custo do consumo de armazenamento do Azure para as necessidades de DR. Ele inclui o consumo de armazenamento para a replicação e durante os exercícios de recuperação de desastre.
Rede: Custo de VPN site a site e ExpressRoute para as necessidades de DR. 

**Licença do ASR**: Custo da licença do Azure Site Recovery para todas as VMs compatíveis. Se você tiver inserido manualmente uma VM na tabela de análise de custo detalhada, o custo da licença do Azure Site Recovery também estará incluído para a VM.

### <a name="overall-dr-cost-by-states"></a>Custo geral de recuperação de desastre por estados
O custo total da recuperação de desastre é categorizado com base em dois estados diferentes - a replicação e a análise de recuperação de desastre.

**Custo de replicação**: O custo incorre no momento da replicação. Abrange o custo de armazenamento, de rede e de licença do Azure Site Recovery. 

**Custo de Análise de DR**: O custo incorre no momento das análises de DR. O Azure Site Recovery gira máquinas virtuais durante a análise de recuperação de desastre. O custo de análise de recuperação de desastre abrange os custos de computação e armazenamento das VMs em execução.
Duração total da análise de recuperação de desastre em um ano = Número de análises de recuperação de desastre x Duração de análise de cada recuperação de desastre (dias) Custo médio de análise de recuperação de desastre (por mês) = Custo total de análise de recuperação de desastre/12

### <a name="storage-cost-table"></a>Tabela de custo de armazenamento:
Esta tabela mostra o custo de armazenamento premium e standard incorrido para replicação e análises de recuperação de desastre com e sem desconto.

### <a name="site-to-azure-network"></a>Site para rede do Azure
Selecione a configuração apropriada de acordo com seus requisitos. 

**ExpressRoute**: Por padrão, a ferramenta seleciona o plano de ExpressRoute mais próximo que corresponda à largura de banda de rede necessária para a replicação delta. Você pode alterar o plano de acordo com suas necessidades.

**Gateway de VPN**: Selecione o Gateway de VPN, se houver algum no seu ambiente. Por padrão, é NA.

**Região de destino**: Região do Azure especificada para DR. O preço usado no relatório de computação, armazenamento, rede e licença baseia-se nos preços do Azure para essa região. 

### <a name="vm-running-on-azure"></a>VM em execução no Azure
Se você tiver qualquer controlador de domínio ou VM de DNS ou VM do SQL Server com Grupos de Disponibilidade AlwaysOn em execução no Azure para recuperação de desastre, poderá fornecer o número de máquinas virtuais e o tamanho para considerar os custos de computação no custo total de recuperação de desastre. 

### <a name="apply-overall-discount-if-applicable"></a>Aplicar desconto geral, se aplicável
Se você for um cliente ou um parceiro do Azure e estiver qualificado para qualquer desconto no preço geral do Azure, poderá usar esse campo. A ferramenta aplica o desconto (%) a todos os componentes.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Número de tipos de máquinas virtuais e custo de computação (por ano)
Esta tabela mostra o número de VMs do Windows e não Windows e o custo de computação de análise de recuperação de desastre para eles.

### <a name="settings"></a>Configurações 
**Usando disco gerenciado**: Especifica se o disco gerenciado está sendo usado no momento das análises de DR. O padrão é sim. Se você tiver definido -UseManagedDisks como não, ele usará o preço de disco não gerenciado para o cálculo de custos.

**Moeda**: A moeda na qual o relatório é gerado. Duração do custo:  É possível exibir todos os custos do mês ou do ano inteiro. 

## <a name="detailed-cost-analysis-table"></a>Tabela de análise detalhada de custo
![Análise de custo detalhada](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) A tabela lista a divisão de custo para cada VM compatível. Você também pode usar essa tabela para obter o custo estimado de recuperação de desastre do Azure de VMs não analisadas ao adicionar as máquinas virtuais manualmente. Isso será útil em casos onde é necessário estimar os custos do Azure para uma nova implantação de recuperação de desastre sem a execução de uma análise detalhada.
Para adicionar manualmente as máquinas virtuais: 
1.  Clique no botão 'Inserir linha' para inserir uma nova linha entre as linhas Início e Término.

2.  Preencha as colunas a seguir com base no tamanho aproximado da VM e no número de máquinas virtuais que correspondem a essa configuração: 

* Número de VMs, tamanho de IaaS (Sua seleção)
* Tipo de Armazenamento (Standard/Premium)
* Tamanho total de armazenamento da VM (GB)
* Número de detalhamentos de recuperação de desastre em um ano 
* Duração de cada detalhamento de recuperação de desastre (Dias) 
* Tipo de sistema operacional
* Redundância de dados 
* Benefício Híbrido do Azure

1. Você pode aplicar o mesmo valor a todas as VMs na tabela clicando no botão 'Aplicar a todos' para Número de Análises de Recuperação de Desastre em um ano, Duração da Análise de cada Recuperação de Desastre (Dias), Redundância de dados e Benefício Híbrido do Azure.

1. Clique em 'Recalcular custo' para atualizar o custo.

**Nome da VM**: O nome da VM.

**Número de VMs**: O número de VMs que correspondem à configuração. Você pode atualizar o número de VMs existentes se as VMs de configuração semelhantes não tiverem sido analisadas mas ainda assim serão protegidas.

**Tamanho de IaaS (recomendação)**: É o tamanho da função VM da VM compatível recomendado pela ferramenta. 

**Tamanho de IaaS (sua seleção)**: Por padrão, é o mesmo tamanho da função VM recomendado. Você pode alterar a função com base no requisito. O custo de computação se baseia no tamanho da função de VM selecionada.

**Tipo de armazenamento**: O tipo de armazenamento usado pela VM. É armazenamento standard ou premium.

**Tamanho total de armazenamento da VM (GB)**: Armazenamento total da VM.

**Número de análises de DR em um ano**: O número de vezes que você executa análises de DR em um ano. Por padrão, é quatro vezes ao ano. Você pode modificar o período de VMs específicas ou aplicar o novo valor a todas as VMs inserindo o novo valor na linha superior e clicando no botão 'Aplicar a todos'. Com base no número de Análises de Recuperação de Desastre e do período de duração de cada Análise de Recuperação de Desastre, o custo total da Análise de Recuperação de Desastre é calculado.  

**Duração de cada Análise de DR (dias)**: A duração de cada análise de DR. Por padrão, é de sete dias a cada 90 dias, de acordo com o [benefício do Software Assurance de Recuperação de Desastre](https://azure.microsoft.com/pricing/details/site-recovery). Você pode modificar o período de VMs específicas ou pode aplicar um novo valor a todas as VMs inserindo o novo valor na linha superior e clicando no botão 'Aplicar a todos'. O custo total da Análise de Recuperação de Desastre é calculado com base no número de análises de recuperação de desastre em um ano e no período de duração de cada Análise de Recuperação de Desastre.
  
**Tipo do SO**: O tipo do SO da VM. É Windows ou Linux. Se o tipo de sistema operacional for Windows, o Benefício Híbrido do Azure poderá ser aplicado a essa VM. 

**Redundância de dados**: Pode ser um dos seguintes: LRS (armazenamento com redundância local), GRS (armazenamento com redundância geográfica) ou RA-GRS (armazenamento com redundância geográfica com acesso de leitura). O padrão é LRS. Você pode alterar o tipo com base em sua conta de armazenamento para máquinas virtuais específicas ou pode aplicar o novo tipo a todas as VMs alterando o tipo da linha superior e clicando no botão 'Aplicar a todos'.  O custo de armazenamento para a replicação é calculado com base no preço de redundância de dados que você selecionou. 

**Benefício Híbrido do Azure**: É possível aplicar o Benefício Híbrido do Azure às VMs do Windows, se aplicável.  O padrão é Sim. Você pode alterar a configuração de máquinas virtuais específicas ou atualizar todas as máquinas virtuais clicando no botão 'Aplicar a todos'.

**Consumo total do Azure**: Inclui o custo de licença do Azure Site Recovery, armazenamento e computação para DR. Com base em sua seleção, mostra o custo mensal ou anual.

**Custo de replicação de estado contínuo**: Inclui o custo de armazenamento para replicação.

**Custo total de Análise de DR (média)**: Inclui o custo de computação e armazenamento para Análise de DR.

**Custo de licença do ASR**: Custo de licença do Azure Site Recovery.

## <a name="supported-target-regions"></a>Regiões de destino com suporte
O Planejador de Implantações do Azure Site Recovery fornece uma estimativa de custo para as seguintes regiões do Azure. Se sua região não estiver listada abaixo, você poderá usar qualquer uma das seguintes regiões com o preço mais próximo de sua região.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Moedas com suporte
O Planejador de Implantações do Azure Site Recovery pode gerar o relatório de custo com qualquer uma das moedas a seguir.

|Moeda|NOME||Moeda|NOME||Moeda|NOME|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentino ($)||AUD|Dólar australiano ($)||BRL|Real brasileiro (R$)|
|CAD|Dólar canadense ($)||CHF|Franco suíço. (chf)||DKK|Coroa dinamarquesa (kr)|
|EUR|Euro (€)||GBP|Libras esterlinas (£)||HKD|Dólar de Hong Kong (HK$)|
|IDR|Rúpia da Indonésia (Rp)||INR|Rúpia indiana (₹)||JPY|Iene japonês (¥)|
|KRW|Won coreano (₩)||MXN|Peso mexicano (MXN$)||MYR|Ringgit malaio (RM$)|
|NOK|Coroa norueguesa (kr)||NZD|Dólar neozelandês ($)||RUB|Rublo russo (руб)|
|SAR|Saudi Riyal (SR)||SEK|Coroa sueca (kr)||TWD|Dólar taiwanês (NT$)|
|TRY|Lira turca (TL)||USD| Dólar americano (US$)||ZAR|Rand da África do Sul (R)|

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como proteger [VMs do VMware para o Azure usando o Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).
