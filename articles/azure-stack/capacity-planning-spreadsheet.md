---
title: Planilha de planejamento para o Azure Stack de capacidade | Microsoft Docs
description: Saiba mais sobre a planilha para implantações do Azure Stack de planejamento de capacidade.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: a37575e6e6e8902dc2dd89a26011031c2e1419c4
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557250"
---
# <a name="azure-stack-capacity-planner"></a>Planejador de capacidade do Azure Stack
O Planejador de capacidade do Azure Stack é uma planilha usada para planejamento de capacidade de recursos do Azure Stack. O Planejador de capacidade fornece a capacidade de criar várias alocações de recursos de computação e ver como eles seriam se através de uma seleção de ofertas de hardware. Instruções detalhadas para o uso da Calculadora do Azure Stack são fornecidas abaixo.

## <a name="worksheet-descriptions"></a>Descrições de planilha
A seguir está um breve resumo das planilhas contidas na planilha do Planejador de capacidade de pilha do Azure que pode ser baixada em [ http://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Nome da guia|DESCRIÇÃO|
|-----|-----|
|Isenção de responsabilidade de versão|Visão geral da finalidade da Calculadora, o número de versão e data de lançamento.|
|Instruções|Fornece instruções detalhadas sobre o uso do Planejador de capacidade de pilha do Azure.|
|DefinedSolutionSKUs|Tabela de várias colunas que contém definições de hardware até cinco. As entradas nesta folha são exemplos. A intenção é que o usuário será alterado de detalhes para corresponder as configurações do sistema que está sendo consideradas para uso ou de compra.|
|DefineByVMFootprint|Encontre o SKU de hardware apropriado, criando uma coleção de vários tamanhos e as quantidades de VMs.|
|DefineByWorkloadFootprint|Encontre o SKU de hardware apropriado, criando uma coleção de cargas de trabalho do Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Instruções de DefinedSolutionSKUs
Esta planilha contém até cinco exemplos de definições de hardware. Alterar detalhes para corresponder as configurações do sistema que está sendo consideradas para uso ou de compra.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Seleções de hardware fornecidas pelos parceiros de Hardware autorizado
O Azure Stack é entregue como um sistema integrado com software instalado por parceiros de solução. Esses parceiros de solução terão suas próprias, versões autoritativos do Azure Stack, ferramentas e as ferramentas de planejamento de capacidade deve ser usada para finalizar a discussões de capacidade de solução.

### <a name="multiple-ways-to-model-computing-resources"></a>Várias maneiras de modelar recursos de computação
Blocos de construção básicos usados para o recurso de modelagem dentro do Planejador do Azure Stack estão os vários tamanhos de máquinas de virtuais do Azure Stack (VMs). Essas configurações variam de tamanho do menor, VMs "Básica 0", até a VM maior atual, "Standard_Fsv2". Com base em suas necessidades, várias quantidades de VMs diferentes podem ser usadas para criar as alocações de recursos de computação usando essa ferramenta de duas maneiras diferentes.

1. O usuário do planejador seleciona uma oferta de hardware específico e vê que combinações de vários recursos se ajustam a esse recurso de hardware. 

2. O usuário do planejador cria uma combinação específica de alocações de VM e, em seguida, permite que mostra a Calculadora de recursos do Azure que SKUs de hardware disponíveis são capazes de dar suporte a essa configuração de VM.

Essa ferramenta fornece dois métodos para alocar recursos VM; ou como uma única coleção de alocações de recursos VM ou como uma coleção de até seis diferentes configurações de carga de trabalho. Cada configuração de carga de trabalho pode conter uma alocação diferente dos recursos VM disponíveis. Informações detalhadas para criar e usar cada um desses modelos de alocação são fornecidas abaixo. Somente os valores contidos no plano de fundo não sombreado células ou em listas suspensas SKU nesta planilha devem ser modificados. As alterações feitas em células sombreadas podem quebrar cálculos do recurso.


## <a name="definebyvmfootprint-instructions"></a>Instruções de DefineByVMFootprint
Para criar um modelo usando uma única coleção de vários tamanhos e as quantidades de VMs, selecione a guia "DefineByVMFootprint" e siga essa sequência de etapas.

1. No canto superior direito desta planilha, use os controles de caixa de lista fornecida de suspenso para selecionar um número inicial de servidores (entre 4 e 16) que deseja instalar em cada sistema de hardware (SKU). Esse número de servidores pode ser modificado a qualquer momento durante o processo de modelagem para ver como isso afeta geral recursos disponíveis para o seu modelo de alocação de recursos.
2. Se você quiser modelar várias alocações de recursos VM em relação a uma configuração de hardware específico, localize a caixa de listagem azul pull diretamente abaixo do rótulo de "SKU atual" no canto superior direito da página. Puxe para baixo nessa caixa de listagem e selecione o SKU de hardware desejado.
3. Agora você está pronto para começar a adicionar várias VMs de tamanhos para seu modelo. Para incluir um tipo específico de VM, digite um valor de quantidade para a caixa contornada azul à esquerda da entrada da VM.

  > [!NOTE]
  > Total de armazenamento de VM refere-se com a capacidade total do disco de dados da VM (número de discos com suporte * a capacidade máxima de um único disco (1 TB)). Com base nos indicadores de configuração do preenchemos a tabela de configurações de armazenamento disponíveis, para que você pode escolher o nível desejado de recurso de armazenamento para cada VM do Azure Stack. No entanto, é importante observar que você pode adicionar ou alterar a tabela de configurações de armazenamento disponíveis, conforme necessário.<br><br>Cada VM começa com um armazenamento temporário local de inicialmente atribuído. Para refletir o provisionamento dinâmico de armazenamento temporário o número de temp local pode ser alterado a qualquer coisa na lista suspensa menu incluindo o valor máximo permitido de armazenamento temporário.

4. Conforme você adiciona máquinas virtuais, você verá os gráficos que mostram os recursos SKU disponíveis a alteração. Isso permite que você veja os efeitos de adição de vários tamanhos e as quantidades de VMs durante o processo de modelagem. Outra maneira de exibir o efeito das alterações é observar os números de consumida e ainda estão disponíveis listados diretamente abaixo da lista de VMs disponíveis. Esses números refletem os valores estimados com base no hardware SKU selecionado no momento.
5. Depois de criar seu conjunto de VMs, você pode encontrar o SKU de hardware sugeridas, clicando no botão "SKU sugerido" encontrado no canto superior direito da página, diretamente abaixo do rótulo de "SKU atual". Com esse botão, em seguida, você pode modificar suas configurações de VM e ver qual hardware dá suporte a cada configuração.


## <a name="definebyworkloadfootprint-instructions"></a>Instruções de DefineByWorkloadFootprint
Para criar um modelo usando uma coleção de cargas de trabalho do Azure Stack, selecione a guia "DefineByWorkloadFootprint" e siga essa sequência de etapas. Cargas de trabalho do Azure Stack são criadas usando os recursos VM disponíveis.   

> [!TIP]
> Para alterar o tamanho de armazenamento fornecido para uma VM de pilha do Azure, consulte a observação na etapa três na seção anterior.

1. No canto superior direito desta página, use os controles de caixa de lista fornecida de suspenso para selecionar um número inicial de servidores (entre 4 e 16) que deseja instalar em cada sistema de hardware (SKU).
2. Se você quiser modelar várias alocações de recursos VM em relação a uma configuração de hardware específico, localize a caixa de listagem azul pull diretamente abaixo do rótulo de "SKU atual" no canto superior direito da página. Puxe para baixo nessa caixa de listagem e selecione o SKU de hardware desejado.
3. Selecione o tamanho de armazenamento apropriado para cada uma das suas VMs de pilha do Azure desejado na página DefineByVMFootprint conforme descrito anteriormente na etapa três da DefineByVMFootprint instruções. O tamanho do armazenamento por VM é definido na planilha de DefineByVMFootprint.
4. Iniciando no canto superior esquerdo da página DefineByWorkloadFootprint, crie configurações para até seis diferentes tipos de carga de trabalho, inserindo a quantidade de cada tipo VM contido dentro dessa carga de trabalho. Isso é feito colocando a valores numéricos na coluna diretamente abaixo do nome da carga de trabalho. Nomes de carga de trabalho podem ser modificados para refletir o tipo de cargas de trabalho que será compatível com essa configuração específica.
5. Você pode incluir uma quantidade específica de cada tipo de carga de trabalho, inserindo um valor na parte inferior da coluna diretamente abaixo do rótulo de "Quantidade".
6. Depois que as quantidades e tipos de carga de trabalho foram criadas, clicando em "SKU sugerido" botão encontrado no canto superior direito da página, diretamente abaixo do rótulo de "SKU atual", fará com que a SKU menor com recursos suficientes para dar suporte a essa geral configuração de cargas de trabalho a ser exibido.
7. Ainda mais a modelagem pode ser feito modificando o número de servidores selecionados para uma SKU, hardware ou alterar as alocações de VM ou quantidades dentro de suas configurações de carga de trabalho. Gráficos associados serão exibido mostrando como as alterações afetam o consumo geral de recursos de comentários imediatos.
8. Quando estiver satisfeito com suas alterações, clicando no botão "SKU sugerido" novamente exibirá o SKU sugerido para a nova configuração.


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [considerações de integração do datacenter para o Azure Stack](azure-stack-datacenter-integration.md)
