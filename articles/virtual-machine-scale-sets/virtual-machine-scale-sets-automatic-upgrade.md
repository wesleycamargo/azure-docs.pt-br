---
title: Atualizações automáticas de imagem do sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba como atualizar automaticamente a imagem do sistema operacional em instâncias de VM em um conjunto de dimensionamento
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: cf25d08fc9a0e1ae458d350be93af31447928ecb
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069447"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Atualizações automáticas de imagem do sistema operacional do conjunto de dimensionamento de máquinas virtuais do Azure

A atualização automática de imagem do sistema operacional é um recurso dos conjuntos de dimensionamento de máquinas virtuais do Azure que atualiza automaticamente todas as VMs para a imagem de sistema operacional mais recente.

A atualização de sistema operacional do Azure tem as seguintes características:

- Depois de configurada, a imagem mais recente do sistema operacional publicada pelos editores de imagem é aplicada automaticamente ao conjunto de dimensionamento sem intervenção do usuário.
- Atualiza lotes de instâncias de maneira ininterrupta sempre que uma nova imagem de plataforma é publicada pelo editor.
- Integra-se à investigação de integridade do aplicativo.
- Funciona para todos os tamanhos de VM e para imagens da plataforma Windows e Linux.
- É possível recusar os upgrades automáticos a qualquer momento (os upgrades do sistema operacional podem ser iniciados manualmente também).
- O disco do sistema operacional de uma VM é substituído pelo novo disco do sistema operacional criado com a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos.
- No momento, não há suporte para o Azure Disk Encryption (na versão prévia).  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Como a atualização automática de imagem do sistema operacional funciona?

A atualização trabalha substituindo o disco do sistema operacional de uma VM por um novo disco criado usando a versão mais recente da imagem. As extensões configuradas e os scripts de dados personalizados são executados, enquanto os discos de dados persistentes são retidos. Para minimizar o tempo de inatividade do aplicativo, as atualizações ocorrem em lotes de máquinas, com até 20% do conjunto de dimensionamento sendo atualizado de cada vez. Também existe a opção de integrar uma investigação de integridade do aplicativo Azure Load Balancer. É altamente recomendado incorporar uma pulsação do aplicativo e validar o sucesso da atualização para cada lote no processo de atualização. As etapas de execução são: 

1. Antes de iniciar o processo de atualização, o orquestrador garantirá que no máximo 20% das instâncias não estejam íntegras. 
2. Identifique o lote de instâncias de VM a serem atualizadas. Cada lote deve ter no máximo 20% da contagem total de instâncias.
3. Atualize a imagem do sistema operacional desse lote de instâncias de VM.
4. Se o cliente tiver configurado investigações de integridade do aplicativo, a atualização aguardará até cinco minutos para que as investigações se tornem íntegras, antes de passar para a atualização do próximo lote. 
5. Se houver instâncias remanescentes a serem atualizadas, passe para a etapa 1) para o próximo lote; caso contrário, o atualização será concluído.

O orquestrador de atualização do sistema operacional do conjunto de dimensionamento verifica a integridade geral das instâncias de VM antes de atualizar cada lote. Ao atualizar um lote, pode haver outra manutenção planejada ou não planejada simultânea acontecendo nos Data centers do Azure que pode afetar a disponibilidade de suas VMs. Portanto, é possível que, temporariamente, mais de 20% das instâncias possam ficar inoperantes. Nesses casos, no final do lote atual, o atualização do conjunto de dimensionamento é interrompido.

## <a name="supported-os-images"></a>Imagens do sistema operacional com suporte
No momento, há suporte apenas determinadas imagens de plataforma do sistema operacional. No momento, não é possível usar imagens personalizadas que você mesmo criou. 

No momento, há suporte para os seguintes SKUs (serão adicionados outros futuramente):
    
| Publicador               | Oferta de sistema operacional      |  Sku               |
|-------------------------|---------------|--------------------|
| Canônico               | UbuntuServer  | 16.04-LTS          |
| Canônico               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Estável             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* O suporte para essas imagens está sendo distribuindo no momento e ficará disponível em todas as regiões do Azure em breve. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisitos para configurar a atualização automática de imagem do sistema operacional

- A propriedade *versão* da imagem da plataforma deve ser definida como *a mais recente*.
- Use as investigações de integridade do aplicativo para conjuntos de dimensionamento que não sejam do Service Fabric.
- Verifique se os recursos aos quais o modelo de conjunto de dimensionamento está se referindo estão disponíveis e estão sendo atualizados. 
  O URI Exa.SAS para conteúdo de inicialização nas propriedades de extensão da VM e o conteúdo na conta de armazenamento fazem referência aos segredos no modelo. 

## <a name="configure-automatic-os-image-upgrade"></a>Configurar a atualização automática da imagem do sistema operacional
Para configurar a atualização automática da imagem do sistema operacional, verifique se *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* está definido como *true* na definição do modelo do conjunto de dimensionamento. 

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{ 
  "properties": { 
    "upgradePolicy": { 
      "automaticOSUpgradePolicy": { 
        "enableAutomaticOSUpgrade":  true 
      } 
    } 
  } 
} 
```

O exemplo a seguir usa a CLI do Azure (2.0.47 ou posterior) para configurar atualizações automáticas para o conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
O suporte para configurar essa propriedade por meio do Azure PowerShell será lançado em breve.

## <a name="using-application-health-probes"></a>Usando investigações de integridade do aplicativo 

Durante a atualização do sistema operacional, as instâncias de VM em um conjunto de dimensionamento são atualizadas em um lote por vez. A atualização deverá continuar apenas se o aplicativo do cliente for íntegro nas instâncias de VM atualizadas. Recomendamos que o aplicativo ofereça sinais de integridade ao mecanismo de atualização do sistema operacional do conjunto de dimensionamento. Por padrão, durante os upgrades do sistema operacional, a plataforma considera o estado de energia da VM e o estado de provisionamento da extensão para determinar se uma instância VM é íntegra após uma atualização. Durante a atualização do sistema operacional de uma instância VM, o disco do sistema operacional em uma instância VM é substituído por um novo com base na versão mais recente da imagem. Após a conclusão de atualização do sistema operacional, as extensões configuradas são executadas nessas VMs. Apenas quando todas as extensões em uma VM forem provisionadas com êxito é que o aplicativo será considerado íntegro. 

Um conjunto de dimensionamento pode opcionalmente ser configurado com Investigações de integridade do aplicativo para oferecer à plataforma informações precisas sobre o estado em andamento do aplicativo. As Investigações de integridade do aplicativo são Investigações personalizadas do Load Balancer usadas como um sinal de integridade. O aplicativo em execução em uma instância VM do conjunto de dimensionamento pode responder a solicitações HTTP ou TCP externas que indicam se ele é íntegro. Para obter mias informações sobre como as Investigações personalizadas do Load Balancer funcionam, consulte [Noções básicas de investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Não é necessária uma Investigação de integridade do aplicativo para upgrades automáticos do sistema operacional, mas é recomendável.

Se o conjunto de dimensionamento estiver configurado para usar vários grupos de posicionamento, as investigações que usarem o [Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) precisarão ser usadas.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurando uma Investigação personalizada do Load Balancer como uma investigação de integridade do aplicativo em um conjunto de dimensionamento
Como uma melhor prática, crie uma investigação do balanceador de carga explicitamente para a integridade do conjunto de dimensionamento. Poderá ser usado o mesmo ponto de extremidade para uma investigação HTTP ou TCP existente, mas uma investigação de integridade pode exigir um comportamento diferente de uma investigação tradicional do balanceador de carga. Por exemplo, uma investigação tradicional do balanceador de carga poderá ser retornada não íntegra se a carga na instância for alta demais, enquanto que isso pode não ser adequado para determinar a integridade da instância durante uma atualização automática do sistema operacional. Configure a investigação para que ela tenha uma alta taxa de sondagem de menos de 2 minutos.

A investigação do balanceador de carga pode ser referenciada no *networkProfile* do conjunto de dimensionamento e associada a um balanceador de carga interno ou público da seguinte maneira:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```
> [!NOTE]
> Ao usar as atualizações automáticas do sistema operacional com o Service Fabric, a nova imagem do sistema operacional é implementada ao domínio de atualização pelo domínio de atualização para manter a alta disponibilidade dos serviços em execução no Service Fabric. Para obter mais informações sobre as características de durabilidade de clusters do Service Fabric, consulte [esta documentação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Manter as credenciais atualizadas
Se o conjunto de dimensionamento usar credenciais para acessar recursos externos, por exemplo, se uma extensão de VM que use um token SAS para a conta de armazenamento estiver configurada, você precisará verificar se as credenciais estão sendo atualizadas. Se quaisquer credenciais, incluindo certificados e tokens, tiveram expirado, a atualização falhará e o primeiro lote de VMs será deixado em estado de falha.

As etapas recomendadas para recuperar as VMs e habilitar novamente a atualização automática do sistema operacional, se houver uma falha de autenticação do recurso, são as seguintes:

* Gerar novamente o token (ou qualquer outra credencial) passado para suas extensões.
* Verificar se as credenciais usadas de dentro da VM para a comunicação com entidades externas estão atualizadas.
* Atualizar extensões no modelo do conjunto de escala com quaisquer tokens novos.
* Implantar o conjunto de escala atualizado, que atualizará todas as instâncias de VM, incluindo aquelas com falha. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Obter o histórico de atualizações automáticas de imagem do sistema operacional 
Você pode verificar o histórico da atualização do sistema operacional mais recente executado no conjunto de dimensionamento com o Azure PowerShell, a CLI do Azure 2.0 ou as APIs REST. Você pode obter o histórico das últimas cinco tentativas de atualização do sistema operacional nos últimos dois meses.

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Azure PowerShell para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0
O exemplo a seguir usa a CLI do Azure (2.0.47 ou posterior) para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
O exemplo a seguir usa a API REST para verificar o status do conjunto de dimensionamento denominado *myVMSS* no grupo de recursos denominado *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
Veja a documentação dessa API aqui: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

A chamada GET retorna propriedades semelhantes à saída de exemplo a seguir:

```json
{
  "value": [
    {
      "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
} 
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Como obter a versão mais recente de uma imagem do sistema operacional da plataforma? 

Você pode obter as versões de imagem para SKUs com suporte para atualização automática de sistema operacional usando os exemplos a seguir: 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Implantar com um modelo

É possível usar o seguinte modelo para implantar um conjunto de dimensionamento que usa atualizaçãos automáticos <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Atualizações sem interrupção – Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Próximas etapas
Para ver mais exemplos sobre como usar atualizaçãos automáticos do sistema operacional com conjuntos de dimensionamento, consulte se há [Recursos de versão prévia no repositório GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
