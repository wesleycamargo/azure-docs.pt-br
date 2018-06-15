---
title: Pools de máquina virtual de ciência de dados – Azure | Microsoft Docs
description: Pools de implantação de VM de ciência de dados como um recurso compartilhado para a equipe
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
ms.openlocfilehash: c7aab0435ecbd0aee57a15008ac0270159ec2eb3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837075"
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Criando um pool compartilhado de máquinas de virtuais de ciência de dados

Este artigo discute como um pool compartilhado de DSVM (máquinas virtuais de ciência de dados) pode ser criado para ser usado pela equipe. A vantagem de usar um pool compartilhado é a melhor utilização de recursos, facilitando o compartilhamento e a colaboração, além de permitir que o departamento de TI gerencie os recursos de DSVM com mais eficiência. 

Há muitas maneiras e tecnologias diferentes que podem ser usadas para criar um pool de DSVM.  Estes são os cenários principais:

* Pool para processamento em lotes
* Pool para VMs interativas

## <a name="batch-processing-pool"></a>Pool de processamento em lotes
Caso deseje configurar um pool de DSVM para, principalmente, executar trabalhos em um lote offline, você pode usar o serviço de [IA do Lote do Azure](https://docs.microsoft.com/azure/batch-ai/) ou do [Lote do Azure](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>IA do Lote do Azure
A edição do Ubuntu de DSVM tem suporte como uma das imagens de IA do Lote do Azure. Na CLI do Azure ou o SDK de Python onde você criou o cluster de IA do Lote do Azure, é possível especificar o parâmetro ```image``` e defini-lo como ```UbuntuDSVM```. É possível escolher o tipo de nós de processamento desejado – instâncias baseadas em GPU vs apenas instâncias de CPU, quantidade de CPUs, memória de uma [ampla escolha de instâncias de VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) disponíveis no Azure. Ao usar a imagem de DSVM do Ubuntu em IA de Lote com nós baseados em GPU, todos os drivers GPU necessários e estruturas de aprendizado estão pré-instalados, economizando um tempo considerável de preparação dos nós de lote. Na verdade, se você estiver desenvolvendo em DSVM do Ubuntu interativamente, notará que os nós de IA do Lote são exatamente a mesma configuração e a configuração do ambiente. Normalmente, quando um cluster de IA do Lote é criado, também é criado um compartilhamento de arquivo que é montado por todos os nós e é usado para a entrada e saída de dados, bem como para o armazenamento do código de trabalho em lotes/scripts. 

Depois de criar o cluster de IA do Lote, você pode usar a mesma CLI ou SDK do Python para enviar trabalhos para serem executados. Você só paga pela hora usada para executar os trabalhos em lotes. 

#### <a name="more-information"></a>Mais informações
* Passo a passo sobre como usar a [CLI do Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) para gerenciar o IA do Lote
* Passo a passo sobre como usar o [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) para gerenciar o IA do Lote
* Há [receitas do IA do Lote](https://github.com/Azure/BatchAI) disponíveis para demonstrar como usar as diversas estruturas de IA/aprendizado com o IA do Lote.

## <a name="interactive-vm-pool"></a>Pool VM interativo

Um pool de DSVMs interativas compartilhadas por toda a IA/equipe de ciência de dados permite que os usuários façam logon uma instância disponível do DSVM em vez de ter uma instância dedicada para cada conjunto de usuários. Isso ajuda ao oferecer melhor disponibilidade e utilização mais eficiente de recursos. 

A tecnologia usada para criar um pool de VM interativo é a de [VMSS (Conjuntos de Dimensionamento de Máquinas Virtuais) do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/), a qual permite criar e gerenciar um grupo de VMs idênticas, com balanceamento de carga e dimensionamento automático. O usuário faz logon no endereço IP ou DNS do pool principal. O conjunto de dimensionamento roteia automaticamente a sessão para uma DSVM disponível no conjunto de dimensionamento. Como o usuário desejaria um ambiente semelhante independentemente da VM na qual está fazendo logon, todas as instâncias da VM no conjunto de dimensionamento montam uma unidade de rede compartilhada como arquivos do Azure ou um compartilhamento de NFS. O espaço de trabalho compartilhado do usuário normalmente é mantido no armazenamento de arquivo compartilhado que está montado em cada uma das instâncias. 

Pode ser encontrado no [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) um exemplo de modelo do Azure Resource Manager que cria um conjunto de dimensionamento de VM com instâncias de DSVMs do Ubuntu. No mesmo local, também é fornecido um exemplo do [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) do modelo do Azure Resource Manager. 

Você pode criar o conjunto de dimensionamento da VM do modelo do Azure Resource Manager especificando valores adequados para o arquivo de parâmetro usando a CLI do Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Os comandos anteriores pressupõem que você tenha uma cópia do arquivo de parâmetro com os valores especificados para a instância do conjunto de dimensionamento da VM, a quantidade de instâncias de VM, ponteiros para os arquivos do Azure, juntamente com as credenciais da conta de armazenamento que será montada em cada VM. O arquivo de parâmetro é referenciado localmente no comando acima. Você também pode passar parâmetros embutido ou solicitá-los em seu script.  

O modelo acima habilita o SSH e a porta do JupyterHub do conjunto de dimensionamento da VM de front-end para o pool de back-end de DSVMs do Ubuntu.  Como usuário, basta você fazer logon na VM no SSH ou JupyterHub da maneira normal. Uma vez que as instâncias de VM podem ser escaladas ou reduzidas verticalmente de modo dinâmico, todos os estados precisam ser salvos no compartilhamento montado de arquivos do Azure. A mesma abordagem pode ser usada para criar um pool de DSVMs do Windows. 

O [script que monta os arquivos do Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) também está disponível no Github DataScienceVM do Azure. Além de montar os arquivos do Azure no ponto de montagem especificado no arquivo de parâmetro, ele também cria ligações simbólicas adicionais para a unidade montada no diretório base do usuário inicial. Além disso, é feita a ligação simbólica de um diretório do bloco de anotações específico do usuário dentro dos arquivos compartilhados do Azure ao diretório ```$HOME/notebooks/remote```, habilitando um usuário a acessar, executar e salvar seus blocos de anotações do Jupyter.  A mesma convenção pode ser usada ao criar usuários adicionais na VM para apontar para o espaço de trabalho de Jupyter de cada usuário para os arquivos compartilhados do Azure. 

Os conjuntos de dimensionamento de VM do Azure oferecem suporte ao dimensionamento automático, em que você pode definir regras sobre quando criar instâncias adicionais e sob quais circunstâncias reduzir instâncias verticalmente, incluindo a redução de instâncias até zero para economizar os custos de uso de hardware de nuvem quando as VMs não estão sendo usadas. As páginas de documentação dos conjuntos de dimensionamento de VM fornecem etapas detalhadas para o [dimensionamento automático](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Próximas etapas

* [Configurar a identidade comum](dsvm-common-identity.md)
* [Armazenar com segurança as credenciais para acessar recursos de nuvem](dsvm-secure-access-keys.md)















