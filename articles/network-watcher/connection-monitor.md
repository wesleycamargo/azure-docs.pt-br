---
title: "Monitorar conexões de rede com o Observador de Rede do Azure – Portal do Azure | Microsoft Docs"
description: Saiba como monitorar a conectividade de rede com o Observador de Rede do Azure usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>Monitorar conexões de rede com o Observador de Rede do Azure usando o portal do Azure

Saiba como usar o monitor de conexões para monitorar a conectividade de rede entre uma máquina virtual do Azure e um endereço IP. O endereço IP pode ser atribuído a outro recurso do Azure ou a um recurso da Internet ou no local.

## <a name="prerequisites"></a>pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de completar as etapas neste artigo:

* Uma instância do Observador de Rede na região onde você deseja monitorar uma conexão. Se você já não tiver criado uma instância do Observador de Rede, pode criar uma completando as etapas em [Criar uma instância do Observador de Rede](network-watcher-create.md).
* Uma máquina virtual da qual monitorar.
* Instale o `AzureNetworkWatcherExtension` na máquina virtual da qual você deseja monitorar uma conexão. Para instalar a extensão em uma máquina virtual do Windows, consulte [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para instalar a extensão em uma máquina virtual do Linux, consulte [Extensão da máquina virtual do Agente do Observador de Rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="sign-in-to-azure"></a>Entrar no Azure 

Entre no [Portal do Azure](http://portal.azure.com).

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

1. No lado esquerdo do portal, selecione **Mais serviços**.
2. Comece digitando *observador de rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
3. Em **MOITORAMENTO**, selecione **Monitor de conexão (Versão prévia)**. Recursos de versão prévia não têm o mesmo nível de confiabilidade ou disponibilidade de região que recursos de versão geral.
4. Selecione **+ Adicionar**.
5. Digite ou selecione as informações apropriadas para a conexão que você deseja monitorar e selecione **Adicionar**. Neste exemplo, uma conexão entre as máquinas virtuais *myVmSource* e *myVmDestination* são monitoradas na porta 80.
    
    |  Configuração                                 |  Valor               |
    |  -------------------------------------   |  ------------------- |
    |  NOME                                    |  myConnectionMonitor |
    |  Máquina virtual de origem                  |  myVmSource          |
    |  Porta de origem                             |                      |
    |  Destino, selecione uma máquina virtual   |  myVmDestination     |
    |  Porta de destino                        |  80                  |

6. O monitoramento é iniciado. O monitor de conexões testa a cada 60 segundos.
7. O monitor de conexões mostra os testes de tempo e porcentagem médios de viagem de ida e volta que falharam. Você pode exibir dados de monitoramento em uma grade ou em um gráfico.

## <a name="next-steps"></a>Próximas etapas

- Saiba como automatizar a captura de pacote com alertas de máquina virtual [Criando uma captura de pacotes acionados por alertas](network-watcher-alert-triggered-packet-capture.md).

- Determine se certo tráfego é permitido dentro ou fora de sua máquina virtual usando a [verificação de fluxo de IP](network-watcher-check-ip-flow-verify-portal.md).