---
title: Verifique se há erros de pool e dos nós - Lote do Microsoft Azure
description: Erros a serem verificados e como evitá-los ao criar pools e nós
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 4e1e645c25d2f1e49e222e39ecd719a414e1404e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790462"
---
# <a name="check-for-pool-and-node-errors"></a>Verificar erros no pool e nos nós

Ao criar e gerenciar os pools do Lote do Microsoft Azure, algumas operações acontecem imediatamente. No entanto, algumas operações são assíncronas e executadas em segundo plano. Isso pode demorar vários minutos.

A detecção de falhas para operações que ocorrem imediatamente é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ocorrer para pools e nós de pool. Ele especifica como se pode detectar e evitar falhas.

## <a name="pool-errors"></a>Erros do pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um existente, o número de destino de nós é especificado.  A operação é concluída imediatamente, mas a alocação real de novos nós ou a remoção de nós existentes pode levar vários minutos.  Especifique o tempo limite de redimensionamento em [crie](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) a API. Se o lote não obter o número de nós de destino durante o período de tempo limite de redimensionamento, ele interrompe a operação. O pool entra em um estado estável e relata os erros de redimensionamento.

A propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a avaliação mais recente relata um tempo de limite de redimensionamento e lista os erros ocorridos.

As causas comuns dos tempos limite de redimensionamento incluem:

- O tempo limite de redimensionamento é muito curto
  - Na maioria das circunstâncias, o tempo limite padrão de 15 minutos é longo o suficiente para que os nós de pool sejam alocados ou removidos.
  - Se estiver alocando um grande número de nós, recomendamos definir o tempo limite de redimensionamento para 30 minutos. Por exemplo, quando estiver redimensionando a mais de 1.000 nós de uma imagem do Microsoft Azure Marketplace ou a mais de 300 nós de uma imagem VM personalizada.
- Cota de núcleo insuficiente
  - Uma conta de lote é limitadas em um número de núcleos alocados em todos os pools. O lote não alocará nós após essa cota ter sido atingida. Você [pode aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) a cota de núcleos para esse lote possa alocar mais nós.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual deve ter suficiente endereços IP não atribuídos para alocar cada nó de pool solicitado. Caso contrário, os nós não podem ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - É possível criar recursos como grupos de segurança de rede, IPs públicos e grupo de segurança de rede na mesma assinatura que a conta do lote. Verifique se as cotas de assinatura para esses recursos são suficientes.
- Grandes pools com imagens de VM personalizadas
  - Grandes pools que usam imagens personalizadas de VM podem demorar mais para alocar, e podem ocorrer tempos limite de redimensionamento.  Consulte [Usar uma imagem personalizada para criar um pool de máquinas virtuais](https://docs.microsoft.com/azure/batch/batch-custom-images) para obter recomendações sobre limites e configuração.

### <a name="automatic-scaling-failures"></a>Falhas de dimensionamento automático

Também se pode definir o Lote do Microsoft Azure para dimensionar automaticamente o número de nós em um pool. Defina os parâmetros da [fórmula de dimensionamento automático para um pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). O serviço de lote usa a fórmula para avaliar o número de nós no pool periodicamente e definir um novo número de destino. Os tipos de problemas a seguir podem ocorrer:

- A fórmula de dimensionamento automático falhar.
- A operação de redimensionamento resultante pode falhar e atingir o tempo limite.
- Um problema com a fórmula de dimensionamento automático pode resultar em valores de destino de nó incorretos. O redimensionamento funcionar ou atingir o tempo limite.

Obtenha informações sobre a última avaliação do dimensionamento automático usando a propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Essa propriedade informa o tempo de avaliação, os valores, o resultado e quaisquer erros de desempenho.

Informações sobre todas as avaliações são capturadas automaticamente por um [evento completo de redimensionamento de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Excluir

Ao excluir um pool que contém nós, o primeiro lote exclui os nós. Em seguida, ele exclui o próprio objeto do pool. A exclusão dos nós do pool pode levar alguns minutos para ser concluída.

O lote define o [estado do pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) será definido como **excluindo** durante o processo de exclusão. O aplicativo de chamada pode detectar se a exclusão do pool está demorando demais usando as propriedades **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Erros do nó de computação do pool

Mesmo quando o lote aloca com êxito os nós em um pool, diversos problemas podem causar que alguns dos nós sejam não íntegros e inutilizável. Esses nós incorrem em encargos. É importante detectar problemas para não pagar por nós inutilizáveis.

### <a name="start-task-failure"></a>Falha na tarefa inicial

Você talvez queira especificar um opcional [Iniciar tarefa](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) para um pool. Assim como acontece com qualquer tarefa, uma linha de comando e os arquivos de recurso para fazer o download do armazenamento podem ser especificados. A tarefa inicial é executada para cada nó depois que ele é iniciado. A propriedade **waitForSuccess** especifica se o lote aguarda até que a tarefa inicial seja concluída com êxito antes de agendar as tarefas para um nó.

E se o nó tiver sido configurado para aguardar a conclusão do iniciar tarefa com êxito, mas o iniciar tarefa falhar? Nesse caso, o nó não poderá ser usado, mas ainda incorrerá em encargos.

Falhas na tarefa inicial podem ser detectadas usando as propriedades [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Um iniciar tarefa que falhar também faz com que o lote defina o nó [state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) em **starttaskfailed** se definir o **waitForSuccess** como **true**.

Assim como acontece com qualquer tarefa, pode haver muitas causas para a falhar na tarefa inicial.  Para solucionar problemas, stdout, stderr e quaisquer arquivos de log de tarefas específicas adicionais devem ser verificados.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Você pode especificar um ou mais pacotes de aplicativos de um pool. O lote faz o download de arquivos de pacote especificados para cada nó e descompacta os arquivos depois que o nó for iniciado, mas antes que as tarefas forem agendadas. É comum usar uma linha de comando de tarefa inicial em conjunto com pacotes de aplicativos. Por exemplo, para copiar arquivos para um local diferente ou para executar a instalação.

Uma falha ao fazer o download e a descompactação de um pacote de aplicativos será relatada pela propriedade [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) do nó. O lote define o estado do nó como **inutilizável**.

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O Lote do Microsoft Azure pode definir o [estado do nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) pode ser definido como **inutilizável** por muitos motivos. Com o estado do nó definido como **inutilizável**, as tarefas não podem ser agendadas para o nó, mas ainda incorrerá em encargos.

O lote sempre tentará recuperar nós inutilizáveis, mas a recuperação pode ou não ser possível, dependendo da causa.

Se o lote poder determinar a causa, a propriedade do nó [erros](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) irá reportá-lo.

Alguns outros exemplos de causas para nós **inutilizáveis** incluem:

- A imagem de VM personalizada é inválida. Por exemplo, uma imagem que não é preparada corretamente.
- Uma VM é movida devido a uma falha de infraestrutura ou de uma atualização de nível baixo. O lote recupera o nó.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

O processo do agente do lote que é executado em cada nó do pool pode fornecer arquivos de log que podem ser útil se você precisar entrar em contato sobre um problema de nó do pool. Os arquivos de log para um nó podem ser carregados por meio do portal do Microsoft Azure, do Batch Explorer ou de uma [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). É útil carregar e salvar os arquivos de log. Depois disso, pode-se excluir o nó ou o pool para economizar o custo de nós em execução.

## <a name="next-steps"></a>Próximas etapas

Verifique se definiu seu aplicativo para implementar a verificação de erro abrangente, especialmente para operações assíncronas. Isso pode ser fundamental para detectar e diagnosticar problemas imediatamente.
