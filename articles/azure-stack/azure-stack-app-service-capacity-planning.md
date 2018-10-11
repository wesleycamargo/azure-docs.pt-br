---
title: Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure no Azure Stack | Microsoft Docs
description: Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078106"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para configurar uma implantação de produção pronta do serviço de aplicativo do Azure no Azure Stack, você deve planejar a capacidade que você espera que o sistema para dar suporte.  

Este artigo fornece orientação para o número mínimo de instâncias de computação e de computação SKUs, você deve usar para qualquer implantação de produção.

Você pode planejar sua estratégia de capacidade do serviço de aplicativo usando essas diretrizes. Versões futuras do Azure Stack fornecerá opções de alta disponibilidade para serviço de aplicativo.

| Função de servidor do serviço de aplicativo | Mínimo recomendado de número de instâncias | SKU de computação recomendada|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-End | 2 | A1 |
| Gerenciamento | 2 | A3 |
| Publicador | 2 | A1 |
| Web Workers - compartilhados | 2 | A1 |
| Web Workers - dedicados | 2 por camada | A1 |

## <a name="controller-role"></a>Função do controlador

**Mínimo recomendado**: duas instâncias do Standard A1

O controlador de serviço de aplicativo do Azure geralmente apresenta baixo de consumo da CPU, memória e recursos de rede. No entanto, para alta disponibilidade, você deve ter dois controladores. Dois controladores também são o número máximo de controladores permitidos. Você pode criar o segundo controlador de Sites da Web direto do instalador durante a implantação.

## <a name="front-end-role"></a>Função de Front-End

**Mínimo recomendado**: duas instâncias do Standard A1

O Front-End encaminha solicitações para Web Workers dependendo da disponibilidade do Web Worker. Para alta disponibilidade, você deve ter mais de um Front-End e pode ter mais de dois. Para fins de planejamento de capacidade, considere que cada núcleo pode lidar com aproximadamente 100 solicitações por segundo.

## <a name="management-role"></a>Função de gerenciamento

**Mínimo recomendado**: duas instâncias do Standard A3

A função de gerenciamento do serviço de aplicativo do Azure é responsável por pontos de extremidade de API e o Gerenciador de recursos do aplicativo de serviço do Azure, portal extensões (administrador, locatário, o portal de funções) e o serviço de dados. A função de servidor de gerenciamento normalmente requer trata apenas de 4 GB de RAM em um ambiente de produção. No entanto, ele pode ter altos níveis de CPU quando muitas tarefas de gerenciamento (como a criação de site da web) são executadas. Para alta disponibilidade, você deve ter mais de um servidor atribuído a essa função e, pelo menos dois núcleos pelo servidor.

## <a name="publisher-role"></a>Função publicador

**Mínimo recomendado**: duas instâncias do Standard A1

Se vários usuários estiverem publicando simultaneamente, a função publicador pode enfrentar uso intenso da CPU. Para alta disponibilidade, disponibilize mais de uma função do publicador.  O publicador trata apenas do tráfego FTP/FTPS.

## <a name="web-worker-role"></a>Função Web worker

**Mínimo recomendado**: duas instâncias do Standard A1

Para alta disponibilidade, você deve ter pelo menos quatro funções Web Worker, duas para modo de site compartilhado e dois para cada camada de trabalho dedicada que pretende oferecer. Os modos de computação dedicados e compartilhados fornecem diferentes níveis de serviço para locatários. Talvez seja necessário mais Web Workers, se muitos dos seus clientes são:

- Usando camadas de trabalhador de modo de computação dedicados (que são de uso intensivo de recursos).
- Em execução no modo de computação compartilhada.

Depois que um usuário tiver criado um plano de serviço de aplicativo para um modo de computação dedicados SKU, o número de Web Workers especificados em que o plano do serviço de aplicativo não estarão disponível para os usuários.

Para fornecer funções do Azure para usuários no modelo de plano de consumo, você deve implantar Shared Web Workers.

Ao decidir sobre o número de funções de Web Worker compartilhadas para usar, examine essas considerações:

- **Memória**: memória é o recurso mais crítico para uma função Web Worker. Memória insuficiente afeta o desempenho do site quando a memória virtual é alternada de disco. Cada servidor requer aproximadamente 1,2 GB de RAM para o sistema operacional. RAM acima desse limite pode ser usado para executar sites da web.
- **Porcentagem de sites ativos**: normalmente, cerca de 5 por cento dos aplicativos em um serviço de aplicativo do Azure na implantação do Azure Stack estão ativos. No entanto, a porcentagem de aplicativos que estão ativos em qualquer momento determinado pode ser maior ou menor. Com uma taxa de aplicativo do Active Directory de 5 por cento, o número máximo de aplicativos para colocar em um serviço de aplicativo do Azure na implantação do Azure Stack deve ser menor que:
  - 20 vezes o número de sites ativos (20 x 5 = 100).
- **Superfície média de memória**: A superfície média de memória para aplicativos observada em ambientes de produção é de aproximadamente 70 MB. Usando esta superfície, a memória alocada em todos os computadores com função Web Worker ou VMs pode ser calculada da seguinte maneira:

    *Número de aplicativos provisionado * 70 MB * 5% - (número de funções Web Worker * 1044 MB)*

   Por exemplo, se houver 5.000 aplicativos no ambiente que está executando 10 funções Web Worker, cada máquina virtual da função Web Worker deve ter 7060 MB de RAM:

   5.000 * 70 * 0,05 – (10 * 1044) = 7060 (= cerca de 7 GB)

   Para obter informações sobre como adicionar mais instâncias de trabalho, consulte [adicionando mais funções de trabalho](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Função de servidor de arquivos

Para a função de servidor de arquivos, você pode usar um servidor de arquivos autônomo para desenvolvimento e teste, por exemplo, ao implantar o serviço de aplicativo do Azure no Kit de desenvolvimento do Azure Stack pode usar este modelo - <https://aka.ms/appsvconmasdkfstemplate>. Para fins de produção, você deve usar um servidor de arquivos do Windows pré-configurado ou um servidor de arquivos de não Windows pré-configurado.

Em ambientes de produção, a função de servidor de arquivos apresenta e/s de disco intensiva. Como ela abriga todos os arquivos de conteúdo e de aplicativos para sites de usuário, você deve pré-configurar um destes procedimentos para esta função:

- um servidor de arquivos do Windows
- um Cluster de servidores de arquivos do Windows
- um servidor de arquivos não-Windows
- um cluster de servidor de arquivos de não-Windows
- um dispositivo NAS (Network Attached Storage)

Para obter mais informações, consulte [provisionar um servidor de arquivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Próximas etapas

[Antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md)
