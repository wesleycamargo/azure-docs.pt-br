---
title: Adicionar usuários individualmente licenciados para licenciamento baseado em grupo - Azure Active Directory | Microsoft Docs
description: Como migrar licenças de usuário individuais para licenciamento baseado em grupo usando o Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192448"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Como migrar usuários com licenças individuais a grupos para licenciamento

É possível implantar licenças existentes em usuários nas organizações por meio da “atribuição direta”; ou seja, usando scripts do PowerShell ou outras ferramentas para atribuir licenças de usuários individuais. Antes de começar a usar o licenciamento baseado em grupo para gerenciar licenças em sua organização, você pode usar este plano de migração para substituir perfeitamente as soluções existentes com licenciamento baseado em grupo.

A coisa mais importante para ter em mente é que você deve evitar uma situação onde a migração para o licenciamento baseado em grupo resultará em usuários temporariamente perdendo suas licenças atribuídas. Deve-se evitar qualquer processo que pode resultar na remoção de licenças, a fim de remover o risco de os usuários perderem o acesso aos serviços e a seus dados.

## <a name="recommended-migration-process"></a>Processo de migração recomendado

1. Você tem a automação existente (por exemplo, PowerShell) gerenciando a atribuição e a remoção de licenças para usuários. Deixe-o em execução como está.

2. Crie um novo grupo de licenciamento (ou decida quais grupos existentes serão usados) e verifique se todos os usuários necessários foram adicionados como membros.

3. Atribua as licenças necessárias para esses grupos; sua meta deve ser refletir o estado de licenciamento mesmo que sua automação existente (por exemplo, PowerShell) esteja sendo aplicada aos usuários.

4. Verifique se as licenças foram aplicadas a todos os usuários nesses grupos. Esta aplicação pode ser feita verificando o estado de processamento em cada grupo e conferindo os logs de auditoria.

   - Você pode identificar usuários individuais ao examinar os detalhes de suas licenças. Você verá que eles têm as licenças atribuídas "diretamente" e "herdadas" de grupos.

   - Você pode executar um script do PowerShell para [verificar como as licenças são atribuídas a usuários](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Quando a mesma licença do produto é atribuída para o usuário diretamente e por meio de um grupo, apenas uma licença é consumida pelo usuário. Portanto, não há nenhuma licença adicional é necessária para realizar uma migração.

5. Verifique se nenhuma atribuição de licença falhou ao verificar cada grupo de usuários em estado de erro. Para saber mais, veja [Identificar e resolver problemas de licença para um grupo](licensing-groups-resolve-problems.md).

6. Considere remover as atribuições diretas originais; talvez você deseje fazer isso gradualmente, em “ondas”, para monitorar o resultado em um subconjunto de usuários primeiro.

   É possível deixar as atribuições diretas originais nos usuários, mas quando os usuários deixarem os respectivos grupos licenciados ainda manterão a licença original, o que provavelmente não é o que você quer.

## <a name="an-example"></a>Um exemplo

Uma organização tem 1.000 usuários. Todos os usuários exigem licenças EMS (Enterprise Mobility + Security). 200 usuários estão no departamento de finanças e exigem licenças do Office 365 Enterprise E3. Atualmente, a organização tem um script do PowerShell em execução local, adicionando e removendo licenças de usuários à medida que elas entram e saem. No entanto, a organização deseja substituir o script pelo licenciamento baseado em grupo para que as licenças possam ser gerenciadas automaticamente pelo Azure AD.

Veja como deve se parecer o processo de migração:

1. Usando o portal do Azure, atribua a licença do EMS ao grupo **Todos os usuários** no Azure AD. Atribua a licença E3 ao grupo **departamento financeiro** que contém todos os usuários exigidos.

2. Para cada grupo, confirme que a atribuição de licença foi concluída para todos os usuários. Vá até a folha de cada grupo, selecione **Licenças** e verifique o status de processamento na parte superior da folha **Licenças**.

   - Procure "As alterações mais recentes de licença foram aplicadas a todos os usuários" para confirmar que o processamento foi concluído.

   - Procure uma notificação na parte superior sobre quaisquer usuários para os quais as licenças talvez não tenham sido atribuídas com êxito. Nós não temos mais licenças para alguns usuários? Alguns usuários têm SKUs com licenças conflitantes que os impedem de herdar as licenças de grupo?

3. Verifique alguns usuários para ver se eles têm as licenças diretas ou de grupo aplicadas. Vá até a folha de um usuário, selecione **Licenças** e examine o estado das licenças.

   - Este é o estado do usuário esperado durante a migração:

      ![o estado do usuário esperado durante a migração](./media/licensing-groups-migrate-users/expected-user-state.png)

   Isso confirma que o usuário tem licenças diretas e herdadas. Podemos ver que **EMS** e **E3** estão atribuídas.

   - Selecione cada licença para mostrar os detalhes sobre os serviços habilitados. Isso pode ser usado para verificar se as licenças diretas e de grupo permitem exatamente os mesmos planos de serviço para o usuário.

      ![Verifique os planos de serviço para o usuário](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Depois de confirmar que as licenças direta e de grupo são equivalentes, você poderá começar a remover as licenças diretas dos usuários. É possível testar isso removendo-as para usuários individuais no portal e, em seguida, executar scripts de automação para removê-las em massa. Este é um exemplo do mesmo usuário com as licenças diretas removidas por meio do portal. Observe que o estado da licença permanece inalterado, mas não vemos mais as atribuições diretas.

   ![Confirme que as licenças diretas são removidas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licença por meio de grupos, leia

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](licensing-groups-assign.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
