---
title: Validação de pilha do Azure como um conceitos principais de serviço | Microsoft Docs
description: Descreve os principais conceitos na validação de pilha do Azure como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: d5b0c15fe5e4740a06af5de8a47b79c6d7528a2c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243807"
---
# <a name="validation-as-a-service-key-concepts"></a>Validação como um conceitos principais do serviço

Este artigo descreve os principais conceitos em validação como um serviço (VaaS).

## <a name="solutions"></a>Soluções

Uma solução VaaS representa uma solução do Azure Stack com uma lista de hardware específicas de materiais (BoM). A solução VaaS atua como um contêiner para os fluxos de trabalho que são executados em relação a solução do Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Criar uma solução no portal do VaaS

1. Entrar para o [VaaS portal](https://azurestackvalidation.com).
2. No painel de soluções, selecione na **nova solução**.
3. Insira um nome para a solução. Para obter sugestões, consulte [convenção de nomenclatura para soluções de VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Selecione **salvar** para criar a solução.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho VaaS opera dentro do contexto de uma solução VaaS. Ele representa um conjunto de conjuntos de testes que exercitam a funcionalidade de uma implantação do Azure Stack. Um fluxo de trabalho deve ser criado para cada atualização de software ou implantação de uma solução do Azure Stack.

Fluxos de trabalho são categorizados por tipo de cenário de teste. Em testes não oficial, o **aprovação do teste** fluxo de trabalho permite que você selecione os testes de todos os materiais VaaS disponíveis. Em testes oficial, o **validação** fluxos de trabalho destinam a cenários específicos de testes selecionados pela Microsoft.

![Blocos de fluxo de trabalho VaaS](media/tile_all-workflows.png)

> [!NOTE]
> O **validação de solução** fluxo de trabalho atualmente dá suporte a dois cenários: [Validar pacotes de OEM](azure-stack-vaas-validate-oem-package.md) e [validar as atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Para obter mais informações sobre tipos de fluxo de trabalho, consulte [o que é validação como um serviço para o Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Guia de Introdução com fluxos de trabalho VaaS

1. No painel de soluções, criar uma nova solução ou selecione um existente. Isso atualiza e permite que os blocos de fluxo de trabalho.
2. Para criar um novo fluxo de trabalho, selecione na **iniciar** em qualquer bloco. Para obter informações específicas para cada fluxo de trabalho, consulte os seguintes artigos:
    - Aprovação do teste: [Início Rápido: Usar a validação como um portal de serviço para agendar seu primeiro teste](azure-stack-vaas-schedule-test-pass.md)
    - Validação da solução: [Validar uma nova solução do Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Validação da solução: [Validar as atualizações de software da Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Validação da solução: [Validar pacotes de OEM](azure-stack-vaas-validate-oem-package.md)

3. Para gerenciar ou monitorar o fluxo de trabalho existente, selecione na **gerenciar** no bloco de fluxo de trabalho. Selecione o nome do fluxo de trabalho e usar o **editar** botão para exibir as propriedades ou alterar os parâmetros comuns de teste.

Para obter mais informações sobre propriedades de fluxo de trabalho e os parâmetros, consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testes

Um teste no VaaS consiste em um conjunto de ações que são executadas em relação a uma solução do Azure Stack. Testes têm diferentes finalidades pretendidas identificadas por uma categoria, como funcional ou a confiabilidade e um ou mais serviços do Azure Stack de destino. Cada teste define seu próprio conjunto de parâmetros, algumas das quais são especificadas pelos parâmetros comuns do fluxo de trabalho que contém.

Para obter mais informações sobre como gerenciar e testes de monitoramento, consulte [monitorar e gerenciar testes no portal do VaaS](azure-stack-vaas-monitor-test.md).

Para obter mais informações sobre parâmetros de teste, consulte [parâmetros comuns de fluxo de trabalho para validação de pilha do Azure como um serviço](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agentes

Um agente VaaS unidades de execução de teste. Dois tipos de agentes para executar testes de VaaS:

- O **agente de nuvem**. Isso é o agente de padrão disponível no VaaS. Nenhuma configuração é necessária, mas isso requer conectividade de entrada para seu ambiente e pontos de extremidade do Azure Stack deverá ser resolvidos na internet. Alguns testes não são compatíveis com o agente de nuvem.
- Um **agente local**. Isso permite que você para executar a validação em cenários em que a conectividade de entrada para seu ambiente não é viável. Alguns testes exigem a execução por meio do agente local.

Os agentes locais não estão vinculados a qualquer solução do Azure Stack ou VaaS específica. Como prática recomendada, ele deve ser executado fora de um ambiente do Azure Stack.

Para obter instruções sobre como adicionar um agente local, consulte [implantar o agente local](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Próximas etapas

- [Práticas recomendadas para a validação como um serviço](azure-stack-vaas-best-practice.md)