---
title: Monitorar e atualizar máquinas virtuais do Linux no Azure | Microsoft Docs
description: Saiba como monitorar as métricas de desempenho e diagnóstico de inicialização e gerenciar atualizações de pacote em uma máquina virtual do Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d5fb239ffd6a957cbb088bf4843819e2c886cee8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Como monitorar e atualizar uma máquina virtual do Linux no Azure

Para garantir que suas VMs (máquinas virtuais) no Azure estejam sendo executadas corretamente, examine o diagnóstico de inicialização e as métricas de desempenho e gerencie as atualizações de pacote. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerenciar atualizações de pacote
> * Monitoramento de alterações e inventário
> * Configurar monitoramento avançado

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Criar VM

Para ver os diagnósticos e as métricas em ação, você precisa de uma VM. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupMonitor* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Agora, crie uma VM com [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVM*:

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

Antes de habilitar o diagnóstico de inicialização, é necessário criar uma conta de armazenamento para armazenar os logs de inicialização. As contas de armazenamento devem ter um nome exclusivo globalmente, com três a 24 caracteres e conter apenas números e letras minúsculas. Crie uma conta de armazenamento com o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create). Neste exemplo, uma cadeia de caracteres aleatória é usada para criar um nome de conta de armazenamento exclusivo.

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

Agora, habilite o diagnóstico de inicialização com [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). O valor `--storage` é o URI do blob coletado na etapa anterior.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Exibir diagnóstico de inicialização

Quando o diagnóstico de inicialização for habilitado, sempre que você parar e iniciar a VM, as informações sobre o processo de inicialização serão gravadas em um arquivo de log. Para este exemplo, primeiro desaloque a VM com o comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate) da seguinte maneira:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Agora, inicie a VM com o comando [az vm start]( /cli/azure/vm#az_vm_stop) da seguinte maneira:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Você pode obter os dados de diagnóstico de inicialização para *myVM* com o comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) da seguinte maneira:

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

As métricas de host básicas estão disponíveis, mas para ver métricas mais granulares e específicas à VM, você precisa instalar a extensão de Diagnóstico do Azure na VM. A extensão de Diagnóstico do Azure permite que dados de monitoramento e diagnóstico adicionais sejam recuperados da VM. Você pode exibir essas métricas de desempenho e criar alertas com base no desempenho de uma máquina virtual. A extensão de diagnóstico é instalada por meio do Portal do Azure, da seguinte maneira:

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
3. Forneça um **Nome** para o alerta, como *myAlertRule*
4. Para disparar um alerta quando o percentual de CPU excede 1.0 por cinco minutos, deixe todos os outros padrões selecionados.
5. Opcionalmente, marque a caixa de *Proprietários, colaboradores e leitores de email* para enviar uma notificação por email. A ação padrão é apresentar uma notificação no portal.
6. Clique no botão **OK**.

## <a name="manage-package-updates"></a>Gerenciar atualizações de pacote

O Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs Linux do Azure.
Diretamente de sua VM, você pode rapidamente avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM.

Para obter informações sobre preços, consulte [Preços de automação de Gerenciamento de Atualizações](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Habilitar Gerenciamento de Atualizações

Habilite o Gerenciamento de Atualizações para sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, clique em **Gerenciamento de Atualizações**. A tela **Habilitar Gerenciamento de Atualizações** é aberta.

Uma validação é executada para determinar se o Gerenciamento de Atualizações está habilitado para essa VM.
A validação inclui verificar se há um espaço de trabalho do Log Analytics e uma conta de automação vinculada e se a solução está no espaço de trabalho.

Um espaço de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md) é usado para coletar dados gerados por recursos e serviços, como o Gerenciamento de Atualizações.
O espaço de trabalho fornece um único local para examinar e analisar dados de várias fontes.
Para executar ações adicionais em máquinas virtuais que requerem atualizações, a Automação do Azure permite que você execute runbooks em VMs, como download e aplicação de atualizações.

O processo de validação também verifica se a VM é provisionada com o MMA (Microsoft Monitoring Agent) e o Hybrid Runbook Worker da Automação.
Esse agente é usado para comunicar-se com a VM e obter informações sobre o status de atualização.

Escolha o espaço de trabalho do Log Analytics e a conta de automação e clique em **Habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

Se algum dos seguintes pré-requisitos estiver ausente durante a integração, ele será adicionado automaticamente:

* Espaço de trabalho do [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automação](../../automation/automation-offering-get-started.md)
* Uma [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) está habilitada na VM

A tela **Gerenciamento de Atualizações** é exibida. Configure o local, o espaço de trabalho de Log analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo espaço de trabalho e conta de Automação devem ser usados.

![Habilitar a solução de Gerenciamento de Atualizações](./media/tutorial-monitoring/manage-updates-update-enable.png)

A habilitação da solução pode levar até 15 minutos. Durante esse tempo, não feche a janela do navegador. Depois que a solução for habilitada, as informações sobre atualizações ausentes na VM fluirão para o Log Analytics. Pode levar entre 30 minutos e 6 horas para que os dados fiquem disponíveis para análise.

### <a name="view-update-assessment"></a>Exibir avaliação de atualização

Depois que o **Gerenciamento de Atualizações** for habilitado, a tela **Gerenciamento de Atualizações** será exibida. Depois que a avaliação das atualizações estiver completa, você verá uma lista de atualizações ausentes na guia **Atualizações ausentes**.

 ![Exibir o status de atualização](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Agendar uma implantação de atualização

Para instalar atualizações, agende uma implantação que siga o agendamento de versão e o período de serviço. Você pode escolher quais tipos de atualização deseja incluir na implantação. Por exemplo, você pode incluir atualizações críticas ou de segurança e excluir pacotes cumulativos de atualizações.

Agende uma nova implantação de atualização para a VM clicando em **Agendar implantação de atualização** na parte superior da tela **Gerenciamento de Atualizações**. Na tela **Nova implantação de atualização**, especifique as seguintes informações:

* **Nome** – forneça um nome exclusivo para identificar a Implantação de atualizações.
* **Classificação de atualização** – selecione os tipos de software que a implantação de atualização incluiu na implantação. Os tipos de classificação são:
  * Atualizações críticas ou de segurança
  * Outras atualizações
* **Atualizações a serem excluídas** – você pode fornecer uma lista de nomes de pacote que devem ser ignorados durante a implantação da atualização. Os nomes de pacote dão suporte a curingas (como \*kernal\*).

  ![Tela de configurações de agenda de atualização](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Configurações de agenda** – você pode aceitar a data e hora padrão, que é de 30 minutos após a hora atual ou especificar um horário diferente.
  Você também pode especificar se a implantação ocorre uma única vez ou configurar um agendamento recorrente. Clique na opção Recorrente em Recorrência para configurar um agendamento recorrente.

  ![Tela de configurações de agenda de atualização](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Janela de manutenção (minutos)** – especifique o período de tempo em que deseja que a implantação de atualização ocorra. Isso ajuda a garantir que as alterações sejam executadas dentro das janelas de serviço definidas.

Depois de concluir a configuração da agenda, clique no botão **Criar** e retorne ao painel de status.
Observe que a tabela **Agendado** mostra a agenda de implantação criada.

> [!WARNING]
> Para atualizações que exigem uma reinicialização, a VM será reiniciada automaticamente.

### <a name="view-results-of-an-update-deployment"></a>Exibir resultados de uma implantação de atualização

Após o início da implantação agendada, você pode ver o status dessa implantação na guia **Implantações de atualização** na tela **Gerenciamento de Atualizações**.
Se ela estiver em execução, seu status será mostrado como **Em andamento**. Após a conclusão, em caso de êxito, ele será alterado para **Êxito**.
Se houver falha em uma ou mais atualizações na implantação, o status será **Falhou parcialmente**.
Clique na implantação de atualização concluída para ver o painel dessa implantação de atualização.

![Painel de status de implantação de atualização para implantação específica](./media/tutorial-monitoring/manage-updates-view-results.png)

No bloco **Resultados de atualização** há um resumo do número total de atualizações e os resultados da implantação na VM.
Na tabela à direita há uma análise detalhada de cada atualização e os resultados da instalação, que podem ser um dos seguintes valores:

* **Não foi tentada** – a atualização não foi instalada devido a tempo suficiente disponível com base na duração da janela de manutenção definida.
* **Êxito** – a atualização foi bem-sucedida
* **Falha** – Falha na atualização

Clique em **Todos os logs** para ver todas as entradas de log que a implantação criou.

Clique no bloco **Saída** para ver o fluxo de trabalho do runbook responsável por gerenciar a implantação de atualização na VM de destino.

Clique em **Erros** para ver informações detalhadas sobre os erros da implantação.

## <a name="monitor-changes-and-inventory"></a>Monitoramento de alterações e inventário

É possível coletar e exibir inventário para software, arquivos, daemons do Linux, serviços do Windows e chaves de Registro do Windows em seus computadores. Acompanhar as configurações de seus computadores pode ajudar a detectar problemas operacionais em seu ambiente e entender melhor o estado dos seus computadores.

### <a name="enable-change-and-inventory-management"></a>Habilitar Alterações e Gerenciamento de Estoque

Habilitar Alterações e Gerenciamento de Estoque para a sua VM:

1. No lado esquerdo da tela, selecione **Máquinas virtuais**.
2. Na lista, selecione uma VM.
3. Na tela da VM, na seção **Operações**, clique em **Estoque** ou **Rastrear mudanças**. A tela **Habilitar Controle de Alterações e Estoque** é aberta.

Configure o local, o espaço de trabalho de Log analytics e a conta de Automação a serem usados e clique em **Habilitar**. Caso os campos estejam esmaecidos, isso significa que outra solução de automação está habilitada para a VM e o mesmo espaço de trabalho e conta de Automação devem ser usados. Mesmo que as soluções estejam separadas no menu, elas são a mesma solução. Habilitar uma permite ambos para sua VM.

![Habilitar Controle de Alterações e Inventário](./media/tutorial-monitoring/manage-inventory-enable.png)

Depois que a solução é habilitada, leva algum tempo para os dados serem exibidos enquanto o inventário está sendo coletado na VM.

### <a name="track-changes"></a>Controle de alterações

Na sua VM, selecione **Controle de alterações** em **OPERAÇÕES**. Clique em **Editar configurações**, a página **Controle de Alterações** é exibida. Selecione o tipo de configuração que você deseja acompanhar e, em seguida, clique em **+ +Adicionar** para definir as configurações. A opção disponível para Linux é **Arquivos Linux**

Para obter informações detalhadas sobre o Controle de Alterações, consulte [Solucionar as alterações em uma máquina virtual](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Exibição do Inventário

Na sua VM, selecione **Inventário** em **OPERAÇÕES**. Na guia **Software**, há uma lista de tabelas que o software havia encontrado. Os detalhes de alto nível de cada registro de software estão visíveis na tabela. Esses detalhes incluem o nome do software, a versão, publicador, hora da última atualização.

![Exibição do Inventário](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorar os logs de atividades e alterações

Na página **Controle de alterações** em sua VM, selecione **Gerenciar Conexão do Log de Atividades**. Essa tarefa abre a página **Log de Atividades do Azure**. Selecione **Conectar** para conectar o Controle de alterações ao log de atividades do Azure para sua VM.

Com essa configuração habilitada, navegue até a página **Visão geral** para a VM e selecione **Parar** para interromper a VM. Quando solicitado, selecione **Sim** para interromper a VM. Quando ele for desalocado, selecione **Iniciar** para reiniciar a VM.

Interromper e iniciar uma VM registra um evento em seu log de atividades. Navegue de volta para a página **Controle de alterações**. Selecione a guia **Eventos** na parte inferior da página. Após algum tempo, os eventos são exibidos no gráfico e na tabela. Cada evento pode ser selecionado para exibir informações detalhadas sobre ele.

![Visualizar categorias no log de atividades](./media/tutorial-monitoring/manage-activitylog-view-results.png)

O gráfico mostra as alterações que ocorreram ao longo do tempo. Depois de adicionar uma conexão do Log de Atividades, o grafo da linha na parte superior exibe eventos do Log de Atividades do Azure. Cada linha de gráficos de barras representa um tipo de alteração controlável diferente. Esses tipos são daemons, arquivos e software Linux. A guia de alteração mostra os detalhes para as alterações mostradas na visualização em ordem decrescente de hora em que a alteração ocorreu (mais recentes primeiro).

## <a name="advanced-monitoring"></a>Monitoramento avançado

Você pode fazer monitoramento mais avançado da sua VM usando soluções como o Gerenciamento de Atualizações e Alterações e Inventário fornecidos pela [Automação do Azure](../../automation/automation-intro.md).

Quando você tem acesso ao espaço de trabalho do Log Analytics, você pode encontrar a chave do espaço de trabalho e o identificador de espaço de trabalho selecionando **Configurações avançadas** em **CONFIGURAÇÕES**. Substitua \<workspace-key\> e \<workspace-id\> pelos valores de seu espaço de trabalho do Log Analytics e use **az vm extension set** para adicionar a extensão à VM:

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

Depois de alguns minutos, você deverá ver a nova VM no espaço de trabalho do Log Analytics.

![Folha do OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou, examinou e gerenciou atualizações para uma VM. Você aprendeu como:

> [!div class="checklist"]
> * Habilitar o diagnóstico de inicialização na VM
> * Exibir diagnóstico de inicialização
> * Exibir métricas de host
> * Habilitar a extensão de diagnóstico na VM
> * Exibir métricas de VM
> * Criar alertas com base nas métricas de diagnóstico
> * Gerenciar atualizações de pacote
> * Monitoramento de alterações e inventário
> * Configurar monitoramento avançado

Avance para o próximo tutorial para saber mais sobre a Central de Segurança do Azure.

> [!div class="nextstepaction"]
> [Gerenciar a segurança da VM](./tutorial-azure-security.md)
