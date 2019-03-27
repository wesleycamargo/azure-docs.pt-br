---
title: O que está acontecendo com IA do Lote do Azure? | Microsoft Docs
description: Saiba mais sobre o que está acontecendo com IA do Lote do Azure e a opção de computação do serviço do Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194495"
---
# <a name="whats-happening-to-azure-batch-ai"></a>O que está acontecendo com IA do Lote do Azure?

**O serviço de IA do Lote do Azure será desativado em março.** O treinamento em escala e as funcionalidades de pontuação do IA do Lote agora estão disponíveis no [serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), que foi disponibilizado em 4 de dezembro de 2018.

Junto com muitas outras funcionalidades de aprendizado de máquina, o serviço do Azure Machine Learning inclui um destino de computação gerenciada baseado em nuvem para treinamento, implantação e modelos de aprendizado de máquina de pontuação. Esse destino de computação é chamado [Computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Comece a migrá-lo e usá-lo hoje](#migrate). Você pode interagir com o serviço do Azure Machine Learning por meio de seus [SDKs do Python](../machine-learning/service/quickstart-create-workspace-with-python.md), interface de linha de comando e [portal do Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Linha do tempo de suporte

No momento, você pode usar suas assinaturas existentes da IA do Lote do Azure como antes. No entanto, não é possível ter **novas assinaturas**, e nenhum novo investimento está sendo feito.

A partir de &nbsp;31&#x2c;&nbsp; de março de 2019, as assinaturas não utilizadas de IA do Lote deixarão de funcionar.

## <a name="compare-to-azure-machine-learning"></a>Comparar com o Azure Machine Learning
Ele é um serviço de nuvem usado para treinar, implantar, automatizar e gerenciar modelos de machine learning, tudo na ampla escala oferecida pela nuvem. Obtenha um entendimento de alto nível do [Serviço do Azure Machine Learning nesta visão geral](../machine-learning/service/overview-what-is-azure-ml.md).
 

Um ciclo de vida típico do desenvolvimento de modelo envolve Preparação de Dados, Treinamento e Experimentação, bem como uma fase de Implantação. Esse ciclo completo pode ser orquestrado por meio de pipelines do Machine Learning.

![Fluxograma](./media/overview-what-happened-batch-ai/lifecycle.png)


[Saiba mais sobre como funciona o serviço e seus conceitos principais](../machine-learning/service/concept-azure-machine-learning-architecture.md). Muitos dos conceitos no fluxo de trabalho de treinamento de modelo são semelhantes aos conceitos existentes na IA do Lote. 

Especificamente, este é um mapeamento de como você deve considerá-los:
 
|Serviço da IA do Lote|  Serviço do Azure Machine Learning|
|:--:|:---:|
|Workspace|Workspace|
|HDInsight|   Computação do tipo `AmlCompute`|
|Servidores de arquivos|DataStores|
|Testes|Testes|
|Trabalhos|Execuções (permite execuções aninhadas)|
 
Esta é outra exibição da mesma tabela que ajudará você a visualizar mais detalhes:
 
### <a name="batch-ai-hierarchy"></a>Hierarquia de IA do Lote do Azure
![Fluxograma](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Hierarquia do serviço do Azure Machine Learning
![Fluxograma](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Funcionalidades da plataforma
O Serviço do Azure Machine Learning traz um ótimo conjunto de novas funcionalidades, incluindo um treinamento completo -> pilha de implantação que você pode usar para o desenvolvimento de IA sem precisar gerenciar recursos do Azure. Esta tabela compara o suporte de recurso para treinamento entre os dois serviços.

|Recurso|Serviço da IA do Lote|Serviço do Azure Machine Learning|
|-------|:-------:|:-------:|
|Escolha de tamanho da VM |CPU/GPU    |CPU/GPU. Também dá suporte à FPGA para inferência|
|Cluster pronto para IA (drivers, Docker etc.)|  Sim |Sim|
|Preparação de nós| Sim|    Não |
|Escolha de família do sistema operacional   |Parcial    |Não |
|VMs Dedicadas e de LowPriority  |Sim    |Sim|
|Dimensionamento automático   |Sim    |Sim (por padrão)|
|Tempo de espera para dimensionamento automático  |Não  |Sim|
|SSH    |Sim|   Sim|
|Montagem no nível do cluster |Sim (FileShares, Blobs, NFS, personalizado)   |Sim (montagem ou download do armazenamento de dados)|
|Treinamento distribuído|  Sim |Sim|
|Modo de execução de trabalho|    VM ou contêiner|    Contêiner|
|Imagem de contêiner personalizada|    Sim |Sim|
|Qualquer kit de ferramentas    |Sim    |Sim (execução de script do Python)|
|JobPreparation|    Sim |Ainda não|
|Montagem de nível de trabalho |Sim (FileShares, Blobs, NFS, personalizado)   |Sim (FileShares, Blobs)|
|Monitoramento de trabalho     |por meio de GetJob|    por meio do histórico de execuções (informações mais sofisticadas, tempo de execução personalizado para efetuar push de mais métricas)|
|Recuperação de logs de trabalho e arquivos/modelos |   por meio de ListFiles e das APIs de Armazenamento  |por meio do serviço de Artefato|
 |Suporte para o Tensorboard   |Não |    Sim|
|Cotas no nível da família de VMs |Sim    |Sim (com a capacidade anterior repassada)|
 
Além da tabela anterior, há recursos no Serviço do Azure Machine Learning que tradicionalmente não tinham suporte na BatchAI.

|Recurso|Serviço da IA do Lote|Serviço do Azure Machine Learning|
|-------|:-------:|:-------:|
|Preparação do ambiente    |Não  |Sim (Preparação do Conda e upload no ACR)|
|Ajuste de HyperParameter  |Não |    Sim|
|Gerenciamento de modelos   |Não  |Sim|
|Operacionalização/Implantação| Não   |Por meio do AKS e do ACI|
|Preparação de dados   |Não  |Sim|
|Destinos de computação    |VMs do Azure  |Local, BatchAI (como AmlCompute), DataBricks, HDInsight|
|Automatizado de Machine Learning |Não |    Sim|
|Pipelines  |Não  |Sim|
|Pontuação do lote  |Sim    |Sim|
|Suporte do portal/da CLI|    Sim |Sim|


## <a name="programming-interfaces"></a>Interfaces de programação

Esta tabela apresenta as várias interfaces de programação disponíveis para cada serviço.
    
|Recurso|Serviço da BatchAI|Serviço do Azure Machine Learning|
|-------|:-------:|:-------:|
|.    |Java, C#, Python, Node.js   |Python (baseado na configuração de execução e baseado no avaliador para estruturas comuns)|
|CLI    |Sim    |Ainda não|
|Portal do Azure   |Sim    |Sim (exceto envio do trabalho)|
|API REST   |Sim    |Sim, mas distribuído entre microsserviços|


A atualização do serviço da versão prévia do BatchAI para o Serviço do Azure Machine Learning com GA oferece uma melhor experiência por meio de conceitos que são mais fáceis de usar, como Avaliadores e Armazenamentos de Dados. Ela também garante SLAs do serviço do Azure no nível de GA e atendimento ao cliente.

O Serviço do Azure Machine Learning também traz novas funcionalidades, como o aprendizado de máquina automatizado, ajuste de Hiperparâmetro e pipelines de ML, que são úteis na maioria das cargas de trabalho de IA em larga escala. A capacidade de operar um modelo treinado sem alternar para um serviço separado ajuda a concluir o loop de ciência de dados da preparação de dados (usando o SDK de Preparação de Dados) à operacionalização e ao monitoramento do modelo.

<a name="migrate"></a>
## <a name="migrate"></a>Migrar

Saiba como migrar e como o código que você usa mapeia para o código no serviço de Azure Machine Learning no artigo [Migrar para o serviço de Azure Machine Learning](how-to-migrate.md).

## <a name="get-support"></a>Obtenha suporte

O IA do Lote do Azure está programada para ser desativada em 31 de março e já está bloqueando o registro de novas assinaturas no serviço, a menos que ele seja colocado na lista de permissões pela geração de uma exceção por meio do suporte.  Contate-nos na [Versão Prévia do Treinamento da IA do Lote do Azure](mailto:AzureBatchAITrainingPreview@service.microsoft.com) em caso de dúvidas ou comentários durante a migração para o Serviço do Azure Machine Learning.

O Serviço do Azure Machine Learning é um serviço com disponibilidade geral. Isso significa que ele vem com um SLA comprometido e vários planos de suporte para escolha.

Os preços pelo uso da infraestrutura do Azure por meio do serviço da BatchAI ou por meio do Serviço do Azure Machine Learning não devem variar, pois somente cobramos o preço pela computação subjacente em ambos os casos. Para obter mais informações, confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Exiba a disponibilidade regional entre os dois serviços no [portal do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais [como migrar](how-to-migrate.md) e como o código que você usa agora é mapeado para o código no serviço do Azure Machine Learning.

+ Leia [Visão geral do serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurar um destino de computação para treinamento de modelo](../machine-learning/service/how-to-set-up-training-targets.md) com o serviço do Azure Machine Learning.

+ Examine o [Roteiro do Azure](https://azure.microsoft.com/updates/) para saber mais sobre outras atualizações de serviço do Azure.
