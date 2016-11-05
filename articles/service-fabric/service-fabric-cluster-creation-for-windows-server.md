---
title: Criar e gerenciar um cluster do Azure Service Fabric autônomo | Microsoft Docs
description: Crie e gerencie um cluster do Azure Service Fabric em qualquer computador (físico ou virtual) que executar o Windows Server, seja localmente ou em qualquer nuvem.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: dkshir;chackdan

---
# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Criar e gerenciar um cluster em execução no Windows Server
Você pode usar o Azure Service Fabric para criar clusters do Service Fabric em qualquer máquina virtual ou computador que estiver executando o Windows Server. Isso significa que você pode implantar e executar os aplicativos do Service Fabric em qualquer ambiente que tenha um conjunto de computadores com o Windows Server interconectados, seja localmente ou em qualquer provedor de nuvem. O Service Fabric fornece um pacote de instalação para criar os clusters do Service Fabric denominado pacote do Windows Server autônomo.

Este artigo explica as etapas necessárias para criar um cluster usando o pacote autônomo do Service Fabric local, embora ele possa ser facilmente adaptado a qualquer outro ambiente, como provedores na nuvem.

> [!NOTE]
> Este pacote autônomo do Windows Server pode conter recursos que estejam em visualização no momento e não tenham suporte para uso comercial. Para ver a lista de recursos que estão em visualização, confira "Recursos de visualização incluídos neste pacote". Você também pode [baixar uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>Obter suporte para o pacote autônomo do Service Fabric
* Pergunte à comunidade sobre o pacote autônomo do Service Fabric para Windows Server no [Fórum do Service Fabric do Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abra um tíquete de [Suporte Profissional para o Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o [Suporte Profissional da Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>Baixar o pacote autônomo do Service Fabric
[Baixe o pacote autônomo do Service Fabric para Windows Server 2012 R2](http://go.microsoft.com/fwlink/?LinkId=730690), que se chama Microsoft.Azure.ServiceFabric.WindowsServer.&lt;version&gt;.zip.

No pacote de download, você encontrará os seguintes arquivos:

| **Nome do arquivo** | **Descrição breve** |
| --- | --- |
| MicrosoftAzureServiceFabric.cab |O arquivo .cab, que contém os binários que são implantados em cada computador no cluster. |
| ClusterConfig.Unsecure.DevCluster.json |Um arquivo de exemplo de configuração do cluster, que contém as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual) com três nós, não protegido, incluindo as informações para cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém as configurações para um cluster de vários computadores (ou máquinas virtuais), não protegido, incluindo as informações para cada computador no cluster. |
| ClusterConfig.Windows.DevCluster.json |Um arquivo de exemplo de configuração do cluster que contém todas as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual) com três nós, protegido, incluindo as informações para cada nó que está no cluster. O cluster é protegido usando as [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de vários computadores (ou máquinas virtuais), protegido, que use a segurança do Windows, incluindo as informações para cada computador no cluster protegido. O cluster é protegido usando as [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de desenvolvimento de um único computador (ou máquina virtual), com três nós, protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509. |
| ClusterConfig.x509.MultiMachine.json |Um arquivo de exemplo de configuração do cluster, que contém todas as configurações para um cluster de vários computadores (ou máquinas virtuais), protegido, incluindo as informações para cada nó no cluster. O cluster é protegido usando certificados x509. |
| EULA_ENU.txt |Os termos de licença para uso do pacote autônomo do Windows Server do Microsoft Azure Service Fabric. Você pode [baixar uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora. |
| Readme. txt |Um link para as notas de versão e instruções básicas de instalação. É um subconjunto das instruções neste documento. |
| CreateServiceFabricCluster.ps1 |Um script do PowerShell que cria o cluster usando as configurações em ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Um script do PowerShell que remove um cluster usando as configurações em ClusterConfig.json. |
| ThirdPartyNotice.rtf |Aviso de software de terceiros que está no pacote. |
| AddNode.ps1 |Um script do PowerShell para adicionar um nó a um cluster implantado existente. |
| RemoveNode.ps1 |Um script do PowerShell para remover um nó de um cluster implantado existente. |
| CleanFabric.ps1 |Um script do PowerShell para limpeza de uma instalação autônoma do Service Fabric do computador atual. As instalações anteriores do MSI devem ser removidas usando seus próprios desinstaladores associados. |
| TestConfiguration.ps1 |Um script do PowerShell para analisar a infraestrutura conforme especificado no Cluster.json. |

## <a name="plan-and-prepare-your-cluster-deployment"></a>Planejar e preparar para a implantação do cluster
Execute as seguintes etapas antes de criar o cluster.

### <a name="step-1:-plan-your-cluster-infrastructure"></a>Etapa 1: Planejar a infraestrutura de cluster
Você está prestes a criar um cluster do Service Fabric em seus computadores. Sendo assim, você pode decidir a quais tipos de falhas deseja que o cluster sobreviva. Por exemplo, você precisa separar as linhas de energia ou conexões de Internet fornecidas para os computadores? Além disso, considere a segurança física dos computadores. Onde os computadores estão localizados e quem precisa acessá-los? Depois de tomar essas decisões, você poderá mapear logicamente os computadores para vários domínios de falha (confira a Etapa 4). O planejamento da infraestrutura para os clusters de produção está mais envolvido do que para os clusters de teste.

<a id="preparemachines"></a>

### <a name="step-2:-prepare-the-machines-to-meet-the-prerequisites"></a>Etapa 2: Preparar os computadores para atender os pré-requisitos
Pré-requisitos para cada computador que você deseja adicionar ao cluster:

* É recomendável, no mínimo, 16 GB de RAM.
* É recomendável um espaço em disco disponível mínimo de 40 GB.
* É recomendável uma CPU com quatro núcleos ou mais.
* Conectividade com uma rede segura para todos os computadores.
* Windows Server 2012 R2 ou Windows Server 2012 (você precisa ter o [KB2858668](https://support.microsoft.com/kb/2858668) instalado).
* [.NET Framework 4.5.1 ou posterior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa.
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* O [serviço RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve estar em execução em todos os computadores.

O administrador do cluster que implanta e configura o cluster deve ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada um dos computadores. Você não pode instalar o Service Fabric em um controlador de domínio.

### <a name="step-3:-determine-the-initial-cluster-size"></a>Etapa 3: Determinar o tamanho inicial do cluster
Cada nó em um cluster do Service Fabric autônomo tem o tempo de execução do Service Fabric implantado e é membro do cluster. Em uma implantação típica de produção, há um nó por instância de SO (física ou virtual). O tamanho do cluster é determinado por suas necessidades comerciais. No entanto, seu cluster deve ter um tamanho mínimo de três nós (computadores ou máquinas virtuais).
Para fins de desenvolvimento, você pode ter mais de um nó em um determinado computador. Em um ambiente de produção, o Service Fabric dá suporte apenas a um nó por máquina virtual ou física.

### <a name="step-4:-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Etapa 4: Determinar o número de domínios de falha e domínios de atualização
Um *FD* (Domínio de Falha) é uma unidade física de falha e está diretamente relacionada à infraestrutura física dos data centers. Um domínio de falha consiste em componentes de hardware (computadores, comutadores, redes, entre outros) que compartilham um ponto único de falha. Embora não haja um mapeamento individual entre domínios de falha e racks, de maneira geral, cada rack pode ser considerado um domínio de falha. Ao considerar os nós no cluster, recomendamos fortemente distribuir os nós entre pelo menos três domínios de falha.

Ao especificar FDs no ClusterConfig.json, você pode escolher o nome de cada FD. O Service Fabric dá suporte a FDs hierárquicos, de modo que você pode refletir a topologia da sua infraestrutura neles.  Por exemplo, os seguintes FDs são válidos:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

Um *UD* (Domínio de Atualização) é uma unidade lógica de nós. Durante as atualizações orquestradas do Service Fabric (atualização de aplicativo ou atualização de cluster), todos os nós em um UD são desativados para realizar a atualização, enquanto os nós em outros UDs permanecem disponíveis para atender às solicitações. As atualizações de firmware executadas em seus computadores não se aplicam a UDs, assim, é preciso fazê-las em um computador por vez.

A maneira mais simples de pensar nesses conceitos é considerar os FDs como a unidade de falha não planejada e as UDs como a unidade de manutenção planejada.

Ao especificar UDs no ClusterConfig.json, você pode escolher o nome de cada UD. Por exemplo, os seguintes nomes são válidos:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Para obter informações mais detalhadas sobre os domínios de atualização e de falha, confira [Describing a Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descrevendo um cluster do Service Fabric).

### <a name="step-5:-download-the-service-fabric-standalone-package-for-windows-server"></a>Etapa 5: baixar o pacote autônomo do Service Fabric para Windows Server
[Baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote para um computador de implantação que não faça parte do cluster ou para um dos computadores que farão parte do cluster. Você pode renomear a pasta descompactada `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>

## <a name="create-your-cluster"></a>Criar o cluster
Após ter concluído as etapas de planejamento e preparação, você estará pronto para criar o cluster.

### <a name="step-1:-modify-cluster-configuration"></a>Etapa 1: Modificar a configuração do cluster
O cluster está descrito em ClusterConfig.json. Para obter detalhes sobre as seções neste arquivo, confira [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md).
Abra um dos arquivos ClusterConfig.json do pacote baixado e modifique as seguintes configurações:

| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| **NodeTypes** |Os tipos de nós permitem separar os nós do cluster em vários grupos. Um cluster precisa ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características comuns:  <br> **Nome**: esse é o nome do tipo de nó. <br>**Portas do ponto de extremidade**: são vários pontos de extremidade (portas) nomeados que estão associados a esse tipo de nó. Você pode usar qualquer número da porta que não entre em conflito com mais nada neste manifesto e já não esteja em uso por outro aplicativo em execução no computador ou na máquina virtual. <br> **Propriedades de posicionamento**: descrevem as propriedades do tipo de nó que você usou como restrições de posicionamento para os serviços do sistema ou seus serviços. Essas propriedades de nó são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó incluem a existência de um disco rígido ou uma placa de vídeo no nó, o número de eixos no disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades**: as capacidades do nó definem o nome e a quantidade de um recurso que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB de memória disponível por padrão. Essas capacidades são usadas no tempo de execução para garantir que os serviços que exigem quantidades específicas de recursos sejam colocados em nós que tenham esses recursos disponíveis nas quantidades necessárias.<br>**IsPrimary**: se você tiver mais de um NodeType definido, verifique se somente um está definido como principal (com o valor *verdadeiro*), que é onde os serviços do sistema são executados. Todos os outros tipos de nó devem ser definidos com o valor *falso*. |
| **Nós** |Estes são os detalhes de cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). Os computadores nos quais você deseja que o cluster seja criado precisam ser listados aqui com os endereços IP. <br>  Se você usar o mesmo endereço IP para todos os nós, um cluster one-box será criado, que você pode usar para fins de teste. Não use clusters de uma caixa para implantar cargas de trabalho de produção. |

### <a name="step-2:-run-the-testconfiguration-script"></a>Etapa 2: executar o script TestConfiguration
O script TestConfiguration testa sua infraestrutura, conforme definido em cluster.json, para se certificar de que as permissões necessárias serão atribuídas, os computadores serão conectados entre si e outros atributos serão definidos para que a implantação possa ser bem-sucedida. Ele é basicamente um mini Analisador de Práticas Recomendadas. Continuaremos a adicionar validações a essa ferramenta ao longo do tempo para torná-la mais robusta.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3:-run-the-create-cluster-script"></a>Etapa 3: executar o script de criação de cluster
Depois de ter modificado a configuração do cluster no documento JSON e adicionado todas as informações do nó a ela, execute o script do PowerShell de criação do cluster *CreateServiceFabricCluster.ps1* na pasta do pacote e transmita o caminho para o arquivo de configuração do JSON. Quando essa etapa for concluída, aceite o EULA.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

> [!NOTE]
> Os logs de implantação estão disponíveis localmente na VM/computador em que você executou o PowerShell CreateServiceFabricCluster. Eles ficam em uma subpasta chamada DeploymentTraces, sob a pasta na qual você executou o comando do PowerShell. Para ver se o Service Fabric foi implantado corretamente em um computador, você poderá encontrar os arquivos instalados no diretório C:\ProgramData, e os processos FabricHost.exe e Fabric.exe poderão ser vistos em execução no Gerenciador de Tarefas.
> 
> 

### <a name="step-4:-connect-to-the-cluster"></a>Etapa 4: conectar-se ao cluster
Para se conectar a um cluster seguro, confira [Service fabric connect to secure cluster](service-fabric-connect-to-secure-cluster.md) (Conexão do Service Fabric com um cluster seguro).

Para se conectar a um cluster não seguro, execute o seguinte comando do PowerShell:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5:-bring-up-service-fabric-explorer"></a>Etapa 5: abrir o Service Fabric Explorer
Agora, você pode conectar o cluster com o Service Fabric Explorer diretamente de um dos computadores com http://localhost:19080/Explorer/index.html ou remotamente com http://<*IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Você pode adicionar ou remover os nós do cluster do Service Fabric autônomo quando seu negócio precisa mudar. Confira [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para ver as etapas detalhadas.

## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script do PowerShell *RemoveServiceFabricCluster.ps1* da pasta do pacote e transmita o caminho para o arquivo de configuração JSON. Como alternativa, você pode especificar um local para o log de exclusão.

Esse script pode ser executado em qualquer computador que tenha o acesso de administrador para todos os computadores listados como nós no arquivo de configuração do cluster. O computador no qual este script é executado não precisa fazer parte do cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dados de telemetria coletados e como recusá-los
Por padrão, o produto coleta a telemetria sobre o uso do Service Fabric para aprimorar o produto. O Analisador de Práticas Recomendadas executado como parte da instalação verifica a conectividade para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se ele não estiver acessível, a instalação falhará, a menos que você recuse a telemetria.

1. O pipeline de telemetria tenta carregar os dados a seguir para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez por dia. É um upload de melhor esforço e não causa nenhum impacto sobre a funcionalidade do cluster. A telemetria é enviada somente do nó que executa o gerenciador de failover primário. Nenhum outro nó envia telemetria.
2. A Telemetria consiste no seguinte:

* Número de serviços
* Número de ServiceTypes
* Número de Aplicativos
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de Réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de Nós
* IsContextComplete: True/False
* ClusterId: esse é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou computador por meio do qual a telemetria é carregada

Para desabilitar a telemetria, adicione o seguinte ao elemento *propriedades* em sua configuração de cluster: *enableTelemetry: false*.

<a id="previewfeatures"></a>

## <a name="preview-features-included-in-this-package"></a>Recursos de preview incluídos neste pacote
Nenhuma.

> [!NOTE]
> Com a nova [versão GA do cluster autônomo para Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), você pode atualizar seu cluster para versões futuras, de modo manual ou automático. Como este recurso não está disponível nas versões de visualização, você precisará criar um cluster usando a versão GA e migrar seus dados e aplicativos do cluster de visualização. Fique atento para obter mais detalhes sobre este recurso.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós de um cluster do Service Fabric autônomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Zona confiável]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Oct16_HO2-->


