---
title: As políticas de segurança da Central de Segurança do Azure podem ser definidas como parte das Políticas do Azure e visualizadas na Central de Segurança | Microsoft Docs
description: Este documento ajuda você a definir políticas no Azure Policy ou exibi-las na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334662"
---
# <a name="view-security-policies"></a>Exibir políticas de segurança

Este artigo explica como as políticas de segurança são configuradas e como exibi-las na Central de Segurança. A Central de Segurança do Azure atribui automaticamente suas [políticas de segurança internas](security-center-policy-definitions.md) a cada assinatura que é incorporada. Você pode configurá-los em [Azure Policy](../azure-policy/azure-policy-introduction.md), que também permite definir políticas em grupos de gerenciamento e em várias assinaturas.

Para instruções sobre como definir políticas usando o PowerShell, confira [Início Rápido: criar uma atribuição de política para identificar recursos sem conformidade usando o módulo do PowerShell do Azure RM](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Quais são políticas de segurança?
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. No Azure Policy, você pode definir políticas para suas assinaturas do Azure e adaptá-las ao seu tipo de carga de trabalho ou à sensibilidade de seus dados. Por exemplo, os aplicativos que usam dados regulamentados, como as informações de identificação pessoal, podem exigir um nível mais alto de segurança do que outras cargas de trabalho. Para definir uma política em assinaturas ou em Grupos de gerenciamento, faça-o no [Azure Policy](../azure-policy/azure-policy-introduction.md).



Suas políticas de segurança direcionam as recomendações de segurança que você recebe na Central de Segurança do Azure. Você pode monitorar a conformidade com eles para ajudá-lo a identificar possíveis vulnerabilidades e atenuar ameaças. Para obter mais informações sobre como determinar a opção apropriada para você, consulte a lista de [políticas de segurança internas](security-center-policy-definitions.md).

### <a name="management-groups"></a>Grupos de gerenciamento
Se sua organização tiver muitas assinaturas, talvez seja necessária uma maneira de gerenciar com eficiência o acesso, as políticas e a conformidade dessas assinaturas. Os Grupos de Gerenciamento do Azure fornecem um nível de escopo sobre assinaturas. Você organiza assinaturas em contêineres chamados “grupos de gerenciamento” e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento “raiz”. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo de gerenciamento raiz permite que políticas globais e atribuições de RBAC sejam aplicados no nível de diretório. Para configurar grupos de gerenciamento para uso com a Central de Segurança do Azure, siga as instruções em [Obtenha visibilidade de todo o locatário da Central de Segurança do Azure](security-center-management-groups.md).

> [!NOTE]
> É importante compreender a hierarquia dos grupos de gerenciamento e assinaturas. Consulte [Organizar seus recursos com Grupos de Gerenciamento do Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) para saber mais sobre grupos de gerenciamento, gerenciamento raiz e acesso ao grupo de gerenciamento.
>

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar as políticas no Azure Policy para fazer o seguinte:
- Criar definições de políticas.
- Atribuir as políticas entre grupos de gerenciamento e assinaturas, que podem representar uma organização inteira ou uma unidade de negócios dentro da organização.
- Monitorar a conformidade da política.

Para obter mais informações sobre o Azure Policy, confira [Criar e gerenciar políticas para impor a conformidade](../azure-policy/create-manage-policy.md).

Uma política do Azure consiste nos seguintes componentes:

- Um **diretiva** é uma regra.
- Uma **iniciativa** é uma coleção de políticas.
- Uma **designação** é a aplicação de uma iniciativa ou política para um escopo específico (grupo de gerenciamento, assinatura ou grupo de recursos).

Um recurso é avaliado em relação às políticas atribuídas a ele e recebe uma taxa de conformidade de acordo com o número de políticas com as quais o recurso tem conformidade.

## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários são atribuídos com a função de proprietário, colaborador ou leitor para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- Leitor de segurança: tem direitos de exibição na Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não pode fazer alterações.
- Administrador de segurança: têm os mesmos direitos de exibição que o leitor de segurança e também podem atualizar a política de segurança e ignorar recomendações e alertas.

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma das suas assinaturas e grupos de gerenciamento do Azure em [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Para modificar uma política de segurança, você deve ser proprietário, colaborador ou administrador de segurança da assinatura ou do grupo de gerenciamento que a contém.

Para obter instruções sobre como editar uma política de segurança na Azure Policy, consulte e [Crie e gerencie políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Exibir políticas de segurança

Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Security Center**, selecione **Security policy**.

    ![O painel Gerenciamento de Política](./media/security-center-policies/security-center-policy-mgt.png)

  Na tela **Gerenciamento de políticas**, você pode ver o número de grupos de gerenciamento, assinaturas e áreas de trabalho, bem como a estrutura do grupo de gerenciamento.

  > [!NOTE]
  > - O painel da Central de Segurança pode mostrar um número maior de inscrições em **Cobertura de assinatura** do que o número de inscrições mostrado em **Gerenciamento de políticas**. Cobertura de assinatura mostra o número de assinaturas Padrão, Gratuitas e “não cobertas”. As assinaturas "não cobertas" não têm o Security Center ativado e não são exibidas em **Gerenciamento de políticas**.
  >

  As colunas na tabela são exibidas:

 - **Atribuição de iniciativa de política** - Central de Segurança [políticas internas](security-center-policy-definitions.md) e iniciativas atribuídas a uma assinatura ou grupo de gerenciamento.
 - **Conformidade** - Pontuação geral de conformidade para um grupo de gerenciamento, assinatura ou espaço de trabalho. A pontuação é a média ponderada das atribuições. Os fatores de média ponderada para o número de políticas em uma única atribuição e o número de recursos a que se aplica a atribuição.

 Por exemplo, se sua assinatura tiver duas VMs e uma iniciativa com cinco políticas atribuídas a ela, você tem 10 avaliações em sua assinatura. Se uma das VMs não tiver conformidade com duas das políticas, a pontuação de conformidade geral da atribuição da assinatura é 80%.

 - **Cobertura** - identifica a camada de preços, Free ou Standard, na qual o grupo de gerenciamento, a assinatura ou a área de trabalho está sendo executada.  Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
 - **Configurações** - As assinaturas têm o link **Editar configurações**. Ao selecionar **Editar configurações**, você pode atualizar suas [Configurações da Central de Segurança](security-center-policies-overview.md) para cada assinatura ou grupo de gerenciamento.

2. Selecione a assinatura ou o grupo de gerenciamento cujas políticas você deseja visualizar.

  - A tela **Política de segurança** reflete a ação tomada pelas políticas atribuídas na assinatura ou no grupo de gerenciamento selecionado.
  - Na parte superior, use os links fornecidos para abrir cada **atribuição** de política aplicável à assinatura ou ao grupo de gerenciamento. Você pode usar os links para acessar a atribuição e editar ou desativar a política. Por exemplo, se você perceber que determinada atribuição de política está efetivamente negando a proteção do ponto de extremidade, poderá usar o link para acessar a política e editá-la ou desativá-la.
  - Na lista de políticas, você pode ver a aplicação efetiva da política em sua assinatura ou grupo de gerenciamento. Isso significa que as configurações de cada política que se aplicam ao escopo são levadas em consideração e você recebe o resultado cumulativo de qual ação é tomada pela política. Por exemplo, se em uma atribuição a política estiver desabilitada, mas em outra ela estiver definida como AuditIfNotExist, o efeito cumulativo será aplicado a AuditIfNotExist. O efeito mais ativo sempre tem precedência.
  - O efeito das políticas pode ser: Anexar, Auditar, AuditIfNotExists, Negar, DeployIfNotExists, Desativado. Para obter mais informações sobre como os efeitos são aplicados, consulte [Reconhecer os efeitos da política](../governance/policy/concepts/effects.md).

   ![tela de política](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Ao visualizar as políticas atribuídas, você pode ver várias atribuições e pode ver como cada atribuição é configurada por conta própria.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de operações e planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Obter visibilidade de todo o locatário para a Central de Segurança do Azure](security-center-management-groups.md): saiba como configurar grupos de gerenciamento para a Central de Segurança do Azure.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as respostas para as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.

Para saber mais sobre o Azure Policy, confira [O que é o Azure Policy?](../azure-policy/azure-policy-introduction.md)
