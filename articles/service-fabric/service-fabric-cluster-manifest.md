<properties
   pageTitle="Configurar o cluster autônomo | Microsoft Azure"
   description="Este artigo descreve como configurar seu cluster autônomo ou particular do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="dkshir"/>



# <a name="configuration-settings-for-standalone-windows-cluster"></a>Definições de configuração para o cluster autônomo no Windows

Este artigo descreve como configurar um cluster autônomo do Service Fabric usando o arquivo _**ClusterConfig.JSON**_. Esse arquivo é baixado em seu computador de trabalho quando você [baixa o pacote do Service Fabric autônomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage). O arquivo ClusterConfig.JSON permite que você especifique informações, como os nós do Service Fabric e seus endereços IP, tipos diferentes de nós no cluster, configurações de segurança, bem como a topologia da rede em termos de domínios de falha/atualização, para o cluster do Service Fabric. 

Vamos examinar abaixo as várias seções desse arquivo.

## <a name="general-cluster-configurations"></a>Configurações gerais do cluster
Isso abrange as configurações específicas mais amplas do cluster, conforme mostra o trecho de JSON abaixo.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Você pode atribuir qualquer nome amigável ao cluster do Service Fabric, atribuindo a ele a variável **name** . Você pode alterar o **clusterManifestVersion** de acordo com a sua configuração; será necessário atualizá-lo antes de atualizar sua configuração do Service Fabric. Você pode deixar a **apiVersion** com o valor padrão.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nós no cluster
Você pode configurar os nós no cluster de seu Service Fabric usando a seção **nós** , como mostra o trecho de código a seguir.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster do Service Fabric exige no mínimo 3 nós. Você pode adicionar mais nós a esta seção de acordo com a sua configuração. A tabela a seguir explica as definições de configuração para cada nó.

|**Configuração de nó**|**Descrição**|
|-----------------------|--------------------------|
|nodeName|Você pode atribuir qualquer nome amigável ao nó.|
|iPAddress|Descubra o endereço IP do seu nó abrindo uma janela de comando e digitando `ipconfig`. Anote o endereço IPV4 e atribua a ele a variável **iPAddress** .|
|nodeTypeRef|Cada nó pode ser receber um tipo de nó diferente. Os [tipos de nó](#nodetypes) são definidos na seção abaixo.|
|faultDomain|Os domínios de falha permitem que os administradores de cluster definam os nós físicos que podem falhar ao mesmo tempo devido às dependências físicas compartilhadas.|
|upgradeDomain|Os domínios de atualização descrevem conjuntos de nós que são desligados para atualizações do Service Fabric quase ao mesmo tempo. Você pode escolher quais nós atribuir a quais Domínios de atualização, pois eles não são limitados por quaisquer requisitos físicos.| 


## <a name="cluster-**properties**"></a> **Propriedades**

A seção **propriedades** no ClusterConfig.JSON é usada para configurar o cluster da seguinte maneira.

### <a name="**diagnosticsstore**"></a>**diagnosticsStore**
Você pode configurar parâmetros para habilitar o diagnóstico e solucionar problemas de falhas de nó e do cluster usando a seção **diagnosticsStore** , conforme mostra o trecho a seguir. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Os **metadados** são uma descrição do diagnóstico do cluster e podem ser definidos de acordo com sua configuração. Essas variáveis ajudam na coleta de logs de rastreamento ETW, despejos de memória e contadores de desempenho. Leia [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Rastreamento ETW](https://msdn.microsoft.com/library/ms751538.aspx) para saber mais sobre os logs de rastreamento de ETW. Todos os logs que incluem [Despejos de memória](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) podem ser direcionados para a pasta **connectionString** em seu computador. Você também pode usar **AzureStorage** para o armazenamento de diagnóstico. Veja abaixo um exemplo de trecho de código.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="**security**"></a>**segurança** 
A seção **segurança** é necessária para um cluster autônomo seguro do Service Fabric. O trecho a seguir mostra uma parte desta seção.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Os **metadados** são uma descrição de seu cluster seguro e podem ser definidos de acordo com sua configuração. O **ClusterCredentialType** e o **ServerCredentialType** determinam o tipo de segurança que o cluster e os nós implementarão. Eles podem ser definidos como *X509* para segurança baseada em certificados ou como *Windows* para segurança baseada no Azure Active Directory. O restante da seção **segurança** será baseado no tipo de segurança. Leia [Segurança baseada em certificados em um cluster autônomo](service-fabric-windows-cluster-x509-security.md) ou [Segurança do Windows em um cluster autônomo](service-fabric-windows-cluster-windows-security.md) para saber como preencher o restante da seção de **segurança**.

### <a name="**reliabilitylevel**"></a>**reliabilityLevel**
O **reliabilityLevel** define o número de cópias de serviços do sistema que podem ser executadas nos nós primários do cluster. Isso aumenta a confiabilidade desses serviços e, portanto, do cluster. Você pode definir essa variável como *Bronze*, *Prata*, *Ouro* ou *Platina* para três, cinco, sete ou nove cópias desses serviços, respectivamente. Veja um exemplo abaixo.

    "reliabilityLevel": "Bronze",
    
Observe que, uma vez que um nó principal executa uma única cópia dos serviços do sistema, você precisaria de pelo menos três nós primários para os níveis de confiabilidade *Bronze*, cinco para *Prata*, sete para *Ouro* e nove para *Platina*.


<a id="nodetypes"></a>
### <a name="**nodetypes**"></a>**nodeTypes**
A seção **nodeTypes** descreve o tipo de nó que seu cluster tem. Pelo menos um tipo de nó deve ser especificado para um cluster, como mostrado no fragmento a seguir. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpoint": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20001",
            "endPort": "20031"
        },
        "ephemeralPorts": {
            "startPort": "20032",
            "endPort": "20062"
        },
        "isPrimary": true
    }]

O **nome** é o nome amigável para esse tipo de nó específico. Para criar um nó desse tipo de nó, será necessário atribuir o nome amigável para esse tipo de nó à variável **nodeTypeRef** daquele nó, conforme mencionado na seção [Nós no cluster](#clusternodes) acima. Para cada tipo de nó, você pode definir vários pontos de extremidade para se conectar a esse cluster. Você pode escolher qualquer número de porta para esses pontos de extremidade de conexão, desde que eles não entrem em conflito com qualquer outro ponto de extremidade neste cluster. Se deseja criar uma porta de gateway de aplicativo http, você pode especificar "reverseProxyEndpointPort": [número da porta], além das outras portas mostradas acima. Em um cluster com vários tipos de nó, haverá um tipo de nó primário, com **isPrimary** definido como *true*. O restante dos nós terá **isPrimary** definido como *false*. Leia [Considerações de planejamento de capacidade de cluster do Service Fabric](service-fabric-cluster-capacity.md) para saber mais sobre os valores de **nodeTypes** e **reliabilityLevel**, de acordo com a capacidade de seu cluster, bem como para saber a diferença entre os tipos de nó primários e não primários.


### <a name="**fabricsettings**"></a>**fabricSettings**
Esta seção permite que você defina os diretórios raiz para os dados e logs do Service Fabric. Você pode personalizá-los somente durante a criação inicial do cluster. Veja abaixo um exemplo de trecho de código desta seção.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\ProgramData\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\ProgramData\SF\Log"
    }]

Recomenda-se usar uma unidade não de SO, como FabricDataRoot e FabricLogRoot, pois isso proporciona mais confiabilidade contra falhas do SO. Observe que se você personalizar somente a raiz dos dados, a raiz do log será colocada um nível abaixo da raiz dos dados.


## <a name="next-steps"></a>Próximas etapas

Quando o arquivo ClusterConfig.JSON estiver totalmente configurado de acordo com a configuração de cluster autônomo, você poderá implantar o cluster seguindo as instruções no artigo [Criar um cluster do Azure Service Fabric local ou na nuvem](service-fabric-cluster-creation-for-windows-server.md) e, em seguida, em [Visualizando o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).





<!--HONumber=Oct16_HO2-->


