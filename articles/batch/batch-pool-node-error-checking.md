---
title: Verificando erros no pool e no nó do Lote
description: Erros a serem verificados e como evitá-los ao criar pools e nós
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435814"
---
# <a name="checking-for-pool-and-node-errors"></a>Verificando erros no pool e no nó

Ao criar e gerenciar pools do Lote, há operações que acontecem imediatamente e há operações assíncronas que não são imediatas, são executadas em segundo plano e podem levar vários minutos para serem concluídas.

A detecção de falhas para operações que ocorrem imediatamente é simples, uma vez que quaisquer falhas serão retornadas imediatamente pela API, CLI ou interface do usuário.

Este artigo aborda as operações em segundo plano que podem ser realizadas para pools e nós de pool – ele especifica como as falhas podem ser detectadas e evitadas.

## <a name="pool-errors"></a>Erros do pool

### <a name="resize-timeout-or-failure"></a>Redimensionar tempo limite ou falha

Ao criar um novo pool ou redimensionar um existente, o número de destino de nós é especificado.  A operação será concluída imediatamente, mas a alocação real de novos nós ou a remoção de nós existentes ocorre em segundo plano durante o que pode levar alguns minutos.  Um tempo limite de redimensionamento é especificado na API [criar](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [redimensionar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) – se o número de destino de nós não puder ser obtido no período de tempo limite do redimensionamento, a operação será interrompida com o pool atingindo um estado estável e apresentando erros de redimensionamento.

Um tempo limite de redimensionamento é relatado pela propriedade [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) para a última avaliação, que lista um ou mais erros que ocorreram.

As causas comuns dos tempos limite de redimensionamento incluem:
- O tempo limite de redimensionamento é muito curto
  - O tempo limite padrão é de 15 minutos, o que normalmente é um tempo amplo para que nós de pool sejam alocados ou removidos.
  - Ao alocar um grande número de nós (mais de 1000 nós de uma imagem do Marketplace ou mais de 300 nós de uma imagem personalizada), durante a criação ou redimensionamento do pool, então é recomendável um tempo limite de 30 minutos.
- Cota de núcleo insuficiente
  - Uma conta de lote tem uma cota para o número de núcleos alocados entre todos os pools.  O lote não alocará nós após essa cota ter sido atingida.  A cota de núcleo [pode ser aumentada](https://docs.microsoft.com/azure/batch/batch-quota-limit) para permitir que mais nós sejam alocados.
- IPs de sub-rede insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Uma sub-rede de rede virtual deve ter endereços IP não atribuídos suficientes a serem alocados a todos os nós de pool que estão sendo solicitados, caso contrário, os nós não poderão ser criados.
- Recursos insuficientes quando um [pool está em uma rede virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Recursos como balanceadores de carga, IPs públicos e NSGs são criados na assinatura usada para criar a conta do Lote.  As cotas de assinatura para esses recursos devem ser suficientes.
- Usando uma imagem de VM personalizada para grandes pools
  - Grandes pools que usam imagens personalizadas podem demorar mais para alocar, e podem ocorrer tempos limite de redimensionamento.  Recomendações sobre limites e configuração são fornecidas em um [artigo específico](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Falhas de dimensionamento automático

Em vez de definir explicitamente o número de destino de nós para um pool na criação ou redimensionamento de pools, o número de nós em um pool pode ser dimensionado automaticamente.  Uma [fórmula de dimensionamento automático pode ser criada para um pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), que será avaliado em um intervalo configurável regular para definir o número de destino de nós do pool.  Os tipos de problemas a seguir podem ocorrer e serem detectados:

- A avaliação de dimensionamento automático pode falhar.
- A operação de redimensionamento resultante pode falhar e atingir o tempo limite.
- Pode haver um problema com a fórmula de dimensionamento automático, levando a valores de destino de nó incorretos, com o redimensionamento funcionando ou atingindo o tempo limite.

Informações sobre a última avaliação de dimensionamento automático são obtidas usando a propriedade [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun), que relata o horário da avaliação, os valores e o resultado da avaliação e quaisquer erros ao executar a avaliação.

Informações sobre todas as avaliações são capturadas automaticamente por um [evento completo de redimensionamento de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Excluir

Supondo que há nós em um pool, então uma operação de exclusão do pool faz os nós serem excluídos primeiro, seguidos pelo próprio objeto do pool.  A exclusão dos nós do pool pode levar alguns minutos para ser concluída.

O [estado do pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) será definido como "excluindo" durante o processo de exclusão.  O aplicativo de chamada pode detectar se a exclusão do pool está demorando demais usando as propriedades "state" e "stateTransitionTime".

## <a name="pool-compute-node-errors"></a>Erros do nó de computação do pool

Os nós podem ser alocados com êxito em um pool, mas vários problemas podem tornar os nós não íntegros e não utilizáveis.  Depois que os nós forem alocados em um pool, eles incorrem em encargos; portanto, é importante detectar problemas para evitar pagar por nós que não poderão ser usados.

### <a name="start-task-failure"></a>Falha na tarefa inicial

Uma [tarefa inicial](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) opcional pode ser especificada para um pool.  Assim como acontece com qualquer tarefa, uma linha de comando e os arquivos de recurso a serem baixados do armazenamento podem ser especificados.  A tarefa inicial é especificada para o pool, mas executada em cada nó – depois que cada nós tiver sido iniciado, então a tarefa inicia será executada.  Uma propriedade adicional da [tarefa inicial](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), "waitForSuccess", especifica se o Lote deve aguardar a conclusão com êxito da tarefa inicial antes de agendar quaisquer tarefas para um nó.

Se uma tarefa inicial falhar e sua configuração for especificada para aguardar a conclusão bem-sucedida, o nó não será utilizável e ainda incorrerão encargos.

Falhas na tarefa inicial podem ser detectadas usando as propriedades [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) da propriedade de nó [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) de nível superior.

Uma tarefa inicial com falha também faz o [estado](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) do nó ser definido como "starttaskfailed", mas apenas se "waitForSuccess" foi definido como "true".

Assim como acontece com qualquer tarefa, pode haver muitas causas para a falhar na tarefa inicial.  Para solucionar problemas, stdout, stderr e quaisquer arquivos de log de tarefas específicas adicionais devem ser verificados.

### <a name="application-package-download-failure"></a>Falha no download do pacote de aplicativos

Um ou mais pacotes de aplicativos podem ser especificados opcionalmente para um pool, com os arquivos do pacote especificado sendo baixados para cada nó e descompactados após o nó ser iniciado, mas antes de as tarefas serem agendadas.  É comum usar uma linha de comando de tarefa inicial, em conjunto com os pacotes de aplicativos, para copiar arquivos para um local diferente ou executar a configuração, por exemplo.

Uma falha no download e descompactação de um pacote de aplicativos será relatada pela propriedade [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) do nó.  O estado do nó será definido como "inutilizável".

### <a name="node-in-unusable-state"></a>Nó em estado inutilizável

O [estado do nó](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) pode ser definido como "inutilizável" por muitos motivos.  Quando forem "inutilizáveis", as tarefas não poderão ser agendadas para o nó, mas o nó ainda incorrerá em encargos.

O lote sempre tentará recuperar nós inutilizáveis, mas a recuperação pode ou não ser possível, dependendo da causa.

Quando a causa puder ser determinada, ela será relatada pela propriedade [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) do nó.

Alguns outros exemplos de causas para nós "inutilizáveis":

- Imagem personalizada inválida; não preparada corretamente, por exemplo.
- Falha na infraestrutura ou atualização de baixo nível fazendo a VM subjacente ser movida; o Lote recuperará o nó.

### <a name="node-agent-log-files"></a>Arquivos de log do agente de nó

Se for necessário contatar suporte com relação a um problema de nó de pool, então os arquivos de log do processo do agente do Lote executado em cada nó do pool poderão ser obtidos.  Os arquivos de log para um nó podem ser carregados por meio do portal do Azure, do Batch Explorer ou de uma [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Carregar e salvar os arquivos de log pode ser extremamente útil, porque o nó ou o pool pode ser excluído para salvar o custo dos nós em execução.

## <a name="next-steps"></a>Próximas etapas

Certifique-se de que seu aplicativo implementou a verificação de erros abrangente, principalmente para operações assíncronas, para que os problemas possam ser detectados rapidamente e diagnosticados.
