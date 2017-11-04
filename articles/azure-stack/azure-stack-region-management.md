---
title: "Gerenciamento de região na pilha do Azure | Microsoft Docs"
description: "Visão geral do gerenciamento de região na pilha do Azure."
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Gerenciamento de região na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Azure tem o conceito de regiões, que são entidades lógicas abrange os recursos de hardware que compõem a infra-estrutura de pilha do Azure. Em gerenciamento de região, você pode localizar todos os recursos que são necessárias para operar com êxito o ciclo de vida de infraestrutura de pilha do Azure.

Um integrado de implantação de sistema (conhecido como um *nuvem Azure pilha*) compõe uma única região. Cada Kit de desenvolvimento de pilha do Azure tem uma região, denominada **local**. Se você implantar um sistema de pilha do Azure integradas segundo ou configurar outra instância do kit de desenvolvimento em hardware separado, esta nuvem de pilha do Azure é uma região diferente.

## <a name="information-available-through-the-region-management-tile"></a>Informações disponíveis por meio do bloco de gerenciamento de região
A pilha do Azure tem um conjunto de recursos de gerenciamento de região disponíveis no **gerenciamento região** lado a lado. Este bloco está disponível para um operador de pilha do Azure usando o painel padrão no portal do administrador. Por esse bloco, você pode monitorar e atualizar sua região de pilha do Azure e seus componentes, que são específicas da região.

 ![O bloco de gerenciamento de região](media/azure-stack-manage-region/image1.png)

 Se você clicar em uma região no bloco de gerenciamento de região, você pode acessar as informações a seguir:

  ![Descrição dos painéis na folha de gerenciamento de região](media/azure-stack-manage-region/image2.png)

1. **O menu de recurso**. Aqui, você pode acessar as áreas do gerenciamento de infraestrutura específicos e exibir e gerenciar recursos do usuário como contas de armazenamento e redes virtuais.

2. **Alertas**. Este bloco lista alertas de todo o sistema e fornece detalhes sobre cada um desses alertas.

3. **Atualizações**. No lado a lado, você pode exibir a versão atual de sua infraestrutura de pilha do Azure.

4. **Provedores de recursos**. Provedores de recursos é o local para gerenciar a funcionalidade de locatário oferecida pelos componentes necessários para executar a pilha do Azure. Cada provedor de recursos é fornecido com uma experiência administrativa. Essa experiência pode incluir alertas para o provedor específico, métricas e outros recursos de gerenciamento específicos para o provedor de recursos.
 
5. **Funções de infraestrutura**. Funções de infraestrutura são os componentes necessários para executar a pilha do Azure. Somente as funções de infraestrutura que relatam alertas são listadas. Ao clicar em uma função, você pode exibir os alertas associados à função específica e as instâncias de função em que essa função está em execução. Embora não haja a capacidade de iniciar, reiniciar, ou desligar uma instância de função de infraestrutura, faça **não** isso em um ambiente do kit de desenvolvimento. Essas opções são criadas somente para um ambiente com vários nó, onde há mais de uma instância de função por função de infraestrutura. Reiniciar uma instância de função (especialmente AzS-Xrp01) no kit de desenvolvimento provoca instabilidade no sistema.

## <a name="next-steps"></a>Próximas etapas
[Monitorar a integridade e alertas na pilha do Azure](azure-stack-monitor-health.md)

[Gerenciar atualizações na pilha do Azure](azure-stack-updates.md)






