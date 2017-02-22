---
title: Migrar VMs para o Resource Manager usando a CLI do Azure | Microsoft Docs
description: "Este artigo apresenta a migração de recursos com suporte da plataforma do modelo clássico para o Azure Resource Manager usando a CLI do Azure"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d6f5a877-05b6-4127-a545-3f5bede4e479
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: e64449991bc28427d8f559ed13c3bdf9160488db
ms.openlocfilehash: 92211cc98b6d8394ff04bc7c2fe33f7bd710713b


---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar recursos de IaaS do modelo clássico para o Azure Resource Manager usando a CLI do Azure
Estas etapas mostram como usar a CLI (interface de linha de comando) do Azure para migrar recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager. O artigo exige a [CLI do Azure](../xplat-cli-install.md).

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma repetirá a ação.
> 
> 

## <a name="step-1-prepare-for-migration"></a>Etapa 1: preparar para a migração
Veja a seguir algumas das práticas que recomendamos durante a avaliação de migração dos recursos de IaaS do modelo clássico para o Gerenciador de Recursos:

* Leia a [lista de recursos ou de configurações sem suporte](virtual-machines-windows-migration-classic-resource-manager.md). Caso você tenha máquinas virtuais que usam recursos ou configurações sem suporte, recomendamos que aguarde até que o suporte para o recurso/configuração seja anunciado. Como alternativa, é possível remover esse recurso ou mudar a configuração para habilitar a migração, caso ela atenda às suas necessidades.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Etapa 2: Definir sua assinatura e registrar o provedor
Para cenários de migração, é necessário instalar seu ambiente tanto para o modelo clássico quanto para o Gerenciador de Recursos. [Instale a CLI do Azure](../xplat-cli-install.md) e [selecione sua assinatura](../xplat-cli-connect.md).

Entre em sua conta.

    azure login

Selecione a assinatura do Azure usando o seguinte comando.

    azure account set "<azure-subscription-name>"

> [!NOTE]
> O registro é uma etapa única, mas é preciso executá-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro 
> 
> *BadRequest: a assinatura não está registrada para migração.* 
> 
> 

Registre-se no provedor de recursos de migração usando o comando a seguir. Observe que, em alguns casos, esse comando atinge o tempo limite. No entanto, o registro será bem-sucedido.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Aguarde cinco minutos para concluir o registro. É possível verificar o status da aprovação usando o comando a seguir. Verifique se RegistrationState é `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Agora mude a CLI para o modo `asm` .

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Etapa 3: Verifique se você tem uma quantidade suficiente de núcleos de Máquina Virtual do Azure Resource Manager na região do Azure de sua implantação atual ou VNET
Nesta etapa, você precisará alternar para o modo `arm` . Faça isso com o seguinte comando.

```
azure config mode arm
```

Você pode usar o seguinte comando da CLI para verificar a quantidade atual de núcleos no Azure Resource Manager. Para saber mais sobre cotas de núcleos, veja [Limites e o Azure Resource Manager](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Quando você terminar de verificar esta etapa, volte para o modo `asm` .

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Etapa 4: Opção 1 – Migrar máquinas virtuais em um serviço de nuvem
Obtenha a lista de serviços de nuvem usando o comando a seguir e escolha o serviço de nuvem que deseja migrar. Observe que, se as VMs no serviço de nuvem estiverem em uma rede virtual ou se tiverem funções web/de trabalho, você receberá uma mensagem de erro.

    azure service list

Execute a comando a seguir para obter o nome da implantação do serviço de nuvem por meio da saída detalhada. Na maioria dos casos, o nome da implantação é igual ao nome do serviço de nuvem.

    azure service show <serviceName> -vv

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

Se quiser migrar as máquinas virtuais em uma rede virtual criada por plataforma, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se quiser migrar para uma rede virtual existente no modelo de implantação do Gerenciador de Recursos, use o comando a seguir.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Após uma operação de preparação bem-sucedida, você poderá examinar a saída detalhada para obter o estado de migração das VMs e assegurar que as elas estejam no estado `Prepared` .

    azure vm show <vmName> -vv

Verifique a configuração dos recursos preparados usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

    azure service deployment commit-migration <serviceName> <deploymentName>



## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Etapa 4: Opção 2 – Migrar máquinas virtuais em uma rede virtual
Selecione a rede virtual que você deseja migrar. Observe que, se a rede virtual contiver funções web/de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Obtenha todas as redes virtuais na assinatura usando o comando a seguir.

    azure network vnet list

A saída será parecida com esta:

![Captura de tela da linha de comando com o nome inteiro da rede virtual realçado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

No exemplo acima, **virtualNetworkName** é o nome inteiro **“Grupo classicubuntu16 classicubuntu16”**.

Prepare a rede virtual de sua preferência para migração usando o comando a seguir.

    azure network vnet prepare-migration <virtualNetworkName>

Verifique a configuração para as máquinas virtuais preparadas usando a CLI ou o portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

    azure network vnet abort-migration <virtualNetworkName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Etapa 5: Migrar uma conta de armazenamento
Depois de concluir a migração das máquinas virtuais, recomendamos a migração da conta de armazenamento.

Prepare a conta de armazenamento para migração usando o comando a seguir

    azure storage account prepare-migration <storageAccountName>

Verifique a configuração da conta de armazenamento preparada usando a CLI ou o Portal do Azure. Se você não estiver pronto para a migração e desejar voltar para o estado anterior, use o comando a seguir.

    azure storage account abort-migration <storageAccountName>

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Próximas etapas
* [Migração de recursos de IaaS com suporte da plataforma do Clássico para o Gerenciador de Recursos](virtual-machines-windows-migration-classic-resource-manager.md)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)




<!--HONumber=Jan17_HO4-->


