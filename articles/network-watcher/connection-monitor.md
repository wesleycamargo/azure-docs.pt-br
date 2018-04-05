---
title: Monitorar conexões de rede com o Observador de Rede do Azure – Portal do Azure | Microsoft Docs
description: Saiba como monitorar a conectividade de rede com o Observador de Rede do Azure usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorar conexões de rede com o Observador de Rede do Azure usando o portal do Azure

Saiba como usar o monitor de conexões para monitorar a conectividade de rede entre uma máquina virtual do Azure e um endereço IP. O monitor de conexão fornece o monitoramento em nível de conexão. Uma conexão é definida como uma combinação de endereço IP e porta de origem e de destino. O monitor de conexão permite cenários como o monitoramento de conectividade de uma máquina virtual em uma rede virtual a uma máquina virtual executando o SQL server na mesma ou em outra rede virtual, através da porta 1433. O monitor de conexão fornece a latência de conexão como métrica do Azure Monitor, registrada a cada 60 segundos. Ele também fornece a você uma topologia de salto a salto e identifica problemas de configuração que afetam sua conexão.


## <a name="prerequisites"></a>pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de completar as etapas neste artigo:

* Uma instância do Observador de Rede na região onde você deseja monitorar uma conexão. Se você já não tiver criado uma instância do Observador de Rede, pode criar uma completando as etapas em [Criar uma instância do Observador de Rede](network-watcher-create.md).
* Uma máquina virtual da qual monitorar.
* Instale o `AzureNetworkWatcherExtension` na máquina virtual da qual você deseja monitorar uma conexão. Para instalar a extensão em uma máquina virtual do Windows, consulte [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para instalar a extensão em uma máquina virtual do Linux, consulte [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no [Portal do Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

As etapas a seguir habilitam o monitoramento de conexão em uma máquina virtual de destino através das portas 80 e 1433:

1. No lado esquerdo do portal, selecione **Mais serviços**.
2. Comece digitando *observador de rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
3. Em **MOITORAMENTO**, selecione **Monitor de conexão (Versão prévia)**. Recursos de versão prévia não têm o mesmo nível de confiabilidade ou disponibilidade de região que recursos de versão geral.
4. Selecione **+ Adicionar**.
5. Digite ou selecione as informações para a conexão que você deseja monitorar e selecione **Adicionar**. No exemplo mostrado na figura a seguir, a conexão monitorada está entre as máquinas virtuais *MultiTierApp0* e *Database0*:

    ![Adicionar monitor de conexão](./media/connection-monitor/add-connection-monitor.png)

    O monitoramento é iniciado. O monitor de conexões testa a cada 60 segundos.

## <a name="view-connection-monitoring"></a>Ver monitoramento de conexão

1. Conclua as etapas 1 a 3 no [Criar um monitor de conexão](#create-a-connection-monitor) para exibir o monitoramento de conexão.
2. A figura a seguir mostra os detalhes para a conexão AppToDB(80). O **Status** é alcançável. O **Exibição de gráfico** mostra o **Tempo médio de ida e volta** e **% de sondas com falha**. O gráfico fornece informações de salto a salto e mostra que não há problemas afetando a acessibilidade de destino.

    ![Ver monitor de conexão](./media/connection-monitor/view-connection-monitor.png)

3. Exibindo o monitor *AppToDB(1433)*, mostrado na figura a seguir, você verá que para as mesmas máquinas virtuais de origem e destino, o status está inacessível pela porta 1433. A **Exibição de grade** neste cenário fornece as informações de salto a salto e o problema que afeta o alcance. Nesse caso, uma regra NSG está bloqueando todo o tráfego na porta 1433 no segundo salto.

    ![Ver monitor de conexão](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como automatizar a captura de pacote com alertas de máquina virtual [Criando uma captura de pacotes acionados por alertas](network-watcher-alert-triggered-packet-capture.md).
- Determine se certo tráfego é permitido dentro ou fora de sua máquina virtual usando a [verificação de fluxo de IP](network-watcher-check-ip-flow-verify-portal.md).