---
title: Pools de Máquinas Virtuais de Ciência de Dados – Azure | Microsoft Docs
description: Implantando pools de VMs de Ciência de Dados como um recurso compartilhado para uma equipe
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309391"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Criar um pool compartilhado de Máquinas Virtuais de Ciência de Dados

Este artigo discute como é possível criar um pool compartilhado de DSVMs (Máquinas Virtuais de Ciência de Dados) para uma equipe usar. Os benefícios do uso de um pool compartilhado são a melhor utilização de recursos, a facilitação do compartilhamento e da colaboração e o gerenciamento mais eficiente dos recursos de DSVM. 

Você pode usar vários métodos e tecnologias para criar um pool de DSVMs. Este artigo concentra-se nos pools para processamento em lotes e VMs interativas.

## <a name="batch-processing-pool"></a>Pool de processamento em lotes
Se você quiser configurar um pool de DSVMs principalmente para executar trabalhos em um lote offline, use o serviço [IA do Lote do Azure](https://docs.microsoft.com/azure/batch-ai/) ou [Lote do Azure](https://docs.microsoft.com/azure/batch/). Este artigo concentra-se na IA do Lote do Azure.

A edição do Ubuntu de DSVM tem suporte como uma das imagens de IA do Lote do Azure. Na CLI do Azure ou no SDK do Python em que você criou o cluster de IA do Lote do Azure, é possível especificar o parâmetro `image` e defini-lo como `UbuntuDSVM`. É possível escolher o tipo de nós de processamento desejado: instâncias baseadas em GPU ou instâncias apenas de CPU, número de CPUs e memória de uma [ampla variedade de instâncias de VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponíveis no Azure. 

Quando você usa a imagem de DSVM do Ubuntu na IA do Lote com nós baseados em GPU, todos os drivers de GPU e as estruturas de aprendizado profundo necessários já estão pré-instalados. A pré-instalação economiza um tempo considerável ao preparar os nós de lote. Na verdade, se estiver desenvolvendo em uma DSVM do Ubuntu de forma interativa, você notará que os nós de IA do Lote têm exatamente as mesmas instalação e configuração do ambiente. 

Normalmente quando você cria um cluster de IA do Lote, também é criado um compartilhamento de arquivos que é montado por todos os nós. O compartilhamento de arquivos é usado para entrada e saída de dados, e também para o armazenamento de códigos/scripts de trabalho em lotes. 

Depois de criar um cluster de IA do Lote, você pode usar a mesma CLI ou o mesmo SDK do Python para enviar trabalhos para serem executados. Você paga apenas pelo tempo usado para executar os trabalhos em lotes. 

Para obter mais informações, consulte:
* Passo a passo de como usar a [CLI do Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) para gerenciar a IA do Lote
* Passo a passo de como usar o [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) para gerenciar a IA do Lote
* [Receitas de IA do Lote](https://github.com/Azure/BatchAI) que demonstram como usar as diversas estruturas de IA e de aprendizado profundo com a IA do Lote

## <a name="interactive-vm-pool"></a>Pool VM interativo

Um pool de VMs interativas compartilhadas por toda a equipe de IA/ciência de dados permite que os usuários façam logon em uma instância disponível da DSVM em vez de ter uma instância dedicada para cada conjunto de usuários. Essa configuração ajuda com uma melhor disponibilidade e uma utilização mais eficiente de recursos. 

A tecnologia que você usa para criar um pool de VMs interativo é os [Conjuntos de Dimensionamento de Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Você pode usar os conjuntos de dimensionamento para criar e gerenciar um grupo de VMs idênticas, com balanceamento de carga e dimensionamento automático. 

O usuário faz logon no endereço IP ou DNS do pool principal. O conjunto de dimensionamento roteia automaticamente a sessão para uma DSVM disponível no conjunto de dimensionamento. Como os usuários desejam um ambiente semelhante, independentemente da VM a qual estão conectados, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada, como um compartilhamento de Arquivos do Azure ou um compartilhamento NFS. O espaço de trabalho compartilhado do usuário normalmente é mantido no armazenamento de arquivos compartilhado montado em cada uma das instâncias. 

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

O [script que monta o compartilhamento de Arquivos do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no repositório DataScienceVM do Azure no GitHub. O script monta o compartilhamento de Arquivos do Azure no ponto de montagem especificado no arquivo de parâmetro. O script também cria links flexíveis com a unidade montada no diretório base do usuário inicial. Um diretório de Notebook específico do usuário no compartilhamento de Arquivos do Azure é vinculado de forma flexível ao diretório `$HOME/notebooks/remote` para que os usuários possam acessar, executar e salvar seus Jupyter Notebooks. A mesma convenção pode ser usada ao criar usuários adicionais na VM para apontar o espaço de trabalho do Jupyter de cada usuário para o compartilhamento de Arquivos do Azure. 

Os Conjuntos de Dimensionamento de Máquinas Virtuais são compatíveis com o dimensionamento automático. Você pode definir regras de quando criar instâncias adicionais e reduzir instâncias verticalmente. Por exemplo, você pode reduzir verticalmente até zero instâncias para economizar em custos de uso de hardware de nuvem quando as VMs não estão sendo usadas. As páginas de documentação dos Conjuntos de Dimensionamento de Máquinas Virtuais fornecem as etapas detalhadas para realizar o [dimensionamento automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Próximas etapas

* [Configurar uma identidade comum](dsvm-common-identity.md)
* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)















