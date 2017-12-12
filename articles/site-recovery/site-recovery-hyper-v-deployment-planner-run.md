---
title: "Planejador de Implantações do Azure Site Recovery para Hyper-V para o Azure | Microsoft Docs"
description: "Este artigo descreve o modo de execução do planejador de implantações do Azure Site Recovery para o cenário do Hyper-V para o Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: bb4ec5cfd455ab0cc22ab693c2a07eed9883dc76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Executar o planejador de implantações do Azure Site Recovery para Hyper-V para o Azure

## <a name="modes-of-running-deployment-planner"></a>Modos do Planejador de Implantações em execução
Você pode executar a ferramenta de linha de comando (ASRDeploymentPlanner.exe) em qualquer um dos quatro seguintes modos: 
1.  [Obter a lista de VMs](#get-vm-list-for-profiling-hyper-v-vms)
2.  [Criação de perfil](#profile-hyper-v-vms)
3.  [Geração de relatórios](#generate-report)
4.  [Obter taxa de transferência](#get-throughput)

Primeiro, execute a ferramenta para obter a lista de VMs de um único host de Hyper-V, ou de vários.  Depois, execute a ferramenta no modo de criação de perfil para obter a variação de dados de VM e IOPS. Em seguida, execute a ferramenta para gerar o relatório e obter os requisitos de armazenamento e largura de banda de rede.

## <a name="get-vm-list-for-profiling-hyper-v-vms"></a>Obter a lista de VMs para criação de perfil de VMs do Hyper-V
Primeiro, você precisa de uma lista de VMs para a criação de perfil. Use o modo GetVMList da ferramenta de planejamento de implantação para gerar a lista de VMs presentes em vários hosts do Hyper-V em um único comando. Depois de gerar a lista completa, remova as VMs para as quais não deseja criar um perfil do arquivo de saída. Depois, use o arquivo de saída para todas as outras operações: criação de perfil, geração de relatórios e obtenção da taxa de transferência.

Você pode gerar a lista de VMs apontando a ferramenta para um cluster do Hyper-V ou um host do Hyper-V autônomo, ou uma combinação de todos.

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| Nome do parâmetro | Descrição |
|---|---|
| -Operation | GetVMList |
| -User | Nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo.|
|-ServerListFile | O arquivo com a lista de servidores contendo as VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. Esse arquivo deve conter uma das seguintes opões em cada linha:<ul><li>Nome do host ou endereço IP do Hyper-V</li><li>Nome do cluster ou endereço IP do Hyper-V</li></ul><br>Por exemplo, o arquivo "ServerList.txt" contém as seguintes servidores:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Opcional) A UNC (convenção de nomenclatura universal) ou o caminho do diretório local para armazenar dados gerados durante essa operação. Se não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como diretório padrão.|
|-OutputFile| (Opcional) O arquivo onde a lista de VMs obtida dos servidores específicos do Hyper-V é salvo. Se o nome não tiver sido mencionado, os detalhes estariam armazenados em "VMList.txt".  Use o arquivo para iniciar a criação de perfil depois de remover as VMs que não precisam de criação de perfil.|
|-Password|(Opcional) Senha para se conectar ao host do Hyper-V.   Se a senha não for especificada como um parâmetro, você deverá fornecê-la mais tarde, quando o comando for executado.|

### <a name="how-does-getvmlist-discovery-work"></a>Como funciona a descoberta de GetVMList?
**Cluster do Hyper-V**: quando o nome do cluster Hyper-V for fornecido no arquivo de lista de servidores, a ferramenta localiza todos os nós do Hyper-V do cluster e obtém as VMs presentes em cada um dos hosts do Hyper-V.

**Host do Hyper-V**: quando o nome de host do Hyper-V for fornecido, a ferramenta verificará primeiro se ele pertence a um cluster. Se pertencer, ele buscará os nós que pertencem ao cluster. Em seguida, ele obterá as VMs de cada host do Hyper-V. 

Também é possível listar em um arquivo os nomes amigáveis ou os endereços IP das VMs para as quais deseja criar o perfil manualmente.

Abra o arquivo de saída no Bloco de Notas e copie os nomes de todas as VMs para as quais deseja criar o perfil para outro arquivo (por exemplo, ProfileVMList.txt), com um nome de VM por linha. Esse arquivo é usado como entrada para o parâmetro - VMListFile da ferramenta para todas as outras operações: criação de perfil, geração de relatórios e obtenção da taxa de transferência.

### <a name="example-1-to-store-the-list-of-vms-in-a-file"></a>Exemplo 1: armazenar a lista de VMs em um arquivo
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

### <a name="example-2-to-store-the-list-of-vms-at-the-default-location-ie--directory-path"></a>Exemplo 2: armazenar a lista de VMs no local padrão, ou seja, caminho do diretório
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Perfil de VMs do Hyper-V
No modo de criação de perfil, a ferramenta de planejamento de implantação conecta-se a cada um dos hosts do Hyper-V para coletar dados de desempenho sobre as VMs. 

* A criação de perfil não afeta o desempenho das VMs de produção, pois nenhuma conexão direta é feita a elas. Todos os dados de desempenho são coletados do host do Hyper-V.
* A ferramenta consulta o host do Hyper-V uma vez a cada 15 segundos para garantir a precisão da criação de perfil e armazena a média dos dados de contador de desempenho de cada minuto.
* A ferramenta lida perfeitamente com a migração da VM de um nó para outro nó no cluster, e com a migração de armazenamento em um host.

### <a name="get-vm-list-to-profile"></a>Obter a lista de VMs para criação de perfil
Consulte a operação [GetVMList](#get-vm-list-for-profiling-hyper-v-vms) para criar uma lista de VMs para criação de perfil

Após obter a lista de VMs para criação de perfil, você pode executar a ferramenta no modo de criação de perfil. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de criação de perfil. A ferramenta é comum para os cenários de VMware no Azure e do Hyper-V para Azure. Os parâmetros a seguir são aplicáveis ao Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| Nome do parâmetro | Descrição |
|---|---|
| -Operation | StartProfiling |
| -User | Nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo.|
| -VMListFile | O arquivo com a lista de VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM separado por uma \ por linha. O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<ul>Por exemplo: o arquivo "VMList.txt" contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|O número de minutos pelos quais a análise deve ser executada. O mínimo é 30 minutos.|
|-NoOfHoursToProfile|O número de horas pelas quais a análise deve ser executada.|
|-NoOfDaysToProfile |O número de dias pelos quais a criação de perfil deve ser executada. É recomendável executar a criação de perfil por mais de sete dias garantir que o padrão de carga de trabalho no ambiente durante o período especificado seja observado e usado para fornecer uma recomendação precisa.|
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) A UNC (convenção de nomenclatura universal) ou o caminho do diretório local para armazenar dados de criação de perfil gerados durante a criação de perfil. Se não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como diretório padrão.|
|-Password|(Opcional) A senha para se conectar ao host do Hyper-V. Se não for especificado agora, você deverá fornecê-la depois, durante a execução do comando.|
|-StorageAccountName|(Opcional) O nome da conta de armazenamento que é usado para localizar a taxa de transferência possível para replicação de dados do local do Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para calcular a taxa de transferência.|
|-StorageAccountKey|(Opcional) A chave de conta de armazenamento que é usada para acessar a conta de armazenamento. Acesse o Portal do Azure > Contas de armazenamento > <Storage account name> > Configurações > Chaves de Acesso > Chave1 (ou chave de acesso primário para a conta de armazenamento clássico).|
|-Ambiente|(opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino é uma nuvem do Governo dos EUA do Azure ou Azure China.|

É recomendável que você analise suas VMs por mais de sete dias. Se o padrão de variação oscilar muito em um mês, recomendamos a análise durante a semana quando você vir a variação máxima. A melhor maneira é analisar por 31 dias para obter a melhor recomendação. Durante o período de criação de perfil, ASRDeploymentPlanner.exe continua em execução. A ferramenta aceita a entrada de tempo de criação de perfil em dias. Para um teste rápido da ferramenta ou para uma prova de conceito, você pode analisar por algumas horas ou minutos. O tempo de criação de perfil mínimo permitido é de 30 minutos. 

Durante a criação de perfil, opcionalmente, você pode passar um nome de conta de armazenamento e uma chave para obter a taxa de transferência que o Azure Site Recovery pode alcançar no momento da replicação do servidor do Hyper-V para o Azure. Se a chave e o nome da conta de armazenamento não forem passados durante a criação de perfil, a ferramenta não calculará a taxa de transferência possível.

Você pode executar várias instâncias da ferramenta para diversos conjuntos de VMs. Verifique se os nomes de VM não são repetidos nos conjuntos de criação de perfil. Por exemplo, se tiver criado o perfil de 10 VMs (VM1 a VM10) e, após alguns dias, quiser criar o perfil de outras cinco VMs (VM11 a VM15), você poderá executar a ferramenta em outro console de linha de comando para o segundo conjunto de VMs (VM11 a VM15). Verifique se o segundo conjunto de VMs não tem nomes de VM da primeira instância de criação de perfil ou use um diretório de saída diferente para a segunda execução. Se forem usadas duas instâncias da ferramenta para a criação de perfil das mesmas VMs e for usado o mesmo diretório de saída, o relatório gerado será incorreto. 

As configurações de VM são capturadas uma vez no início da operação de criação de perfil e armazenadas em um arquivo chamado VMDetailList.xml. Essas informações são usadas quando o relatório é gerado. Não é capturada alteração alguma na configuração de VM (por exemplo, um número maior de núcleos, discos ou NICs) do início ao fim da criação de perfil. Se uma configuração de VM com criação de perfil tiver sido alterada durante a criação de perfil, aqui está a solução alternativa para obter os detalhes mais recentes da VM ao gerar o relatório:

* Faça backup de VMdetailList.xml e exclua o arquivo do local atual.
* Passe os argumentos -User e -Password no momento da geração de relatórios

O comando de criação de perfil gera vários arquivos no diretório de criação de perfil. Não exclua nenhum dos arquivos, pois isso afeta a geração de relatórios.

### <a name="examples"></a>Exemplos
#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: criar o perfil de VMs para 30 dias e obter a taxa de transferência do local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exemplo 2: criar o perfil de VMs por 15 dias
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Exemplo 3: Analisar as VMs por 60 minutos para um teste rápido da ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Exemplo 4: Analisar as VMs por duas horas para uma prova de conceito
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

>[OBSERVAÇÃO!]
>
* Se o servidor em que a ferramenta está sendo executada for reinicializado ou falhar ou se você fechar a ferramenta usando Ctrl + C, os dados de criação de perfil serão preservados. Porém, há uma possibilidade de perder os últimos 15 minutos de dados de criação de perfil. Em casos assim, execute novamente a ferramenta no modo de criação de perfil depois que o servidor for reiniciado.
* Quando o nome da conta de armazenamento e a chave são passados, a ferramenta mede a taxa de transferência na última etapa de criação de perfil. Se a ferramenta for fechada antes da conclusão da criação de perfil, a taxa de transferência não será calculada. Para obter a taxa de transferência antes de gerar o relatório, você pode executar a operação GetThroughput no console de linha de comando. Caso contrário, o relatório gerado não conterá as informações de taxa de transferência.
* O Azure Site Recovery não oferece suporte a VMs que possuem discos de passagem e iSCSI. No entanto, a ferramenta não pode detectar e criar perfil para discos de passagem e iSCSI anexados às VMs.

## <a name="generate-report"></a>Gerar relatório
A ferramenta gera um arquivo do Microsoft Excel com macros habilitadas (arquivo XLSM) como a saída de relatório, que resume todas as recomendações de implantação. O relatório é denominado DeploymentPlannerReport_<unique numeric identifier>.xlsm e colocado no diretório especificado.
Após a conclusão da criação de perfil, você poderá executar a ferramenta no modo de geração de relatório. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de geração de relatórios. A ferramenta é comum para os cenários de VMware no Azure e do Hyper-V para Azure. Os parâmetros a seguir são aplicáveis ao Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| Nome do parâmetro | Descrição |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | O arquivo que contém a lista de VMs com criação de perfil para as quais o relatório deve ser gerado. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM separado por uma \ por linha. O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<ul>Por exemplo: o arquivo "VMList.txt" contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) A UNC (convenção de nomenclatura universal) o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se um nome não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como o diretório padrão.|
| -User | (Opcional) Nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo.<br>Usuário e senha são usados para buscar as informações mais recentes de configuração das VMs, como o número de discos, o número de núcleos, o número de NICs etc. a serem usados no relatório. Se não for fornecido, as informações de configuração coletadas durante a criação de perfil serão usadas.|
|-Password|(Opcional) A senha para se conectar ao host do Hyper-V. Se não for especificado agora, você deverá fornecê-la depois, durante a execução do comando.|
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação desejado, em minutos. O padrão é de 15 minutos.|
| -Bandwidth | (Opcional) Largura de banda em Mbps. O parâmetro a ser usado para calcular o RPO que pode ser obtido para a largura de banda especificada. |
| -StartDate | (Opcional) A data e a hora de início em MM-DD-YYYY:HH:MM (formato de 24 horas). *StartDate* deve ser especificado junto com *EndDate*. Quando StartDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e a hora de término em MM-DD-YYYY:HH:MM (formato de 24 horas). *EndDate* deve ser especificado junto com *StartDate*. Quando EndDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expressado como uma porcentagem. O padrão é 30%. |
| -UseManagedDisks | (Opcional) UseManagedDisks - Sim/Não. O padrão é Sim. O número de máquinas virtuais que podem ser colocadas em uma única conta de armazenamento é calculado considerando-se o Failover/Failover de teste de máquinas virtuais é feito em um disco gerenciado em vez de em um disco não gerenciado. |
|-SubscriptionId |(Opcional) A Guid da assinatura. Use esse parâmetro para gerar um relatório de estimativa de custo com os preços mais recentes com base em sua assinatura, a oferta associada à sua assinatura e para a região do Azure de destino específica na moeda especificada.|
|-TargetRegion|(Opcional) A região do Azure que é o destino da replicação. Como o Azure tem custos diferentes por região, para gerar o relatório com a região do Azure de destino específica, use este parâmetro.<br>O padrão é WestUS2 ou a última região de destino usada.<br>Consulte a lista de [regiões de destino com suporte](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) A oferta associada a determinada assinatura. O padrão é MS-AZR - 0003P (Pré-pago).|
|-Currency|(Opcional) A moeda na qual o custo é mostrado no relatório gerado. O padrão é dólar americano ($) ou a última moeda usada.<br>Consulte a lista de [moedas com suporte](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

### <a name="examples"></a>Exemplos
#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exemplo 1: gerar um relatório com os valores padrão quando os dados de criação de perfil estiverem na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exemplo 2: gerar um relatório quando os dados de criação de perfil estiverem em um servidor remoto
Você deve ter acesso de leitura/gravação no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V - -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-that-you-will-be-provision-for-the-replication"></a>Exemplo 3: Gerar um relatório com uma largura de banda específica que será provisionada para a replicação
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exemplo 4: gerar um relatório com um fator de crescimento de 5% em vez do padrão de 30% 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exemplo 5: gerar um relatório com um subconjunto dos dados de criação de perfil
Por exemplo, você tem 30 dias de dados de criação de perfil e deseja gerar um relatório de apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minutes-rpo"></a>Exemplo 6: gerar um relatório para o RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Exemplo 7: Gerar um relatório para a região Sul da Índia do Azure com Rúpia Indiana e ID da oferta específica
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil usado para o cálculo
**Qual valor de percentil padrão das métricas de desempenho coletadas durante a criação de perfil a ferramenta usa ao gerar um relatório?**

A ferramenta usa como padrão os valores do 95º percentil de IOPS de leitura/gravação, IOPS de gravação e variação de dados coletados durante a criação de perfil de todas as VMs. Essa métrica garante que o pico do 100º percentil que suas VMs poderão ver devido a eventos temporários não seja usado para determinar seus requisitos de conta de armazenamento de destino e largura de banda de origem. Por exemplo, um evento temporário pode ser um trabalho de backup executado uma vez por dia, uma indexação de um banco de dados periódica ou uma atividade de geração de relatórios de análise ou outros eventos pontuais semelhantes de curta duração.

O uso de valores do 95º percentil proporciona a visão verdadeira das características de carga de trabalho reais e oferece o melhor desempenho quando as cargas de trabalho estão em execução no Azure. Não prevemos que você precise alterar esse número. Se alterar o valor (para o 90º percentil, por exemplo), você poderá atualizar o arquivo de configuração ASRDeploymentPlanner.exe.config na pasta padrão e salvá-lo para gerar um novo relatório nos dados de criação de perfil existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Considerações sobre o fator de crescimento
**Por que deve considerar o fator de crescimento ao planejar implantações?**
É fundamental levar em conta o crescimento em suas características de carga de trabalho, supondo um aumento potencial no uso ao longo do tempo. Depois que proteção estiver em vigor, se as características de carga de trabalho forem alteradas, você não poderá alternar para outra conta de armazenamento para proteção sem desabilitar e reabilitar a proteção.

Por exemplo, digamos que hoje a VM esteja em uma conta de replicação de armazenamento standard. Nos próximos três meses, várias alterações têm probabilidade de ocorrer:

* O número de usuários do aplicativo que é executado na VM aumentará.
* A maior variação resultante na VM exigirá que a VM vá para o armazenamento premium para que a replicação do Azure Site Recovery possa acompanhar o ritmo.
* Consequentemente, você precisará desabilitar e reabilitar a proteção para uma conta de armazenamento premium.

É altamente recomendável que você planeje o crescimento durante o planejamento da implantação, e enquanto o valor padrão é de 30%; você é o especialista no padrão de uso e projeções de crescimento de seu aplicativo, e pode alterar esse número adequadamente ao gerar um relatório. Além disso, você pode gerar vários relatórios com vários fatores de crescimento com os mesmos dados de criação de perfil e determinar quais recomendações de armazenamento de destino e largura de banda de origem funcionam melhor para você. 

O relatório gerado do Microsoft Excel contém as seguintes informações:

* [Resumo local](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recomendações](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Posicionamento de VM<->Storage](site-recovery-hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [VMs compatíveis](site-recovery-hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [VMs incompatíveis](site-recovery-hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Requisito de armazenamento local](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Lote de IR](site-recovery-hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Estimativa de custo](site-recovery-hyper-v-deployment-planner-cost-estimation.md)

![Relatório do planejador de implantação](media/site-recovery-hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Obter taxa de transferência
Para estimar a taxa de transferência que o Azure Site Recovery pode obter do local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula a taxa de transferência do servidor em que a ferramenta está em execução. Idealmente, esse servidor é o servidor do Hyper-V cujas VMs devem ser protegidas. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando 
Abra um console de linha de comando e acesse a pasta da ferramentas de planejamento de implantação do Azure Site Recovery. Execute ASRDeploymentPlanner.exe com os parâmetros a seguir
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 Nome do parâmetro | Descrição |
|---|---|
| -Operation | GetThroughtput |
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) A UNC (convenção de nomenclatura universal) o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se um nome não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como o diretório padrão.|
| -StorageAccountName | O nome de conta de armazenamento usada para obter a largura de banda consumida para replicação de dados do local para o Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para obter a largura de banda consumida. |
| -StorageAccountKey | A chave da conta de armazenamento usada para acessar a conta de armazenamento. Vá para o portal do Azure > Contas de armazenamento ><*Nome da conta de armazenamento*> > Configurações > Chaves de Acesso > Chave1.|
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil para calcular a largura de banda consumida. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM separado por uma \ por linha. O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<ul>Por exemplo: o arquivo "VMList.txt" contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Ambiente|(opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino é uma nuvem do Governo dos EUA do Azure ou Azure China|


A ferramenta cria vários arquivos asrvhdfile<#>. vhd de 64 MB (em que "#" é o número de arquivos) no diretório especificado. A ferramenta carrega os arquivos para a conta de armazenamento para obter a taxa de transferência. Depois que a taxa de transferência é medida, a ferramenta exclui todos os arquivos da conta de armazenamento e do servidor local. Se a ferramenta for encerrada por qualquer motivo enquanto estiver calculando a taxa de transferência, não excluirá os arquivos do armazenamento ou do servidor local. Será necessário excluí-los manualmente.

A taxa de transferência é medida em um momento específico e é a taxa de transferência máxima que o Azure Site Recovery pode atingir durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se qualquer aplicativo começar a consumir mais largura de banda na mesma rede, a taxa de transferência real variará durante a replicação. O resultado da taxa de transferência medida será diferente se a operação GetThroughput for executada quando as VMs protegidas tiverem alta variação de dados. É recomendável que você execute a ferramenta em vários pontos no tempo durante a criação de perfil para compreender quais níveis de taxa de transferência podem ser obtidos em vários momentos. No relatório, a ferramenta mostra a taxa de transferência medida por último.
    
### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

[OBSERVAÇÃO!]
>
> Execute a ferramenta em um servidor que tenha o mesmo armazenamento e as mesmas características de CPU que o servidor do Hyper-V.
>
> Para a replicação, defina a largura de banda recomendada para atender ao RPO 100% do tempo. Depois que você definir a largura de banda correta, se não houver um aumento na taxa de transferência obtida relatada pela ferramenta, faça o seguinte:
>
>  1. Verifique se há QoS (Qualidade de Serviço de Rede) que está limitando a taxa de transferência do Azure Site Recovery.
>
>  2. Verifique se o cofre do Azure Site Recovery está na região do Microsoft Azure com suporte mais próxima fisicamente para minimizar a latência de rede.
>
>  3. Verifique as características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, de HDD para SSD).
>

## <a name="next-steps"></a>Próximas etapas
* [Analise o relatório gerado](site-recovery-hyper-v-deployment-planner-analyze-report.md).