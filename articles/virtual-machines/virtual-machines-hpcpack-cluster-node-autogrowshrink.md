<properties
 pageTitle="Escalar automaticamente os recursos de computação no cluster HPC | Microsoft Azure"
 description="Saiba mais sobre formas de aumentar e reduzir automaticamente os recursos de computação em um cluster HPC Pack no Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Escalar vertical e horizontalmente de forma automática os recursos de computação do Azure em um cluster HPC Pack de acordo com a carga de trabalho do cluster

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Se você implantar nós de “disparo contínuo” do Azure em seu cluster HPC Pack ou criar um cluster HPC Pack em VMs do Azure, pode ser conveniente encontrar uma maneira de aumentar ou reduzir automaticamente os recursos de computação do Azure de acordo com a atual carga de trabalho de trabalhos e tarefas no cluster. Isso permite usar os recursos do Azure com mais eficiência e controlar seus custos. Para fazer isso, use o script **AzureAutoGrowShrink.ps1** do HPC PowerShell que está instalado com o HPC Pack.

>[AZURE.TIP]A partir do HPC Pack 2012 R2 Atualização 2, o HPC Pack inclui um serviço interno para aumentar e reduzir automaticamente os nós de disparo contínuo do Azure ou os nós de computação de VM do Azure. Configure o serviço com uma configuração no [script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) ou defina manualmente a propriedade **AutoGrowShrink** do cluster. Veja [Novidades no Microsoft HPC Pack 2012 R2 Atualização 2](https://technet.microsoft.com/library/mt269417.aspx).

## Pré-requisitos

* **Cluster HPC Pack 2012 R2 Atualização 1 ou posterior** - O script **AzureAutoGrowShrink.ps1** está instalado na pasta %CCP\_HOME%bin. O nó de cabeçalho do cluster pode ser implantado localmente ou em uma VM do Azure. Veja [Configurar um cluster híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó de cabeçalho local e os nós de “disparo contínuo” do Azure. Veja o [script de implantação de IaaS do HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md)) para implantar rapidamente um cluster HPC Pack em VMs do Azure.

* **Para um cluster com nós de disparo contínuo do Azure** - Execute o script em um computador cliente em que o HPC Pack está instalado ou no nó de cabeçalho. Se executado em um computador cliente, certifique-se de que você defina a variável $env:CCP\_SCHEDULER corretamente para apontar para o nó de cabeçalho. Os nós de “disparo contínuo” do Azure já devem ter sido adicionados ao cluster, mas podem estar no estado Não Implantado.


* **Para um cluster implantado em VMs do Azure** - Execute o script no nó de cabeçalho da VM, pois ele depende dos scripts **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** que estão instalados ali. Além disso, esses scripts exigem um certificado de gerenciamento ou um arquivo de configurações de publicação do Azure (veja [Gerenciar nós de computação em um cluster HPC Pack no Azure](virtual-machines-hpcpack-cluster-node-manage.md)). Verifique se todas as VMs de nó de computação necessárias já foram adicionadas ao cluster, mas elas podem estar no estado Interrompido.

## Sintaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Parâmetros

 * **NodeTemplates** - Nomes dos modelos de nó para definir o escopo para que os nós sejam aumentados ou reduzidos. Se não for especificado (o valor padrão é @()), todos os nós no grupo de nós **AzureNodes** estarão no escopo quando **NodeType** tiver um valor de AzureNodes, e todos os nós do grupo de nós **ComputeNodes** estarão no escopo quando **NodeType** tiver um valor de ComputeNodes.

 * **JobTemplates** - Nomes dos modelos de trabalho para definir o escopo para que os nós sejam aumentados.

 * **NodeType** - O tipo de nó para ser aumentado ou reduzido. Os valores para os quais há suporte são:

     * **AzureNodes** – para os nós (de disparo contínuo) de PaaS do Azure em cluster local ou de IaaS do Azure.

     * **ComputeNodes** - para VMs de nó de computação apenas em um cluster de IaaS do Azure.

* **NumOfQueuedJobsPerNodeToGrow** - Número de trabalhos em fila necessários para aumentar um nó.

* **NumOfQueuedJobsToGrowThreshold** - O número de trabalhos em fila para iniciar o processo de aumento.

* **NumOfActiveQueuedTasksPerNodeToGrow** - O número de tarefas em fila ativas necessárias para aumentar um nó. Se **NumOfQueuedJobsPerNodeToGrow** for especificado com um valor maior que 0, esse parâmetro será ignorado.

* **NumOfActiveQueuedTasksToGrowThreshold**- O número de tarefas em fila ativas para iniciar o processo de aumento.

* **NumOfInitialNodesToGrow** - O número mínimo inicial de nós a ser aumentado se todos os nós no escopo forem **Não Implantados** ou **Interrompidos (Desalocados)**.

* **GrowCheckIntervalMins** - O intervalo em minutos entre as verificações a ser aumentado.

* **ShrinkCheckIntervalMins** - O intervalo em minutos entre as verificações a ser reduzido.

* **ShrinkCheckIdleTimes**- O número de verificações de redução contínua (separadas por **ShrinkCheckIntervalMins**) para indicar que os nós estão ociosos.

* **UseLastConfigurations*** as configurações anteriores salvas no arquivo de argumento.

* **ArgFile**- O nome do arquivo argumento usado para salvar e atualizar as configurações para executar o script.

* **LogFilePrefix**- O nome do prefixo do arquivo de log. Você pode especificar um caminho. Por padrão, o log é gravado no atual diretório de trabalho.

### Exemplo 1

O exemplo a seguir configura os nós de disparo contínuo do Azure implantados com o Modelo Padrão AzureNode para aumentar ou reduzir automaticamente. Se todos os nós estiverem inicialmente no estado **Não Implantado**, pelo menos três nós serão iniciados. Se o número de trabalhos em fila exceder oito, o script iniciará os nós até seu número exceder a taxa de trabalhos em fila para **NumOfQueuedJobsPerNodeToGrow**. Se for descoberto que um nó está ocioso em 3 tempos ociosos consecutivos, ele será interrompido.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Exemplo 2

O exemplo a seguir configura as VMs de nó de computação do Azure implantadas com o Modelo Padrão ComputeNode para serem aumentadas ou reduzidas automaticamente. Os trabalhos configurados pelo modelo de trabalho Padrão definem o escopo da carga de trabalho no cluster. Se todos os nós forem interrompidos inicialmente, pelo menos cinco nós serão iniciados. Se o número de tarefas em fila ativas exceder quinze, o script iniciará os nós até seu número exceder a taxa de tarefas em fila ativas para **NumOfActiveQueuedTasksPerNodeToGrow**. Se for descoberto que um nó está ocioso em 10 tempos ociosos consecutivos, ele será interrompido.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO3-->