---
title: "Expansão de funções de trabalho nos serviços de aplicativo - pilha do Azure | Microsoft Docs"
description: "Instruções detalhadas para dimensionar os serviços de aplicativos de pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: 30ab325488684a26a6ef442e7c8241526a66aa4c
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Serviço de aplicativo na pilha do Azure: adicionar mais funções de infraestrutura ou de trabalho

Este documento fornece instruções sobre como dimensionar o serviço de aplicativo em funções de infraestrutura e de trabalho da pilha do Azure. Ele contém as etapas para a criação de funções de trabalho adicional para dar suporte a aplicativos de qualquer tamanho.

> [!NOTE]
> Se seu ambiente de pilha do Azure não tem mais de 96 GB de RAM, você pode ter dificuldade para adicionar capacidade adicional.

Serviço de aplicativo na pilha do Azure, por padrão, dá suporte a camadas de trabalhador gratuito e compartilhado. Para adicionar outros níveis de trabalho, você precisa adicionar mais funções de trabalho.

Se não tiver certeza de que foi implantado com o serviço de aplicativo padrão na instalação da pilha do Azure, você pode examinar informações adicionais de [do serviço de aplicativo na visão geral do Azure pilha](azure-stack-app-service-overview.md).

Serviço de aplicativo do Azure na pilha do Azure implanta todas as funções usando conjuntos de escala de máquina Virtual e como tal tira proveito dos recursos de dimensionamento dessa carga de trabalho. Portanto, todo o dimensionamento das camadas de trabalhador é feito por meio do administrador do serviço de aplicativo.

Adicionar operadores adicionais diretamente no provedor de administrador do aplicativo serviço recursos.

1. Faça logon portal de administração do Azure pilha como o administrador de serviço.

2. Navegue até **serviços de aplicativos**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)
  
3. Clique em **Funções**. Veja aqui a análise de todas as funções de serviço de aplicativo implantado.

4. Clique com o botão direito na linha do tipo que você deseja dimensionar e, em seguida, clique em **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)
  
5. Clique em **Scaling**, selecione o número de instâncias que você deseja dimensionar para e, em seguida, clique em **salvar**.
    
    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Serviço de aplicativo na pilha do Azure agora será adicionar outras VMs, configurá-los, instalar todos os softwares necessários e marcá-los como pronto quando esse processo é concluído. Esse processo pode levar aproximadamente 80 minutos.

7. Você pode monitorar o progresso da preparação das novas funções, exibindo os trabalhadores no **funções** folha.

Depois que eles são totalmente implantado e estiver pronto, os trabalhadores ficam disponíveis para os usuários implantar suas cargas de trabalho para eles. O exemplo a seguir mostra um exemplo de várias camadas de preços disponíveis por padrão. Se não houver nenhum operadores disponíveis para uma camada de trabalhador específico, a opção de escolher a camada de preços correspondente não está disponível.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para expandir gerenciamento, funções de Front-End ou publicador adicionam que você deve expandir o conjunto de escalas de VM correspondente. Vamos adicionar a capacidade de expandir essas funções por meio do gerenciamento de serviço de aplicativo em uma versão futura.

Para expandir gerenciamento, Front-End ou publicador funções, siga as mesmas etapas que selecionando o tipo de função apropriada. Controladores não são implantados como conjuntos de escala e, portanto, duas devem ser implantadas no momento da instalação para todas as implantações de produção.

### <a name="next-steps"></a>Próximas etapas

[Configurar fontes de implantação](azure-stack-app-service-configure-deployment-sources.md)
