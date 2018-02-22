---
title: "Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure na pilha do Azure | Microsoft Docs"
description: "Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure na pilha do Azure"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planejamento de capacidade para funções de servidor do serviço de aplicativo do Azure na pilha do Azure
*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Para provisionar uma implantação de produção pronto de serviço de aplicativo do Azure na pilha do Azure, você deve planejar a capacidade que você espera que o sistema ofereça suporte.  Aqui está a orientação sobre o número mínimo de instâncias e computação SKUs, você deve usar para qualquer implantação de produção.

Você pode planejar sua estratégia de capacidade do serviço de aplicativo usando essas diretrizes. Versões futuras da pilha do Azure fornecerá opções de alta disponibilidade para serviço de aplicativo.

| Função de servidor do serviço de aplicativo | Mínimo recomendado de número de instâncias | Computação recomendada SKU|
| --- | --- | --- |
| Controller | 2 | A1 |
| Front-End | 2 | A1 |
| Gerenciamento | 2 | A3 |
| Publicador | 2 | A1 |
| Web Workers - compartilhados | 2 | A1 |
| Web Workers - dedicados | 2 por camada | A1 |

## <a name="controller-role"></a>Função do controlador

**Mínimo recomendado**: duas instâncias de A1 padrão

O controlador de serviço de aplicativo do Azure geralmente apresenta baixo de consumo de CPU, memória e recursos de rede. No entanto, para alta disponibilidade, você deve ter dois controladores. Dois controladores também são o número máximo de controladores permitidos. Você pode criar o segundo controlador de Sites da Web direto do installer durante a implantação.

## <a name="front-end-role"></a>Função de Front-End

**Mínimo recomendado**: duas instâncias de A1 padrão

O Front-End encaminha solicitações para Web Workers dependendo da disponibilidade do Web Worker. Para alta disponibilidade, você deve ter mais de um Front-End e pode ter mais de dois. Para fins de planejamento de capacidade, considere que cada principal pode ter aproximadamente 100 solicitações por segundo.

## <a name="management-role"></a>Função de gerenciamento

**Mínimo recomendado**: duas instâncias de A3

A função de gerenciamento de serviço de aplicativo do Azure é responsável pelo aplicativo de serviço do Azure Resource Manager e pontos de extremidade de API, extensões portais (administrador, locatário, portal de funções) e o serviço de dados. A função de servidor de gerenciamento normalmente requer somente sobre 4 GB de RAM em um ambiente de produção. No entanto, ela pode apresentar níveis altos de consumo da CPU quando muitas tarefas de gerenciamento (como a criação de site da web) são executadas. Para alta disponibilidade, você deve ter mais de um servidor atribuído a essa função e, pelo menos dois núcleos pelo servidor.

## <a name="publisher-role"></a>Função de editor

**Mínimo recomendado**: duas instâncias de A1

Se vários usuários estiverem publicando simultaneamente, a função publicador poderá apresentar grande utilização da CPU. Para alta disponibilidade, disponibilize mais de uma função do publicador.  O publicador só trata do tráfego FTP/FTPS.

## <a name="web-worker-role"></a>Função Web worker

**Mínimo recomendado**: duas instâncias de A1

Para alta disponibilidade, você deve ter pelo menos quatro funções Web Worker, duas para o modo de site compartilhado e duas para cada camada de trabalhador dedicado que pretende oferecer. Os modos de computação dedicado e compartilhado fornecem níveis diferentes de serviço para locatários. Se você tiver muitos clientes, talvez seja necessário mais Web Workers:
 - usar camadas de trabalhador de modo de computação dedicado (que fazem uso intensivo de recursos)
 - em execução no modo de computação compartilhada.

Depois que um usuário tenha criado um plano de serviço de aplicativo para um modo de computação dedicado SKU, o número de Web Workers especificado em que o plano de serviço de aplicativo não estará disponível para os usuários.

Para fornecer funções do Azure para usuários no modelo de plano de consumo, você deve implantar compartilhado Web Workers.

Ao decidir sobre o número de funções de Web Worker compartilhadas para usar, examine essas considerações:

- **Memória**: memória é o recurso mais importante para a função Web Worker. Memória insuficiente afeta o desempenho do site quando a memória virtual é alternada de disco. Cada servidor requer aproximadamente 1,2 GB de RAM para o sistema operacional. RAM acima desse limite pode ser usado para executar sites.
- **Porcentagem de sites ativos**: normalmente, cerca de 5 por cento dos aplicativos em um serviço de aplicativo do Azure na implantação da pilha do Azure estão ativos. No entanto, a porcentagem de aplicativos que estão ativos a qualquer momento pode ser maior ou menor. Com uma taxa de aplicativo ativa de 5 por cento, o número máximo de aplicativos para colocar em um serviço de aplicativo do Azure na implantação do Azure pilha deve ser menor que:
    - 20 vezes o número de sites ativos (5 x 20 = 100).
- **Superfície média de memória**: A superfície média de memória para aplicativos observada em ambientes de produção é de aproximadamente 70 MB. Portanto, a memória alocada em todos os computadores com função Web Worker ou máquinas virtuais pode ser calculada da seguinte maneira:

    *Número de aplicativos provisionado * 70 MB * 5% - (número de funções Web Worker * 1044 MB)*

   Por exemplo, se houver 5.000 aplicativos no ambiente que está executando 10 funções Web Worker, cada máquina virtual da função Web Worker deve ter 7060 MB de RAM:

   5.000 * 70 * 0,05 – (10 * 1044) = 7060 (= cerca de 7 GB)

   Para obter informações sobre como adicionar mais instâncias de trabalho, consulte [adicionando mais funções de trabalho](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Função de servidor de arquivos

Para a função de servidor de arquivos, você pode usar um servidor de arquivos autônomo para desenvolvimento e teste, por exemplo, ao implantar o serviço de aplicativo do Azure no Kit de desenvolvimento de pilha do Azure você pode usar esse modelo - https://aka.ms/appsvconmasdkfstemplate. Para fins de produção, você deve usar um servidor de arquivos pré-configurado do Windows ou um servidor de arquivos não Windows pré-configurado.

Em ambientes de produção, a função de servidor de arquivos apresenta e/s intensiva de disco. Como ela abriga todo os conteúdo e arquivos de aplicativo para sites de usuário, você deve pré-configurar um destes procedimentos para esta função:
- um servidor de arquivos do Windows
- Cluster de servidor de arquivos
- um servidor de arquivos não-Windows
- cluster de servidor de arquivos
- Dispositivo NAS (Network Attached Storage) para obter mais informações, consulte [provisionar um servidor de arquivos](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Próximas etapas

[Antes de iniciar o serviço de aplicativo na pilha do Azure](azure-stack-app-service-before-you-get-started.md)
