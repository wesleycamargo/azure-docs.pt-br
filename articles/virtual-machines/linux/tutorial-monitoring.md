---
title: "Monitorar máquinas virtuais Linux no Azure | Microsoft Docs"
description: "Saiba como monitorar o diagnóstico de inicialização e as métricas de desempenho em uma máquina virtual do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3fe8390e88e609b57a462e066f972346f8e8730e
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---
# <a name="how-to-monitor-a-linux-virtual-machine-in-azure"></a>Como monitorar uma máquina virtual do Linux no Azure

Para garantir que suas VMs (máquinas virtuais) no Azure estejam sendo executadas corretamente, examine o diagnóstico de inicialização e as métricas de desempenho. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Configurar monitoramento avançado


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, você precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupMonitor* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVM*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Habilitar o diagnóstico de inicialização

Durante a inicialização das VMs do Linux, a extensão de diagnóstico de inicialização captura a saída de inicialização e a armazena no Armazenamento do Azure. Esses dados podem ser usados para solucionar problemas de inicialização da VM. Os diagnósticos de inicialização não são habilitados automaticamente quando você cria uma VM do Linux usando a CLI do Azure.

Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. As contas de armazenamento devem ter um nome exclusivo globalmente, com três a 24 caracteres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#create). Neste exemplo, uma cadeia de caracteres aleatória é usada para criar um nome de conta de armazenamento exclusivo. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Ao habilitar o diagnóstico de inicialização, o URI do contêiner de armazenamento de blobs é exigido. O comando a seguir consulta a conta de armazenamento para retornar esse URI. O valor do URI é armazenado em uma variável chamada *bloburi*, que é usada na próxima etapa.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Agora, habilite o diagnóstico de inicialização com [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#enable). O valor `--storage` é o URI do blob coletado na etapa anterior.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Quando o diagnóstico de inicialização for habilitado, sempre que você parar e iniciar a VM, as informações sobre o processo de inicialização serão gravadas em um arquivo de log. Para este exemplo, primeiro desaloque a VM com o comando [az vm deallocate](/cli/azure/vm#deallocate) da seguinte maneira:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start]( /cli/azure/vm#stop) da seguinte maneira:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Você pode obter os dados de diagnóstico de inicialização para *myVM* com o comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#get-boot-log) da seguinte maneira:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Exibir métricas de host

Uma VM do Linux tem um host dedicado no Azure com o qual ela interage. As métricas são coletadas automaticamente para o host e podem ser exibidas no Portal do Azure da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroupMonitor** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como está o desempenho da VM do host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de *Host* em **Métricas disponíveis**.

    ![Exibir métricas de host](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Instalar extensão de diagnóstico

> [!IMPORTANT]
> Este documento descreve a versão 2.3 da Extensão de Diagnóstico do Linux, a qual está preterida. A versão 2.3 será suportada até 30 de junho de 2018.
>
> A versão 3.0 da Extensão de Diagnóstico do Linux pode ser ativada em vez disso. Para obter mais informações, consulte a [documentação](./diagnostic-extension.md).

As métricas de host básicas estão disponíveis, mas para ver métricas mais granulares e específicas de VM, você precisa instalar a extensão de Diagnóstico do Azure na VM. A extensão de Diagnóstico do Azure permite que dados de monitoramento e diagnóstico adicionais sejam recuperados da VM. Você pode exibir essas métricas de desempenho e criar alertas com base no desempenho de uma máquina virtual. A extensão de diagnóstico é instalada por meio do Portal do Azure, da seguinte maneira:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
1. Clique em **Configurações de diagnóstico**. A lista mostra que o *Diagnóstico de inicialização* já está habilitado na seção anterior. Clique na caixa de seleção para as *Métricas básicas*.
1. Na seção *Conta de armazenamento*, navegue até a conta *mydiagdata[1234]* criada na seção anterior e selecione-a.
1. Clique no botão **Salvar** .

    ![Exibir métricas de diagnóstico](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Exibir métricas de VM

Você pode exibir as métricas da VM da mesma maneira que você exibiu as métricas da VM host:

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
1. Para ver como está o desempenho da VM do Host, clique em **Métricas** na folha da VM e, em seguida, selecione qualquer uma das métricas de diagnóstico em **Métricas disponíveis**.

    ![Exibir métricas de VM](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Criar alertas

Você pode criar alertas com base em métricas de desempenho específicas. Alertas podem ser usados para lhe notificar quando uso médio da CPU excede um determinado limite ou o espaço em disco livre disponível cai abaixo de um determinado valor, por exemplo. Os alertas são exibidos no Portal do Azure, ou podem ser enviados por email. Você também pode disparar Runbooks da Automação do Azure ou Aplicativos Lógicos do Azure em resposta à geração de alertas.

O exemplo a seguir cria um alerta para uso médio da CPU.

1. No Portal do Azure, clique em **Grupos de Recursos**, selecione **myResourceGroup** e, em seguida, selecione **myVM** na lista de recursos.
2. Clique em **Regras de alerta** na folha da VM e, em seguida, clique em **Adicionar alerta de métrica** na parte superior da folha de alertas.
4. Forneça um **Nome** para o alerta, como *myAlertRule*
5. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.
6. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.
7. Clique no botão **OK**.


## <a name="advanced-monitoring"></a>Monitoramento avançado 

Você pode fazer um monitoramento mais avançado da sua VM usando o [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Caso ainda não tenha feito isso, você pode se inscrever para uma [avaliação gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) do Operations Management Suite.

Quando você tem acesso ao portal do OMS, você pode encontrar a chave e o identificador de espaço de trabalho na folha Configurações. Substitua <workspace-key> e <workspace-id> pelos valores de seu espaço de trabalho OMS e, em seguida, use **az vm extension set** para adicionar a extensão do OMS à VM:

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

Na folha Pesquisa de Logsdo portal do OMS, você deve ver *myVM*, como mostra a figura a seguir:

![Folha do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou e revisou VMs com a Central de Segurança do Azure. Você aprendeu como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Configurar monitoramento avançado

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](./tutorial-azure-security.md)
