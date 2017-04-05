---
title: "Preparação para implantação de cluster autônomo do Azure Service Fabric | Microsoft Docs"
description: "Documentação relacionada à preparação do ambiente e à criação da configuração de cluster a ser considerada antes de implantar um cluster voltado para a manipulação de uma carga de trabalho de produção."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: maburlik;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: bd9ea82adc97698957dfebc3ee7c14c498808dd3
ms.lasthandoff: 03/29/2017


---

<a id="preparemachines"></a>

## <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planejar e preparar a implantação de cluster autônomo do Service Fabric
Execute as seguintes etapas antes de criar o cluster.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Etapa 1: Planejar a infraestrutura de cluster
Você está prestes a criar um cluster do Service Fabric em seus computadores. Sendo assim, você pode decidir a quais tipos de falhas deseja que o cluster sobreviva. Por exemplo, você precisa separar as linhas de energia ou conexões de Internet fornecidas para os computadores? Além disso, considere a segurança física dos computadores. Onde os computadores estão localizados e quem precisa acessá-los? Depois de tomar essas decisões, você poderá mapear logicamente os computadores para vários domínios de falha (confira a Etapa 4). O planejamento da infraestrutura para os clusters de produção está mais envolvido do que para os clusters de teste.

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Etapa 2: Preparar os computadores para atender os pré-requisitos
Pré-requisitos para cada computador que você deseja adicionar ao cluster:

* É recomendável, no mínimo, 16 GB de RAM.
* É recomendável um espaço em disco disponível mínimo de 40 GB.
* É recomendável uma CPU com quatro núcleos ou mais.
* Conectividade com uma rede segura para todos os computadores.
* Windows Server 2012 R2 ou Windows Server 2016. 
* [.NET Framework 4.5.1 ou posterior](https://www.microsoft.com/download/details.aspx?id=40773), instalação completa.
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
* O [serviço RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve estar em execução em todos os computadores.

O administrador do cluster que implanta e configura o cluster deve ter [privilégios de administrador](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) em cada um dos computadores. Você não pode instalar o Service Fabric em um controlador de domínio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Etapa 3: Determinar o tamanho inicial do cluster
Cada nó em um cluster do Service Fabric autônomo tem o tempo de execução do Service Fabric implantado e é membro do cluster. Em uma implantação típica de produção, há um nó por instância de SO (física ou virtual). O tamanho do cluster é determinado por suas necessidades comerciais. No entanto, seu cluster deve ter um tamanho mínimo de três nós (computadores ou máquinas virtuais).
Para fins de desenvolvimento, você pode ter mais de um nó em um determinado computador. Em um ambiente de produção, o Service Fabric dá suporte apenas a um nó por máquina virtual ou física.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Etapa 4: Determinar o número de domínios de falha e domínios de atualização
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

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Etapa 5: baixar o pacote autônomo do Service Fabric para Windows Server
[Baixe o Pacote autônomo do Service Fabric – Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote para um computador de implantação que não faça parte do cluster ou para um dos computadores que farão parte do cluster.

### <a name="step-6-modify-cluster-configuration"></a>Etapa 6: modificar a configuração do cluster
Para criar um cluster autônomo, você precisa criar um arquivo de configuração de cluster autônomo ClusterConfig.json, que descreve a especificação do cluster. Você pode basear o arquivo de configuração nos modelos encontrados no link abaixo. <br>
[Configurações de cluster autônomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Para obter detalhes sobre as seções neste arquivo, confira [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md).

Abra um dos arquivos ClusterConfig.json do pacote baixado e modifique as seguintes configurações:
| **Parâmetro de configuração** | **Descrição** |
| --- | --- |
| **NodeTypes** |Os tipos de nós permitem separar os nós do cluster em vários grupos. Um cluster precisa ter pelo menos um NodeType. Todos os nós em um grupo têm as seguintes características comuns:  <br> **Nome** – esse é o nome do tipo de nó. <br>**Portas do Ponto de Extremidade** : são vários pontos de extremidade (portas) nomeados que estão associados a esse tipo de nó. Você pode usar qualquer número de porta que desejar, desde que não entre em conflito com nada mais neste manifesto e já não esteja em uso por outro aplicativo em execução no computador/VM. <br> **Propriedades de posicionamento** – Descrevem as propriedades do tipo de nó que você usou como restrições de posicionamento para os serviços do sistema ou seus serviços. Essas propriedades são pares de chave/valor definidos pelo usuário que fornecem metadados extras para um determinado nó. Exemplos de propriedades de nó incluem se o nó temum disco rígido ou uma placa de vídeo, o número de eixos no disco rígido, núcleos e outras propriedades físicas. <br> **Capacidades** : as capacidades do nó definem o nome e a quantidade de um recurso específico que um determinado nó tem disponível para consumo. Por exemplo, um nó pode definir que tem capacidade para uma métrica chamada "MemoryInMb" e que tem 2048 MB de memória disponível por padrão. Essas capacidades são usadas no tempo de execução para garantir que os serviços que exigem quantidades específicas de recursos sejam colocados em nós que tenham esses recursos disponíveis nas quantidades necessárias.<br>**IsPrimary** – se você tiver mais de um NodeType definido, verifique se somente um está definido como principal com o valor *true*, que é onde os serviços do sistema são executados. Todos os outros tipos de nó devem ser definidos para o valor *false* |
| **Nós** |Esses são os detalhes de cada um dos nós que fazem parte do cluster (tipo de nó, nome do nó, endereço IP, domínio de falha e domínio de atualização do nó). Os computadores nos quais você deseja que o cluster seja criado precisam ser listados aqui com os endereços IP. <br> Se você usar o mesmo endereço IP para todos os nós, um cluster one-box será criado, que você pode usar para fins de teste. Não use clusters de uma caixa para implantar cargas de trabalho de produção. |

Depois que a configuração do cluster tiver todas as configurações definidas no ambiente, ela poderá ser testada no ambiente de cluster (etapa 7).

<a id="environmentsetup"></a>

### <a name="step-7-environment-setup"></a>Etapa 7. Configuração do ambiente

Quando um administrador de cluster configura um cluster autônomo do Service Fabric, o ambiente precisa ser configurado com os seguintes critérios: <br>
1. O usuário que está criando o cluster deve ter privilégios de segurança de nível de administrador para todos os computadores listados como nós no arquivo de configuração do cluster.
2. No computador do qual o cluster é criado, bem como em cada computador do nó de cluster, é necessário:
* Desinstalar o SDK do Service Fabric
* Desinstalar o tempo de execução do Service Fabric 
* Habilitar o serviço de Firewall do Windows (mpssvc)
* Habilitar o Serviço de Registro Remoto (remoteregistry)
* Habilitar o compartilhamento de arquivos (SMB)
* Abrir as portas necessárias, com base nas portas de configuração de cluster
* Abrir as portas necessárias para o Serviço de Registro Remoto e o SMB do Windows: 135, 137, 138, 139 e 445
* Ter conectividade de rede com os outros computadores
3. Nenhum dos computadores de nó de cluster deve ser um controlador de domínio.
4. Se o cluster a ser implantado é um cluster seguro, valide que os pré-requisitos de segurança necessários estão em vigor e configurados corretamente em relação à configuração.
5. Se os computadores do cluster não são acessíveis pela Internet, defina o seguinte na configuração do cluster:
* Desabilitar a telemetria: em *properties*, defina   *"enableTelemetry": false*
* Desabilitar o download automático de versão do e Fabric e as notificações de que a versão do cluster atual está aproximando do fim do suporte: em *properties*, defina   *"fabricClusterAutoupgradeEnabled": true*
6. Defina as exclusões de antivírus do Service Fabric apropriadas:

| **Diretórios de Antivírus excluídos** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (da configuração de cluster) |
| FabricLogRoot (da configuração de cluster) |

| **Processos do antivírus excluídos** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

### <a name="step-8-validate-environment-using-testconfiguration-script"></a>Etapa 8. Validar o ambiente usando o script TestConfiguration
O script TestConfiguration.ps1 pode ser encontrado no pacote autônomo. Ele é usado como um Analisador de Práticas Recomendadas para validar alguns dos critérios acima e deve ser usado como uma verificação de integridade para validar se um cluster pode ser implantado em um ambiente específico. Se houver alguma falha, consulte a lista em [Configuração do Ambiente](service-fabric-cluster-standalone-deployment-preparation.md) para solução de problemas. 

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

No momento, este módulo de teste de configuração não valida a configuração de segurança, por isso deve ser feito de forma independente.  

> [!NOTE]
> Estamos continuamente fazendo aprimoramentos para tornar esse módulo mais robusto, então, se há um caso com falha ou ausente que você acredita não ter sido detectado até o momento pelo TestConfiguration, informe-nos pelos nossos [canais de suporte](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Criar um cluster autônomo em execução no Windows Server](service-fabric-cluster-creation-for-windows-server.md)

