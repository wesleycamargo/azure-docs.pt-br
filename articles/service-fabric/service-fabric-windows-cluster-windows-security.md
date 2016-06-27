<properties
   pageTitle="Proteger um cluster em execução no Windows usando a Segurança do Windows | Microsoft Azure"
   description="Saiba como configurar a segurança de nó para nó e de cliente para nó em um cluster autônomo em execução no Windows usando a Segurança do Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/14/2016"
   ms.author="ryanwi"/>


# Proteger um cluster autônomo no Windows usando a segurança

Para impedir o acesso não autorizado ao cluster do Service Fabric, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Este artigo descreve como configurar a segurança de nó para nó e de cliente para nó usando a segurança do Windows no arquivo *ClusterConfig.JSON* e corresponde à etapa de configuração de segurança de [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para saber mais sobre como o Service Fabric usa a Segurança do Windows, veja [Cenários de segurança de cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Você deve considerar sua seleção de segurança para a segurança de nó para nó com cuidado, já que não há nenhuma atualização de cluster de uma opção de segurança para outra. A alteração da seleção de segurança exigiria uma recompilação completa do cluster.

## Configurar a segurança do Windows
O arquivo de configuração *ClusterConfig.Windows.JSON* de exemplo baixado com o pacote de clusters autônomos [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para a configuração da segurança do Windows. A segurança do Windows é configurada na seção **Properties**:

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
		"ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Parâmetro de configuração**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCredentialType|A Segurança do Windows é habilitada por meio da definição do parâmetro **ClusterCredentialType** para o *Windows*.|
|ServerCredentialType|A Segurança do Windows para clientes é habilitada por meio da definição do parâmetro **ServerCredentialType** para o *Windows*. Isso indica que os clientes do cluster, e o próprio cluster, estão sendo executados em um Domínio do Active Directory.|
|WindowsIdentities|Contém as identidades do cluster e do cliente.|
|ClusterIdentity|Configura a segurança de nó para nó. Uma lista separada por vírgulas de contas de serviço gerenciadas por grupo ou nomes de computador.|
|ClientIdentities|Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente.|
|Identidade|A identidade do cliente, um usuário de domínio.|
|IsAdmin|True especifica que o usuário de domínio tem acesso de cliente de administrador, false para acesso de cliente de usuário.|

A [Segurança de nó para nó](service-fabric-cluster-security.md#node-to-node-security) é configurada por meio da definição com **ClusterIdentity**. Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros. Isso pode ser feito de duas maneiras diferentes: especifique a conta de serviço gerenciada por grupo que inclui todos os nós no cluster ou especifique as identidades de nó de domínio de todos os nós no cluster. É altamente recomendável usar a abordagem [Conta de Serviço Gerenciada por Grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx), especialmente para clusters maiores (mais de 10 nós) ou para clusters com probabilidade de aumentar ou de reduzir. Essa abordagem permite que os nós sejam adicionados ou removidos da gMSA, sem exigir alterações no manifesto do cluster. Essa abordagem não exige a criação de um grupo de domínios para o qual os administradores de cluster receberam direitos de acesso para adicionar e remover membros. Para obter mais informações, confira [Introdução às contas de serviço gerenciadas por grupo](http://technet.microsoft.com/library/jj128431.aspx).

A [Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Isso pode ser feito de duas maneiras diferentes: especifique os usuários do grupo de domínio que podem se conectar ou especifique os usuários de nó do domínio que podem se conectar. O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso oferece a capacidade para que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

A seção **security** de exemplo a seguir configura a segurança do Windows e especifica que os computadores em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e que *CONTOSO\\usera* tem acesso de cliente do administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
		"ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## Próximas etapas

Depois de configurar a segurança do Windows no arquivo *ClusterConfig.JSON*, retome o processo de criação de cluster em [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para saber mais sobre a segurança de nó para nó, a segurança de cliente para nó e o controle de acesso baseado em função, confira [Cenários de segurança de cluster](service-fabric-cluster-security.md).

Confira [Conectar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de conexão usando o PowerShell ou o FabricClient.

<!---HONumber=AcomDC_0615_2016-->