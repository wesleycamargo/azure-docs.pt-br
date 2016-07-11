<properties
 pageTitle="Escalar automaticamente os recursos de computação no cluster HPC | Microsoft Azure"
 description="Aumentar e reduzir automaticamente o número de nós de computação de cluster do HPC Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="04/14/2016"
 ms.author="danlep"/>

# Aumentar e reduzir automaticamente os recursos de cluster do HPC Pack no Azure conforme a carga de trabalho de cluster




Se você implantar nós de “disparo contínuo” no seu cluster do HPC Pack ou criar um cluster HPC Pack em VMs do Azure, poderá ser conveniente encontrar uma maneira de aumentar ou reduzir automaticamente o número de recursos de computação do Azure como os núcleos de acordo com a atual carga de trabalho atual no cluster. Isso permite usar os recursos do Azure com mais eficiência e controlar seus custos. Para fazer isso, configure a propriedade de cluster de HPC Pack **AutoGrowShrink**. Ou então, use o script **AzureAutoGrowShrink.ps1** do script HPC PowerShell que é instalado com o HPC Pack.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Além disso, no momento somente é possível aumentar ou reduzir automaticamente nós de computação HPC Pack que estão em execução em um sistema operacional Windows Server.

## Definir a propriedade de cluster AutoGrowShrink

### Pré-requisitos

* **Cluster do HPC Pack 2012 R2 Atualização 2 ou posterior**: o nó de cabeçalho do cluster pode ser implantado localmente ou em uma VM do Azure. Veja [Configurar um cluster híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó de cabeçalho local e os nós de “disparo contínuo” do Azure. Consulte o [script de implantação de IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implantar rapidamente um cluster HPC Pack em VMs do Azure.


* **Para um cluster com um nó principal no Azure**: se você usar o script de implantação do IaaS HPC Pack para criar o cluster, habilite a propriedade de cluster **AutoGrowShrink** definindo a opção AutoGrowShrink no arquivo de configuração do cluster. Para obter detalhes, consulte a documentação que acompanha o [download do script](https://www.microsoft.com/download/details.aspx?id=44949).

    Como alternativa, defina a propriedade de cluster **AutoGrowShrink** depois de implantá-lo usando os comandos do PowerShell HPC descritos na seção a seguir. Para usar o PowerShell HPC para fazer isso, primeiro conclua as seguintes etapas:
    1. Configure o certificado de gerenciamento do Azure no nó de cabeçalho na assinatura do Azure. Para uma implantação de teste, você pode usar o certificado autoassinado padrão do Microsoft HPC Azure que instala o HPC Pack no nó principal e simplesmente carregar esse certificado para sua assinatura do Azure. Para ver as opções e as etapas, consulte a [diretriz da Biblioteca do TechNet](https://technet.microsoft.com/library/gg481759.aspx).
    2. Execute **regedit** no nó principal, vá para HKLM\\SOFTWARE\\Micorsoft\\HPC\\IaasInfo e adicione um novo valor de cadeia de caracteres. Defina o nome do valor como "Impressão digital" e dados de Valor para a impressão digital do certificado na Etapa 1.


### Comandos do PowerShell HPC para definir a propriedade AutoGrowShrink

A seguir estão exemplos de comandos do PowerShell HPC para definir **AutoGrowShrink** e ajustar seu comportamento com parâmetros adicionais. Consulte [parâmetros de AutoGrowShrink](#AutoGrowShrink-parameters) posteriormente neste artigo para obter uma lista de configurações.

Para executar esses comandos, inicie o PowerShell HPC no nó principal do cluster como um administrador.

**Para habilitar a propriedade AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 1

**Para desabilitar a propriedade AutoGrowShrink**

    Set-HpcClusterProperty –EnableGrowShrink 0

**Para alterar o intervalo de crescimento em minutos**

    Set-HpcClusterProperty –GrowInterval <interval>

**Para alterar o intervalo de diminuição em minutos**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**Para exibir a configuração atual de AutoGrowShrink**

    Get-HpcClusterProperty –AutoGrowShrink

### Parâmetros de AutoGrowShrink

A seguir estão os parâmetros de AutoGrowShrink que podem ser modificados usando o comando **Set-HpcClusterProperty**.

* **EnableGrowShrink**: alterne para habilitar ou desabilitar a propriedade **AutoGrowShrink**.
* **ParamSweepTasksPerCore**: número de tarefas de limpeza paramétrica para aumentar um núcleo. O padrão é a aumentar um núcleo por tarefa.
 
    >[AZURE.NOTE] O QFE KB3134307 do HPC Pack altera o **ParamSweepTasksPerCore** para **TasksPerResourceUnit**. Ele se baseia no tipo de recurso de trabalho e pode ser nó, soquete ou núcleo.
    
* **GrowThreshold**: limite de tarefas em fila para disparar o crescimento automático. O padrão é 1, o que significa que, se houver 1 ou mais tarefas no estado em fila, os nós aumentarão automaticamente.
* **GrowInterval**: intervalo em minutos para disparar o crescimento automático. O intervalo padrão é 5 minutos.
* **ShrinkInterval**: intervalo em minutos para disparar a redução automática. O intervalo padrão é 5 minutos.
* **ShrinkIdleTimes**: o número de verificações contínuas para reduzir para indicar que os nós estão ociosos. O padrão é 3. Por exemplo, se o **ShrinkInterval** for de 5 minutos, o HPC Pack verifica se o nó está ocioso a cada 5 minutos. Se os nós estiverem no estado ocioso após 3 verificações contínuas (15 minutos), o HPC Pack reduzirá esse nó.
* **ExtraNodesGrowRatio**: percentual adicional de nós para crescimento de trabalhos de MPI (Interface Transferência de Mensagens). O valor padrão é 1, o que significa que o HPC Pack aumenta 1% dos nós para trabalhos MPI.
* **GrowByMin**: alterne para indicar se a política de crescimento automático se baseia nos recursos mínimos necessários para o trabalho. O padrão é false, o que significa que o HPC Pack aumenta os nós para trabalhos com base nos recursos máximo necessários para os trabalhos.
* **SoaJobGrowThreshold**: limite de solicitações SOA de entrada para disparar o processo de crescimento automático. O valor padrão é 50000.
    
    >[AZURE.NOTE] Esse parâmetro tem suporte a partir do HPC Pack 2012 R2 Atualização 3.
    
* **SoaRequestsPerCore**: o número de solicitações SOA de entrada para aumentar um núcleo. O valor padrão é 20000.

    >[AZURE.NOTE] Esse parâmetro tem suporte a partir do HPC Pack 2012 R2 Atualização 3.

### Exemplo de MPI

Por padrão, o HPC Pack aumenta 1% de nós extras para trabalhos MPI (**ExtraNodesGrowRatio** está definido como 1). O motivo é que MPI pode exigir vários nós e o trabalho pode ser executado somente quando todos os nós estiverem prontos. Quando o Azure inicia os nós, ocasionalmente, um nó pode precisar de mais tempo para iniciar que os outros, fazendo com que outros nós fiquem ociosos enquanto aguardam esse nó ficar pronto. Ao aumentar nós extras, o HPC Pack reduz o tempo de espera desse recurso e potencialmente reduz os custos. Para aumentar o percentual de nós extras para trabalhos de MPI (por exemplo, para 10%), execute um comando semelhante a

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### Exemplo de SOA

Por padrão, **SoaJobGrowThreshold** é definido como 50000 e **SoaRequestsPerCore** é definido como 200000. Se você enviar um trabalho SOA com 70000 solicitações, haverá uma tarefa na fila e as solicitações de entrada serão 70000. Nesse caso, o HPC Pack aumenta 1 núcleo para a tarefa em fila, e para solicitações de entrada aumentará (70000 - 50000)/20000 = 1 núcleo, portanto o total aumentará em 2 núcleos para este trabalho SOA.

## Executar o script AzureAutoGrowShrink.ps1

### Pré-requisitos

* **Cluster HPC Pack 2012 R2 Atualização 1 ou posterior** - O script **AzureAutoGrowShrink.ps1** está instalado na pasta %CCP\_HOME%bin. O nó de cabeçalho do cluster pode ser implantado localmente ou em uma VM do Azure. Veja [Configurar um cluster híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para começar com um nó de cabeçalho local e os nós de “disparo contínuo” do Azure. Consulte o [script de implantação IaaS do HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implantar rapidamente um cluster HPC Pack em VMs do Azure ou use um [modelo de início rápido do Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12**: o script depende atualmente desta versão específica do Azure PowerShell. Se você estiver executando uma versão mais recente no nó principal, terá que fazer downgrade do Azure PowerShell para a [versão 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para executar o script.

* **Para um cluster com nós de disparo contínuo do Azure** - Execute o script em um computador cliente em que o HPC Pack está instalado ou no nó de cabeçalho. Se executado em um computador cliente, certifique-se de que você defina a variável $env:CCP\_SCHEDULER corretamente para apontar para o nó de cabeçalho. Os nós de “disparo contínuo” do Azure já devem ter sido adicionados ao cluster, mas podem estar no estado Não Implantado.


* **Para um cluster implantado em VMs do Azure** - Execute o script no nó de cabeçalho da VM, pois ele depende dos scripts **Start-HpcIaaSNode.ps1** e **Stop-HpcIaaSNode.ps1** que estão instalados ali. Além disso, esses scripts exigem um certificado de gerenciamento ou um arquivo de configurações de publicação do Azure (veja [Gerenciar nós de computação em um cluster HPC Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)). Verifique se todas as VMs de nó de computação necessárias já foram adicionadas ao cluster. Eles podem estar no estado Parado.

### Sintaxe

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
### Parâmetros

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

* **ShrinkCheckIdleTimes**: o número de verificações de redução contínuas (separado por **ShrinkCheckIntervalMins**) para indicar que os nós estão ociosos.

* **UseLastConfigurations**: as configurações anteriores salvas no arquivo de argumento.

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

<!---HONumber=AcomDC_0629_2016-->