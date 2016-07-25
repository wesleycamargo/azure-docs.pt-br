<properties
   pageTitle="Criar um cluster do Azure Service Fabric local ou em qualquer nuvem | Microsoft Azure"
   description="Saiba como criar um cluster do Azure Service Fabric em qualquer computador (físico ou virtual) que executar o Windows Server, seja localmente ou em qualquer nuvem."
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
   ms.date="07/05/2016"
   ms.author="chackdan"/>


# Criar um cluster para execução no Windows Server

O Azure Service Fabric permite a criação de clusters do Service Fabric em quaisquer máquinas virtuais ou computadores que estiverem executando o Windows Server. Isso significa que você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores com Windows Server interconectados, seja ele local ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para a criação de clusters do Service Fabric chamado pacote do Windows Server.

Este artigo explica as etapas necessárias para criar um cluster usando o pacote autônomo do Service Fabric local, mas ele pode ser adaptada facilmente a qualquer outro ambiente, como provedores na nuvem.

>[AZURE.NOTE] Este pacote do Windows Server autônomo está atualmente em visualização e não há suporte para cargas de trabalho de produção. [Clique aqui](http://go.microsoft.com/fwlink/?LinkID=733084) se quiser baixar uma cópia do EULA agora.

<a id="downloadpackage"></a>
## Baixar o pacote autônomo do Service Fabric


[Baixe o pacote autônomo do Service Fabric para Windows Server 2012 R2 e versões posteriores](http://go.microsoft.com/fwlink/?LinkId=730690), que se chama *Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip*.

No pacote de download, você encontrará os seguintes arquivos:

|**Nome do arquivo**|**Descrição breve**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|O arquivo CAB que contém os binários que são implantados em cada máquina no cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Arquivo de exemplo de configuração de cluster que contém as configurações para um cluster de desenvolvimento de uma única VM/Máquina, de três nós, não protegido, incluindo as informações para cada nó que está no cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Arquivo de exemplo de configuração de cluster que contém as configurações para o cluster de vários computadores/VMs não protegido, incluindo as informações para cada computador que está no cluster.|
|ClusterConfig.Windows.DevCluster.json|Arquivo de exemplo de configuração de cluster que contém as configurações para um cluster de desenvolvimento de uma única VM/computador, de três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de vários computadores/VMs protegido que use a segurança do Windows, incluindo as informações para cada computador no cluster protegido. O cluster é protegido usando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de desenvolvimento de uma única VM/Máquina, de três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509.|
|ClusterConfig.x509.MultiMachine.json|Arquivo de exemplo de configuração de cluster que contém todas as configurações para um cluster de várias VM/computadores protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509.|
|EULA.txt|Os termos de licença para uso do pacote autônomo do Windows Server do Microsoft Azure Service Fabric. [Clique aqui](http://go.microsoft.com/fwlink/?LinkID=733084) se quiser baixar uma cópia do EULA agora.|
|Readme. txt|Link para as notas de versão e instruções básicas de instalação. Trata-se de um subconjunto das instruções que você encontrará nesta página.|
|CreateServiceFabricCluster.ps1|Script do PowerShell que cria o cluster usando as configurações no arquivo ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script do PowerShell que move um cluster usando as configurações no arquivo ClusterConfig.json.|
|AddNode.ps1|Script do PowerShell para adicionar um nó a um cluster implantado existente.|
|RemoveNode.ps1|Script do PowerShell para remover um nó de um cluster implantado existente.|


## Planejar e preparar para a implantação do cluster
As etapas a seguir precisam ser executadas antes da criação do cluster.

### Etapa 1: Planejar a infraestrutura de cluster
Você está prestes a criar um cluster do Service Fabric nas máquinas que possui. Sendo assim, você decide a quais tipos de falhas deseja que o cluster sobreviva. Por exemplo, você precisa de linhas de energia ou conexões com a Internet separadas que alimentam esses computadores? Além disso, você também deve levar em consideração a segurança física dos computadores. Qual é sua localização física e quem precisa ter acesso a eles? Depois de tomar essas decisões, você mapeia logicamente os computadores para vários domínios de falha (consulte abaixo para obter mais informações). O planejamento da infra-estrutura para os clusters de produção será envolverá muito mais aspectos do que para os clusters de teste.

### Etapa 2: Preparar os computadores para atender os pré-requisitos
Pré-requisitos para cada computador que você deseja adicionar ao cluster:

- Um mínimo de 2 GB de memória é recomendado
- Conectividade de rede. Verifique se os computadores estão em uma rede ou redes seguras
- Windows Server 2012 R2 ou Windows Server 2012 (para isso, você precisa ter o KB2858668 instalado).
- .NET Framework 4.5.1 ou superior, instalação completa
- Windows PowerShell 3.0
- O administrador do cluster que estiver implantando e configurando o cluster precisa ter privilégios de administrador em cada um dos computadores.
- O serviço RemoteRegistry deve ser executado em todos os computadores.

### Etapa 3: Determinar o tamanho inicial do cluster
Cada nó tem o tempo de execução do Service Fabric implantado e é um membro do cluster. Em uma implantação típica de produção, há um nó por instância de SO (física ou virtual). O tamanho do cluster é determinado por suas necessidades de negócios. No entanto, você precisa ter um tamanho mínimo de cluster de três nós (máquinas/VMs). Observe que, para fins de desenvolvimento, você pode ter mais de um nó em um determinado computador. Em um ambiente de produção, o Service Fabric dá suporte apenas a um nó por máquina virtual ou física.

### Etapa 4: Determinar o número de domínios de falha e domínios de atualização
Um **FD (domínio de falha)** é uma unidade física de falha e está diretamente relacionada à infraestrutura física dos datacenters. Um domínio de falha consiste em componentes de hardware (computadores, comutadores, rede, entre outros) que compartilham um ponto único de falha. Embora não haja um mapeamento individual entre domínios de falha e racks, de maneira geral, cada rack pode ser considerado um domínio de falha. Ao considerar os nós no cluster, é altamente recomendável que os nós sejam distribuídos entre pelo menos três domínios de falha.

Ao especificar FDs em *ClusterConfig*.json*, você poderá escolher o nome de cada FD. O Service Fabric dá suporte a FDs hierárquicos, de modo que você pode refletir a topologia da sua infraestrutura neles. Por exemplo, os seguintes FDs são válidos:

- "faultDomain": "fd:/Room1/Rack1/Machine1"
- "faultDomain": "fd:/FD1"
- "faultDomain": "fd:/Room1/Rack1/PDU1/M1"


Um **UD (domínio de atualização)** é uma unidade lógica de nós. Durante uma atualização orquestrada do Service Fabric (atualização de aplicativo ou atualização de cluster), todos os nós em um UD são desativados para realizar a atualização, enquanto os nós em outros UDs permanecem disponíveis para atender às solicitações. As atualizações de firmware que você fizer em seus computadores não se aplicarão a UDs, portanto você deve fazê-las um computador por vez.

A maneira mais simples de pensar nesses conceitos é considerar os FDs como a unidade da falha não planejada e as UDs como a unidade da manutenção planejada.

Ao especificar UDs em *ClusterConfig*.json*, você pode escolher o nome de cada UD. Por exemplo, é válido o seguinte:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blue"

Para saber mais sobre domínios de atualização e domínio de falha, leia o artigo [Descrevendo um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

### Etapa 5: baixar o pacote autônomo do Service Fabric para Windows Server
[Baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote em um computador de implantação que não faz parte do cluster ou em um dos computadores que farão parte do cluster.

<a id="createcluster"></a>
## Criar o cluster

Após ter concluído as etapas descritas na seção de planejamento e preparação acima, você está pronto para criar o cluster.

### Etapa 1: Modificar a configuração do cluster
O cluster está descrito em um arquivo *ClusterConfig.json*. Para obter detalhes sobre as seções neste arquivo, leia o artigo [Definições de configuração para o cluster autônomo do Windows](service-fabric-cluster-manifest.md). Abra um dos arquivos *ClusterConfig.json* do pacote baixado e modifique as seguintes configurações:

|**Parâmetro de configuração**|**Descrição**|
|-----------------------|--------------------------|
|**NodeTypes**|Os tipos de nós permitem separar os nós do cluster em vários grupos. Um cluster precisa ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características em comum. <br> **Name** -esse é o nome do tipo de nó. <br>**Portas EndPoints** - são vários pontos de extremidade (portas) nomeados que estão associados a esse tipo de nó. Você pode usar qualquer número da porta que desejar, desde que ele não entre em conflito com qualquer outra coisa no manifesto e não esteja sendo usado por outro aplicativo em execução no computador/VM<br> **PlacementProperties** - descrevem propriedades para esse tipo de nó, que você usará como restrições de posicionamento para serviços do sistema ou seus serviços. Essas propriedades de nó são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó incluem se o nó tem ou não um disco rígido ou placa de vídeo, o número de eixos no seu disco rígido, núcleos e outras propriedades físicas. <br> **Capacities** - as capacidades do nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB de memória disponível por padrão. Essas capacidades são usadas em tempo de execução para fazer com que os serviços que necessitam de uma quantidade específica de recursos sejam colocados em nós com os recursos restantes disponíveis.<br>**IsPrimary** - se você tiver mais de um NodeType definido, defina somente um como primário com o valor *true*, que é onde os serviços de sistema são executados. Todos os outros tipos de nó devem ser definidos com o valor *false*|
|**Nós**|Os detalhes de cada um de nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). Os computadores nos quais você deseja que o cluster seja criado precisam ser listados aqui com os endereços IP. <br> Se você usar os mesmos endereços IP para todos os nós, um cluster one-box será criado e será possível usá-lo para fins de teste. Clusters one-box não devem ser usados para a implantação de cargas de trabalho de produção.|

### Etapa 2: Executar o script de criação de cluster
Depois de ter modificado a configuração do cluster no documento JSON e adicionado todas as informações do nó a ele, execute o script do PowerShell de criação de cluster *CreateServiceFabricCluster.ps1* na pasta do pacote e transmita para ele o caminho para o arquivo de configuração e o local do arquivo CAB do pacote.

Esse script pode ser executado em qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual esse script é executado pode ou não fazer parte do cluster.

```
#Create an unsecure local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```
```
#Create an unsecure multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

>[AZURE.NOTE] Os logs de implantação estão disponíveis localmente na VM/máquina em que você executou o PowerShell CreateServiceFabricCluster. Você os encontrará em uma subpasta chamada "DeploymentTraces" na pasta da qual você executou o comando do PowerShell. Além disso, para ver se o Service Fabric foi implantado corretamente em um computador, você poderá encontrar os arquivos instalados no diretório C:\\ProgramData, e os processos FabricHost.exe e Fabric.exe poderão ser vistos em execução no Gerenciador de Tarefas.

### Etapa 3: conectar-se ao cluster
Agora, você pode se conectar ao cluster com o Service Fabric Explorer diretamente de uma das máquinas com http://localhost:19080/Explorer/index.html ou remotamente com http://<*IPAddressofaMachine*>:19080/Explorer/index.html

## Adicionar nós ao cluster

1. Prepare a VM/máquina a ser adicionada ao cluster (confira a etapa 2 na seção acima Planejar e preparar para implantação de cluster).
2. Planeje a qual domínio de falha e domínio de atualização você vai adicionar essa VM/máquina.
3. Copie ou [baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote na VM/máquina que está planejando adicionar ao cluster.
4. Abra um prompt de administração do PowerShell e navegue até o local do pacote descompactado.
5. Execute o PowerShell *AddNode.ps1* com os parâmetros que descrevem o novo nó a adicionar. O exemplo a seguir adiciona um novo nó chamado VM5, com tipo NodeType0, endereço IP 182.17.34.52 em UD1 e FD1. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão para um nó já existente em um cluster existente. Você pode escolher *qualquer* endereço IP do nó para isso no cluster.

```
.\AddNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1
```

## Remover nós do cluster

1. RDP (área de trabalho remota) na VM/computador que você deseja remover do cluster.
2. Copie ou [baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote na VM/máquina que está planejando adicionar ao cluster.
3. Abra um prompt de administração do PowerShell e navegue até o local do pacote descompactado.
4. Execute o PowerShell *RemoveNode.ps1*. O exemplo a seguir remove o nó atual do cluster. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão para um nó já existente em um cluster existente. Você pode escolher *qualquer* endereço IP do nó para isso no cluster.

```
.\RemoveNode.ps1 -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```

## Remover o cluster
Para remover um cluster que executa o script *RemoveServiceFabricCluster.ps1* do Powershell na pasta do pacote e transmitir o caminho para o arquivo de configuração JSON e o local do arquivo CAB do pacote.

Esse script pode ser executado em qualquer computador que tenha acesso de administrador a todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual esse script é executado pode ou não fazer parte do cluster

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

## Como criar um cluster de três nós com VMs IaaS do Azure
As etapas a seguir descrevem como criar um cluster em VMs IaaS do Azure usando o instalador autônomo do Windows Server. Observe que o tempo de execução do Service Fabric neste cluster IaaS é totalmente gerenciado por você, ao contrário dos clusters criados pelo portal do Azure que são atualizados pelo provedor de recursos do Service Fabric.

1. Faça logon no portal do Azure e crie uma nova VM do Windows Server 2012 R2 Datacenter em um grupo de recursos.
2. Adicione mais duas VMs Windows Server 2012 R2 Datacenter ao mesmo grupo de recursos. Verifique se cada uma das VMs tem o mesmo nome de usuário administrador e senha quando criada. Uma vez criadas, você deve ver as três VMs na mesma rede virtual.
3. Conecte-se a cada uma das VMs e desative o firewall do Windows usando o Gerenciador de Servidores, painel Servidor Local. Isso faz com que o tráfego de rede possa se comunicar entre computadores. Em cada máquina que estiver, obtenha o endereço IP abrindo um prompt de comando e digitando *ipconfig*. Como alternativa, você pode ver o endereço IP de cada máquina selecionando o recurso de rede virtual para o grupo de recursos no portal do Azure
4. Conecte-se a uma das máquinas e teste se você pode executar o ping para as duas outras máquinas com êxito.
5. Conecte-se a uma das VMs, baixe o pacote do Windows Server autônomo em uma nova pasta no computador e descompacte-o.
6. Abra o arquivo *ClusterConfig.Unsecure.MultiMachine.json* no Bloco de Notas e edite cada nó com os três endereços IP das máquinas, altere o nome do cluster na parte superior e salve o arquivo. Um exemplo parcial do manifesto do cluster é mostrado abaixo com os endereços IP de cada máquina.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Abra uma janela do ISE do PowerShell, navegue até a pasta onde você baixou e descompactou o pacote do instalador autônomo e salve o arquivo de manifesto acima. Execute o comando do PowerShell a seguir.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Você deve ver o Powershell executar, conectar-se a cada máquina e criar um cluster. Após um minuto, você deve poder testar se o cluster está operacional conectando-se ao Service Fabric Explorer em um dos endereços IP da máquina, por exemplo, http://10.7.0.5:19080/Explorer/index.html. Como esse é um cluster autônomo em VMs IaaS, para torná-lo mais seguro, você precisará implantar certificados para as máquinas virtuais ou configurar uma das máquinas como um controlador AD (Active Directory) do Windows Server para autenticação do Windows, assim como você faria no local. Confira a seção Próximas etapas abaixo para configurar clusters protegidos.

## Próximas etapas
- [Criar clusters autônomos do Service Fabric no Windows Server ou Linux](service-fabric-deploy-anywhere.md)
- [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)

- [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0713_2016-->