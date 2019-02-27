---
title: Gerenciamento de região no Azure Stack | Microsoft Docs
description: Visão geral do gerenciamento de região no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 2845bb09fa44c3bd9cee914e2446403b4182b0d5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871036"
---
# <a name="region-management-in-azure-stack"></a>Gerenciamento de região no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pilha do Azure usa o conceito de *regiões*, que são entidades lógicas compostas por recursos de hardware que compõem a infraestrutura do Azure Stack. No gerenciamento de região, você pode encontrar todos os recursos que são necessários para operar com êxito a infraestrutura do Azure Stack.

Uma delas integrada a implantação de sistema (conhecido como um *nuvem do Azure Stack*) compõe uma única região. Cada Kit de desenvolvimento do Azure Stack tem uma região, denominada **local**. Se você implantar um segundo sistema integrado do Azure Stack ou se você configurar a outra instância do kit de desenvolvimento em um hardware separado, essa nuvem do Azure Stack é uma região diferente.

## <a name="information-available-through-the-region-management-tile"></a>Informações disponíveis por meio do bloco de gerenciamento de região

O Azure Stack tem um conjunto de recursos de gerenciamento de região disponíveis na **gerenciamento de região** lado a lado. Esse bloco está disponível para um operador do Azure Stack no painel no portal do administrador padrão. Por meio de lado a lado, você pode monitorar e atualizar sua região do Azure Stack e seus componentes, que são específicas da região.

![O bloco de gerenciamento de região](media/azure-stack-region-management/image1.png)

Se você clicar em uma região na **gerenciamento de região** lado a lado, você pode acessar as informações a seguir:

[![Descrição dos painéis na folha de gerenciamento de região](media/azure-stack-region-management/regionssm.png "folha de gerenciamento de região")](media/azure-stack-region-management/regions.png#lightbox)

1. **Menu de recursos**. Acessar áreas de gerenciamento de infraestrutura específicos e exibir e gerenciar recursos do usuário como contas de armazenamento e redes virtuais.

2. **Alertas**. Lista de alertas de todo o sistema e fornece detalhes sobre cada um desses alertas.

3. **Atualizações**. Exiba a versão atual de sua infraestrutura do Azure Stack, as atualizações disponíveis e o histórico de atualização. Você também pode atualizar seu sistema integrado.

4. **Provedores de recursos**. Gerencie a funcionalidade de usuário oferecida pelos componentes necessários para executar o Azure Stack. Cada provedor de recursos é fornecido com uma experiência administrativa. Essa experiência pode incluir alertas para o provedor específico, métricas e outros recursos de gerenciamento específicos para o provedor de recursos.

5. **Funções de infraestrutura**. Os componentes necessários para executar o Azure Stack. Somente as funções de infraestrutura que relatam os alertas são listadas. Selecionando uma função, você pode exibir os alertas associados com a função e as instâncias de função em que essa função está em execução.

6. **Propriedades**. O status do registro e os detalhes do seu ambiente na folha de gerenciamento de região. O status pode ser **registrado** ou **não registrado**. Se registrado, ele também mostra a ID da assinatura do Azure que é usado para registrar seu Azure Stack, juntamente com o grupo de recursos de registro e o nome.

## <a name="next-steps"></a>Próximas etapas

- [Monitorar integridade e alertas no Azure Stack](azure-stack-monitor-health.md)
- [Gerenciar atualizações no Azure Stack](azure-stack-updates.md)
