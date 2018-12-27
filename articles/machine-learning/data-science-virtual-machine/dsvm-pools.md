---
title: Pools de Máquinas Virtuais de Ciência de Dados – Azure | Microsoft Docs
description: Implantando pools de VMs de Ciência de Dados como um recurso compartilhado para uma equipe
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: gokuma
ms.openlocfilehash: 648b0818f07aca8763fa4af01380076ae307b0a2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408940"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um pool compartilhado de Máquinas Virtuais de Ciência de Dados

Este artigo discute como é possível criar um pool compartilhado de DSVMs (Máquinas Virtuais de Ciência de Dados) para uma equipe usar. Os benefícios do uso de um pool compartilhado são a melhor utilização de recursos, a facilitação do compartilhamento e da colaboração e o gerenciamento mais eficiente dos recursos de DSVM. 

Você pode usar vários métodos e tecnologias para criar um pool de DSVMs. Este artigo enfoca os pools para VMs interativas. Uma infraestrutura de computação gerenciada alternativa é a Computação do Azure Machine Learning. Consulte [Configurar destinos de computação](../service/how-to-set-up-training-targets.md#amlcompute) para obter mais informações.

## <a name="interactive-vm-pool"></a>Pool VM interativo

Um pool de VMs interativas compartilhadas por toda a equipe de IA/ciência de dados permite que os usuários façam logon em uma instância disponível da DSVM em vez de ter uma instância dedicada para cada conjunto de usuários. Essa configuração ajuda com uma melhor disponibilidade e uma utilização mais eficiente de recursos. 

A tecnologia que você usa para criar um pool de VMs interativo é os [Conjuntos de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Você pode usar os conjuntos de dimensionamento para criar e gerenciar um grupo de VMs idênticas, com balanceamento de carga e dimensionamento automático. 

O usuário faz logon no endereço IP ou DNS do pool principal. O conjunto de dimensionamento roteia automaticamente a sessão para uma DSVM disponível no conjunto de dimensionamento. Como os usuários desejam um ambiente semelhante, independentemente da VM a qual estão conectados, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada, como um compartilhamento de Arquivos do Azure ou um compartilhamento NFS. O workspace compartilhado do usuário normalmente é mantido no armazenamento de arquivos compartilhado montado em cada uma das instâncias. 

Encontre uma amostra de modelo do Azure Resource Manager que cria um conjunto de dimensionamento com instâncias de DSVM do Ubuntu no [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Há um exemplo do [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) do modelo do Azure Resource Manager no mesmo local. 

Você pode criar o conjunto de dimensionamento por meio do modelo do Azure Resource Manager especificando valores para o arquivo de parâmetro usando a CLI do Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Os comandos anteriores consideram que você tenha:
* Uma cópia do arquivo de parâmetro com os valores especificados para a instância do conjunto de dimensionamento.
* O número de instâncias de VM.
* Ponteiros para o compartilhamento de Arquivos do Azure.
* Credenciais da conta de armazenamento que será montada em cada VM. 

O arquivo de parâmetro é referenciado localmente nos comandos. Você também pode passar parâmetros embutidos ou solicitá-los no script.  

O modelo anterior habilita o SSH e a porta do JupyterHub do conjunto de dimensionamento de front-end para o pool de back-end de DSVMs do Ubuntu. Como usuário, basta fazer logon na VM no SSH ou no JupyterHub da maneira normal. Como as instâncias de VM podem ser escaladas ou reduzidas verticalmente de modo dinâmico, todos os estados precisam ser salvos no compartilhamento de Arquivos do Azure montados. A mesma abordagem pode ser usada para criar um pool de DSVMs do Windows. 

O [script que monta o compartilhamento de Arquivos do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no GitHub. O script monta o compartilhamento de Arquivos do Azure no ponto de montagem especificado no arquivo de parâmetro. O script também cria links flexíveis com a unidade montada no diretório base do usuário inicial. Um diretório de Notebook específico do usuário no compartilhamento de Arquivos do Azure é vinculado de forma flexível ao diretório `$HOME/notebooks/remote` para que os usuários possam acessar, executar e salvar seus Jupyter Notebooks. A mesma convenção pode ser usada ao criar usuários adicionais na VM para apontar o workspace do Jupyter de cada usuário para o compartilhamento de Arquivos do Azure. 

Os Conjuntos de Dimensionamento de Máquinas Virtuais são compatíveis com o dimensionamento automático. Você pode definir regras de quando criar instâncias adicionais e reduzir instâncias verticalmente. Por exemplo, você pode reduzir verticalmente até zero instâncias para economizar em custos de uso de hardware de nuvem quando as VMs não estão sendo usadas. As páginas de documentação dos Conjuntos de Dimensionamento de Máquinas Virtuais fornecem as etapas detalhadas para realizar o [dimensionamento automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma identidade comum](dsvm-common-identity.md)
* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)















