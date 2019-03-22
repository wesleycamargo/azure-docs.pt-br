---
title: Crie um grupo dinâmico e verifique o status - Azure Active Directory | Microsoft Docs
description: Como criar uma regra de associação de grupo no portal do Azure, verifique o status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199674"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Criar um grupo dinâmico e verificar o status

No Azure Active Directory (Azure AD), você pode usar regras para determinar a associação de grupo com base nas propriedades do usuário ou dispositivo. Este artigo informa como configurar uma regra para um grupo dinâmico no portal do Azure.
Associação dinâmica tem suporte para grupos de segurança ou grupos do Office 365. Quando uma regra de associação de grupo é aplicada, os atributos de usuário e dispositivo são avaliados para correspondências com a regra de associação. Quando um atributo é alterado para um dispositivo ou usuário, todas as regras de grupo dinâmico na organização serão processadas para alterações de associação. Usuários e dispositivos são adicionados ou removidos se eles atendem às condições para um grupo.

Para obter exemplos de sintaxe, propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Entrar para o [Centro de administração do Azure AD](https://aad.portal.azure.com) com uma conta que está em como o Administrador Global, administrador do Intune ou função de administrador do usuário no locatário.
2. Selecione **Grupos**.
3. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Selecione o comando para adicionar um novo grupo](./media/groups-create-rule/new-group-creation.png)

4. Sobre o **grupo** página, insira um nome e uma descrição para o novo grupo. Selecione uma **tipo de associação** para a usuários ou dispositivos e selecione **adicionar consulta dinâmica**. Você pode usar o construtor de regra para criar uma regra simples, ou [escreve uma regra de associação](groups-dynamic-membership.md).

   ![Adicionar regra de associação para um grupo dinâmico](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Para ver as propriedades de extensão personalizado disponíveis para a sua consulta de associação
   1. Selecione **obter propriedades de extensão personalizada**
   2. Insira a ID do aplicativo e, em seguida, selecione **Refresh propriedades**. 
6. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da folha.
7. Selecione **Criar** on the **Grupo** para criar o grupo.

Se a regra que você inseriu não é válida, uma explicação de por que a regra não pôde ser processada é exibida no canto superior direito do portal. Leia com atenção para entender como corrigir a regra.

## <a name="turn-on-or-off-welcome-email"></a>Ativar ou desativar o email de boas-vinda

Quando é criado um novo grupo do Office 365, uma notificação de boas-vinda é enviada os usuários que são adicionados ao grupo. Posteriormente, se os atributos de um usuário ou dispositivo são alterados, todas as regras de grupo dinâmico na organização são processadas para alterações de associação. Os usuários que são adicionados, em seguida, também receberem a notificação de boas-vinda. Você pode desativar esse comportamento na [do Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Verificar o status de processamento para uma regra

Veja o status do processamento de associação e a data da última atualização na página **Visão Geral** do grupo.
  
  ![exibição do status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

* **Avaliando**:  a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
* **Processando**: as atualizações estão sendo processadas.
* **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
* **Erro de processamento**:  Não foi possível concluir o processamento devido a um erro ao avaliar a regra de associação.
* **Atualização em pausa**: as atualizações dinâmicas da regra de associação foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

* &lt;**Data e hora**&gt;: a última vez em que a associação foi atualizada.
* **Em andamento**: as atualizações estão em andamento no momento.
* **Desconhecido**: Não é possível recuperar a hora da última atualização. O grupo pode ser novo.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada de nenhum dos grupos do locatário por mais de 24 horas, um alerta será mostrado na parte superior da **Todos os grupos**.

![alertas de mensagem de erro de processamento](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

* [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
