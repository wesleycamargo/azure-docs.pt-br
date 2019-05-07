---
title: Configurar o agendamento de aplicação de patch no SO para clusters Azure HDInsight baseados em Linux
description: Saiba como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203650"
---
# <a name="os-patching-for-hdinsight"></a>Aplicação de patch no HDInsight 

> [!IMPORTANT]
> As imagens do Ubuntu ficam disponíveis para a criação do novo cluster HDInsight dentro de 3 meses depois de publicado. A partir de janeiro de 2019, os clusters em execução **não** contam com patch automaticamente. Os clientes devem usar ações de script ou outros mecanismos para aplicar o patch de um cluster em execução. Clusters recém-criados sempre terão as atualizações mais recentes disponíveis, incluindo os mais recentes patches de segurança.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Como configurar o agendamento de aplicação de patch no SO para clusters HDInsight baseados em Linux
As máquinas virtuais em um cluster HDInsight precisam ser reinicializadas ocasionalmente para que os patches de segurança importantes possam ser instalados. 

Com a ação de script descrita neste artigo, você pode modificar a agenda de aplicação de patch no SO da seguinte maneira:
1. Instalar atualizações de sistema operacional completas ou instalar somente atualizações de segurança
2. Reinicie a VM

> [!NOTE]  
> Essa ação de script só funcionará com clusters HDInsight baseados em Linux criados após 1º de agosto de 2016. Os patches terão efeito somente após a reinicialização das VMs. Esse script não será aplicada automaticamente atualizações para todos os ciclos de atualização futura. Execute o script que sempre que novas atualizações precisam ser aplicadas para instalar as atualizações e reinicie a VM.

## <a name="how-to-use-the-script"></a>Como usar o script 

Ao usar esse script você precisará das seguintes informações:
1. O local do script: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  O HDInsight usa esse URI para localizar e executar o script em todas as máquinas virtuais no cluster.
  
2. Os tipos de nós do cluster ao qual o script é aplicado: nó de cabeçalho, nó de trabalho, zookeeper. Esse script deve ser aplicado a todos os tipos de nó do cluster. Se não for aplicado a um tipo de nó, as máquinas virtuais para esse tipo de nó não serão atualizadas.


3.  Parâmetro: Esse script aceita um parâmetro numérico:

    | Parâmetro | Definição |
    | --- | --- |
    | Atualizações do sistema operacional completo de instalação/instalar somente atualizações de segurança |0 ou 1. Um valor de 0 instala atualizações de segurança somente enquanto 1 instala atualizações de sistema operacional completas. Se nenhum parâmetro for fornecido, que o padrão é 0. |

> [!NOTE]  
> Marque esse script como persistente ao aplicar a um cluster existente. Caso contrário, quaisquer nós novos criado por meio de operações de dimensionamento usarão o agendamento de aplicação de patch padrão.  Se você aplicar o script como parte do processo de criação de cluster, ele será persistido automaticamente.

## <a name="next-steps"></a>Próximas etapas

Para conhecer as etapas específicas sobre como usar a ação de script, confira as seções a seguir em [Personalizar clusters HDInsight baseados em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md):

* [Usar uma ação de script durante a criação do cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Aplicar uma ação de script em um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
