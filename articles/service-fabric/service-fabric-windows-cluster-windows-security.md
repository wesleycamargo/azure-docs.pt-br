---
title: "Proteger um cluster em execução no Windows usando a Segurança do Windows | Microsoft Docs"
description: "Saiba como configurar a segurança de nó para nó e de cliente para nó em um cluster autônomo em execução no Windows usando a Segurança do Windows."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: e9d7e1b5976719c07de78b01408b2546b4fec297
ms.openlocfilehash: 03aabfd3c86ba80a5af42ffa06cfbd2007f4927c
ms.lasthandoff: 02/16/2017


---
# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Proteger um cluster autônomo no Windows usando a segurança
Para impedir o acesso não autorizado ao cluster do Service Fabric, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Este artigo descreve como configurar a segurança de nó para nó e de cliente para nó usando a segurança do Windows no arquivo *ClusterConfig.JSON* e corresponde à etapa de configuração de segurança de [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para saber mais sobre como o Service Fabric usa a Segurança do Windows, veja [Cenários de segurança de cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Você deve considerar sua seleção de segurança para a segurança de nó para nó com cuidado, já que não há nenhuma atualização de cluster de uma opção de segurança para outra. A alteração da seleção de segurança exigiria uma recompilação completa do cluster.
> 
> 

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows usando gMSA
O arquivo de configuração *ClusterConfig.gMSA.Windows.MultiMachine.JSON* de exemplo baixado com o pacote de clusters independentes [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para a configuração da segurança do Windows usando [Conta de Serviço Gerenciado por Grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):

```
"security": {
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClustergMSAIdentity": "accountname@fqdn"
                "ClusterSPN": "fqdn"
                "ClientIdentities": [
                    {
                        "Identity": "domain\\username",
                        "IsAdmin": true
                    }
                ]
            }
        }
```

| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| WindowsIdentities |Contém as identidades do cluster e do cliente. |
| ClustergMSAIdentity |Configura a segurança de nó para nó. Uma conta de serviço gerenciado de grupo. |
| ClusterSPN |SPN de domínio totalmente qualificado para a conta gMSA|
| ClientIdentities |Configura a segurança de cliente para nó. Uma matriz de contas de usuário do cliente. |
| Identidade |A identidade do cliente, um usuário de domínio. |
| IsAdmin |True especifica que o usuário de domínio tem acesso de cliente de administrador, false para acesso de cliente de usuário. |

[A segurança entre nós](service-fabric-cluster-security.md#node-to-node-security) é configurada definindo **ClustergMSAIdentity** quando a malha do serviço precisar ser executada em gMSA. Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros. Isso pode ser feito de duas maneiras diferentes: especifique a conta de serviço gerenciada por grupo que inclui todos os nós no cluster, ou especifique o grupo de máquina de domínio que inclui todos os nós no cluster. É altamente recomendável usar a abordagem de [gMSA (Conta de Serviço Gerenciado de Grupo)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clusters maiores (com mais de 10 nós) ou para clusters com probabilidade de aumentar ou reduzir.
Essa abordagem não exige a criação de um grupo de domínios para o qual os administradores de cluster receberam direitos de acesso para adicionar e remover membros. Essas contas também são úteis para gerenciamento automático de senha. Para obter mais informações, confira [Introdução a contas de serviços gerenciados de grupo](http://technet.microsoft.com/library/jj128431.aspx).

[Segurança de cliente para nó](service-fabric-cluster-security.md#client-to-node-security) é configurada usando **ClientIdentities**. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Isso pode ser feito de duas maneiras diferentes: especifique os usuários do grupo de domínio que podem se conectar ou especifique os usuários de nó do domínio que podem se conectar. O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso oferece a capacidade para que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.  Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos. Para saber mais sobre controles de acesso, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

A seção de **segurança** do exemplo a seguir configura a segurança do Windows usando gMSA e especifica que os computadores no gMSA *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e que *CONTOSO\usera* tem acesso de cliente do administrador:

```
"security": {
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```

## <a name="configure-windows-security-using-a-machine-group"></a>Configurar a segurança do Windows usando um grupo de máquinas
O arquivo de configuração *ClusterConfig.Windows.MultiMachine.JSON* de exemplo baixado com o pacote de clusters independentes [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) contém um modelo para a configuração da segurança do Windows.  A segurança do Windows é configurada na seção **Propriedades** :

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

| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |**ClusterCredentialType** será definido como *Windows* se ClusterIdentity especificar um Nome de Grupo de Máquinas do Active Directory. |
| ServerCredentialType |A Segurança do Windows para clientes é habilitada definindo o parâmetro **ServerCredentialType** como *Windows*. Isso indica que os clientes do cluster, e o próprio cluster, estão sendo executados em um Domínio do Active Directory. |
| ClusterIdentity |Configura a segurança de nó para nó. Um nome de grupo do computador. |

[A segurança entre nós](service-fabric-cluster-security.md#node-to-node-security) é definida usando a configuração **ClusterIdentity**, se você quiser usar um grupo de máquinas em um Domínio do Active Directory. Para saber mais, confira [Criar um grupo de máquinas no Active Directory](https://msdn.microsoft.com/en-us/library/aa545347(v=cs.70).aspx).

A seção de **segurança** do exemplo a seguir configura a segurança do Windows e especifica que os computadores em *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e que *CONTOSO\usera* tem acesso de cliente do administrador:

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

> [!NOTE]
> O Service Fabric não devem ser implantado em um controlador de domínio. Certifique-se de que ClusterConfig.json não inclua o IP do controlador de domínio ao usar grupos de computadores ou gMSA.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Depois de configurar a segurança do Windows no arquivo *ClusterConfig.JSON* , retome o processo de criação de cluster em [Criar um cluster autônomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para saber mais sobre a segurança de nó para nó, a segurança de cliente para nó e o controle de acesso baseado em função, consulte [Cenários de segurança de cluster](service-fabric-cluster-security.md).

Confira [Conectar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de conexão usando o PowerShell ou o FabricClient.


