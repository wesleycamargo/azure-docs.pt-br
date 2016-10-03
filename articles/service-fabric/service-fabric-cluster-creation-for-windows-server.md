<properties
   pageTitle="Criar e gerenciar um cluster do Azure Service Fabric autônomo | Microsoft Azure"
   description="Saiba como criar e gerenciar um cluster do Azure Service Fabric em qualquer computador (físico ou virtual) executando o Windows Server, local ou em qualquer nuvem."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="dkshir;chackdan"/>


# Criar e gerenciar um cluster em execução no Windows Server

O Azure Service Fabric permite a criação de clusters do Service Fabric em quaisquer máquinas virtuais ou computadores que estiverem executando o Windows Server. Isso significa que você pode implantar e executar os aplicativos do Service Fabric em qualquer ambiente que tenha um conjunto de computadores com o Windows Server interconectados, no local ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para criar os clusters do Service Fabric denominado pacote do Windows Server autônomo.

Este artigo explica as etapas necessárias para criar um cluster usando o pacote autônomo do Service Fabric local, mas ele pode ser adaptada facilmente a qualquer outro ambiente, como provedores na nuvem.

>[AZURE.NOTE] Este pacote autônomo do Windows Server pode conter recursos que estejam em preview no momento e não tenham suporte para uso comercial. Para ver a lista de recursos que estão em preview, role para baixo até o final deste documento. [Clique aqui](http://go.microsoft.com/fwlink/?LinkID=733084) se quiser baixar uma cópia do EULA agora.

<a id="downloadpackage"></a>
## Baixar o pacote autônomo do Service Fabric


[Baixe o pacote autônomo do Service Fabric para Windows Server 2012 R2 e versões posteriores](http://go.microsoft.com/fwlink/?LinkId=730690), que se chama *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

>[AZURE.NOTE] Se você estiver usando os navegadores Internet Explorer ou Microsoft Edge para baixar esse pacote, adicione o site [http://download.microsoft.com](http://download.microsoft.com) aos sites confiáveis na intranet para impedir que a marcação Zonas seja gravada nos arquivos ao baixar o arquivo zip

 ![TrustedZone][TrustedZone]

No pacote de download, você encontra os seguintes arquivos:

|**Nome do arquivo**|**Descrição breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|O arquivo CAB que contém os binários que são implantados em cada máquina no cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Arquivo de exemplo de configuração do cluster que contém as configurações para um cluster de desenvolvimento de uma única VM/computador com três nós, não protegido, incluindo as informações para cada nó no cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Arquivo de exemplo de configuração do cluster que contém as configurações para um cluster de várias VMs/computadores não protegido, incluindo as informações para cada computador no cluster.|
|ClusterConfig.Windows.DevCluster.json|Arquivo de exemplo de configuração de cluster que contém as configurações para um cluster de desenvolvimento de uma única VM/computador, de três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de vários computadores/VMs protegido que use a segurança do Windows, incluindo as informações para cada computador no cluster protegido. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de desenvolvimento de uma única VM/Máquina, de três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509.|
|ClusterConfig.x509.MultiMachine.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de várias VM/computadores protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509.|
|EULA.txt|Os termos de licença para uso do pacote autônomo do Windows Server do Microsoft Azure Service Fabric. [Clique aqui](http://go.microsoft.com/fwlink/?LinkID=733084) se quiser baixar uma cópia do EULA agora.|
|Readme. txt|Link para as notas de versão e instruções básicas de instalação. É um subconjunto das instruções encontradas nesta página.|
|CreateServiceFabricCluster.ps1|Script do PowerShell que cria o cluster usando as configurações no arquivo ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script do PowerShell que move um cluster usando as configurações no arquivo ClusterConfig.json.|
|AddNode.ps1|Script do PowerShell para adicionar um nó a um cluster implantado existente.|
|RemoveNode.ps1|Script do PowerShell para remover um nó de um cluster implantado existente.|


## Planejar e preparar para a implantação do cluster
Execute as seguintes etapas antes de criar o cluster.

### Etapa 1: Planejar a infraestrutura de cluster
Você está prestes a criar um cluster do Service Fabric em seus computadores. Sendo assim, você pode decidir a quais tipos de falhas deseja que o cluster sobreviva. Por exemplo, você precisa separar as linhas de energia ou conexões de Internet fornecidas para os computadores? Além disso, considere a segurança física dos computadores. Qual é sua localização física e quem precisa ter acesso a eles? Depois de tomar essas decisões, você pode mapear logicamente os computadores para os vários domínios de falha (role para baixo para consultar mais informações). O planejamento da infraestrutura para os clusters de produção está mais envolvido que para os clusters de teste.

<a id="preparemachines"></a>
### Etapa 2: Preparar os computadores para atender os pré-requisitos
Pré-requisitos para cada computador que você deseja adicionar ao cluster:

- Um mínimo de 4 GB de memória é recomendado
- Conectividade da rede: verifique se os computadores estão em uma rede ou redes seguras.
- Windows Server 2012 R2 ou Windows Server 2012 (você precisa ter o [KB2858668](https://support.microsoft.com/kb/2858668) instalado).
- [.NET Framework 4.5.1 ou posterior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- O administrador do cluster que implanta e configura o cluster deve ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada um dos computadores.
- O [serviço RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve estar em execução em todos os computadores.

### Etapa 3: Determinar o tamanho inicial do cluster
Cada nó em um cluster do Service Fabric autônomo tem a execução do Service Fabric implantada e é membro do cluster. Em uma implantação típica de produção, há um nó por instância de SO (física ou virtual). O tamanho do cluster é determinado por suas necessidades de negócios. No entanto, você precisa ter um tamanho mínimo de cluster de três nós (máquinas/VMs). Para fins de desenvolvimento, você pode ter mais de um nó em um determinado computador. Em um ambiente de produção, o Service Fabric dá suporte apenas a um nó por máquina virtual ou física.

### Etapa 4: Determinar o número de domínios de falha e domínios de atualização
Um **FD (Domínio de Falha)** é uma unidade física de falha e está diretamente relacionada à infraestrutura física dos data centers. Um domínio de falha consiste em componentes de hardware (computadores, comutadores, rede, entre outros) que compartilham um ponto único de falha. Embora não haja um mapeamento individual entre domínios de falha e racks, de maneira geral, cada rack pode ser considerado um domínio de falha. Ao considerar os nós no cluster, recomendamos fortemente distribuir os nós entre pelo menos três domínios de falha.

Quando você especifica FDs no *ClusterConfig.json*, pode escolher o nome de cada FD. O Service Fabric dá suporte a FDs hierárquicos, de modo que você pode refletir a topologia da sua infraestrutura neles. Por exemplo, os seguintes FDs são válidos:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Um **UD (Domínio de Atualização)** é uma unidade lógica de nós. Durante uma atualização orquestrada do Service Fabric (atualização de aplicativo ou atualização de cluster), todos os nós em um UD são desativados para realizar a atualização, enquanto os nós em outros UDs permanecem disponíveis para atender às solicitações. As atualizações de firmware aplicadas a seus computadores não se aplicam a UDs, assim, é preciso fazê-las em um computador por vez.

A maneira mais simples de pensar nesses conceitos é considerar os FDs como a unidade da falha não planejada e as UDs como a unidade da manutenção planejada.

Quando você especifica UDs no *ClusterConfig.json*, pode escolher o nome de cada UD. Por exemplo, é válido o seguinte:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Para obter informações mais detalhadas sobre os domínios de atualização e domínio de falha, leia o artigo [Descrevendo um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Etapa 5: baixar o pacote autônomo do Service Fabric para Windows Server
[Baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote para um computador de implantação que não faça parte do cluster ou para um dos computadores que farão parte do cluster.

<a id="createcluster"></a>
## Criar o cluster

Após ter concluído as etapas descritas na seção de planejamento e preparação acima, você está pronto para criar o cluster.

### Etapa 1: Modificar a configuração do cluster
O cluster está descrito em um arquivo *ClusterConfig.json*. Para obter detalhes sobre as seções neste arquivo, leia o artigo [Definições de configuração para o cluster autônomo do Windows](service-fabric-cluster-manifest.md). Abra um dos arquivos *ClusterConfig.json* do pacote baixado e modifique as seguintes configurações:

|**Parâmetro de configuração**|**Descrição**|
|-----------------------|--------------------------|
|**NodeTypes**|Os tipos de nós permitem separar os nós do cluster em vários grupos. Um cluster precisa ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características comuns: <br> **Nome** – esse é o nome do tipo de nó. <br>**Portas do Ponto de Extremidade**: são vários pontos de extremidade (portas) nomeados que estão associados a esse tipo de nó. Você pode usar qualquer número de porta que desejar, desde que não entre em conflito com nada mais neste manifesto e já não esteja em uso por outro aplicativo em execução no computador/VM. <br> **Propriedades de Posicionamento**: descrevem as propriedades do tipo de nó utilizadas como restrições de posicionamento para os serviços do sistema ou seus serviços. Essas propriedades são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó incluem se o nó tem ou não um disco rígido ou uma placa de vídeo, o número de eixos no disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades**: as capacidades do nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB de memória disponível por padrão. Essas capacidades são usadas no tempo de execução para garantir que os serviços que exigem quantidades específicas de recursos sejam colocados em nós que tenham esses recursos disponíveis nas quantidades necessárias.<br>**IsPrimary** – se você tiver mais de um NodeType definido, verifique se somente um está definido como principal com o valor *true*, que é onde os serviços do sistema são executados. Todos os outros tipos de nó devem ser definidos para o valor *false*|
|**Nós**|Esses são os detalhes de cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). Os computadores nos quais você deseja que o cluster seja criado precisam ser listados aqui com os endereços IP. <br> Se você usar o mesmo endereço IP para todos os nós, um cluster one-box será criado, que você pode usar para fins de teste. Não use clusters de uma caixa para implantar cargas de trabalho de produção.|

### Etapa 2: Executar o script de criação de cluster
Depois de ter modificado a configuração do cluster no documento JSON e adicionado todas as informações do nó a ela, execute o script do PowerShell de criação do cluster *CreateServiceFabricCluster.ps1* na pasta do pacote e transmita o caminho para o arquivo de configuração do JSON e aceite o EULA.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual esse script é executado pode ou não fazer parte do cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA true
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA true
```

>[AZURE.NOTE] Os logs de implantação estão disponíveis localmente na VM/máquina em que você executou o PowerShell CreateServiceFabricCluster. Eles ficam em uma subpasta chamada "DeploymentTraces" sob a pasta na qual você executou o comando do PowerShell. Além disso, para ver se o Service Fabric foi implantado corretamente em um computador, você poderá encontrar os arquivos instalados no diretório C:\\ProgramData, e os processos FabricHost.exe e Fabric.exe poderão ser vistos em execução no Gerenciador de Tarefas.

### Etapa 3: conectar-se ao cluster

Consulte [este documento](service-fabric-connect-to-secure-cluster.md) para obter instruções para conectar-se a um cluster seguro.

Para conectar-se a um cluster não seguro, execute o seguinte comando do PowerShell

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### Etapa 4: abrir o Service Fabric Explorer

Agora, você pode conectar o cluster com o Service Fabric Explorer diretamente de um dos computadores com http://localhost:19080/Explorer/index.html ou remotamente com http://<*IPAddressofaMachine*>:19080/Explorer/index.html



## Adicionar e remover nós do cluster

Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Leia [Adicionar ou Remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.


## Remover o cluster

Para remover um cluster, execute o script do Powershell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON e, opcionalmente, especifique uma localização para o log da exclusão.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual esse script é executado pode ou não fazer parte do cluster

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```

## Recursos de preview incluídos neste pacote

O pacote inteiro está atualmente em preview.

## Próximas etapas
- [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
- [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)


<!--Image references-->
[TrustedZone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

<!---HONumber=AcomDC_0921_2016-->