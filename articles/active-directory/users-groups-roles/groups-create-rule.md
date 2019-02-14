---
title: Crie um grupo dinâmico e verifique o status - Azure Active Directory | Microsoft Docs
description: Como criar uma associação de grupo de regras no portal do Azure, verifique o status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7e283127fa9effe04f59f31c836f471020c9dacc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506935"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Criar um grupo dinâmico e verificar o status

No Microsoft Azure Active Directory (Azure AD), você pode criar grupos, aplicando uma regra para determinar a associação com base nas propriedades do usuário ou dispositivo. Quando os atributos de um usuário ou dispositivo muda, o Microsoft Azure Active Directory avalia o grupo dinâmico todos os regras no locatário do AD do Azure e executa qualquer adiciona ou remove. Se um usuário ou dispositivo atender a uma regra para um grupo, ele será adicionado como um membro e quando elas não atendem à regra, eles são removidos.

Este artigo fornece detalhes sobre como configurar uma regra no portal do Azure para a associação dinâmica em grupos de segurança ou grupos do Office 365. Para obter exemplos de sintaxe de regra e uma lista completa de propriedades com suporte, operadores e valores para uma regra de associação, consulte [regras de associação dinâmica para grupos no Microsoft Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para criar uma regra de associação de grupo

1. Entre no [Centro de Administração do Microsoft Azure AD](https://aad.portal.azure.com) com uma conta que tenha uma função de Administrador global, Administrador de serviços do Intune ou Administrador da conta do usuário no locatário.
2. Selecione **Grupos**.
3. Selecione **Todos os grupos** e selecione **Novo grupo**.

   ![Adicione o novo grupo](./media/groups-create-rule/new-group-creation.png)

4. Na folha **Grupo** , insira um nome e uma descrição para o novo grupo. Selecione o **Tipo de associação** **Usuário Dinâmico** ou **Dispositivo Dinâmico**, dependendo se você deseja criar uma regra para usuários ou dispositivos e, em seguida, selecione **Adicionar consulta dinâmica**. Você pode usar o construtor de regra para criar uma regra simples ou gravar uma regra de associação por conta própria. Este artigo contém mais informações sobre os atributos de usuário e dispositivo disponíveis, bem como exemplos de regras avançadas.

   ![Adicionar regra de associação dinâmica](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Para ver a lista completa de propriedades de extensão personalizadas que você pode adicionar à sua consulta de associação, selecione **Obter propriedades de extensão personalizada**, insira a ID do aplicativo e, em seguida, selecione **Atualizar propriedades**. A lista de completa das propriedades agora estará disponível para seleção.
6. Depois de criar a regra, selecione **Adicionar consulta** na parte inferior da folha.
7. Selecione **Criar** on the **Grupo** para criar o grupo.

> [!TIP]
> A criação de grupo falha quando a regra inserida é formada incorretamente ou não é válida. Uma notificação é exibida no canto superior direito do portal, contendo uma explicação de por que a regra não pôde ser processada. Leia com cuidado para entender como você precisa ajustar a regra para torná-la válida.

## <a name="check-processing-status-for-a-membership-rule"></a>Verificar o status de processamento para uma regra de associação

Veja o status do processamento de associação e a data da última atualização na página **Visão Geral** do grupo.
  
  ![exibição de status do grupo dinâmico](./media/groups-create-rule/group-status.png)

As seguintes mensagens de status podem ser mostradas para o status **Processamento de associação**:

* **Avaliando**:  a alteração do grupo foi recebida e as atualizações estão sendo avaliadas.
* **Processando**: as atualizações estão sendo processadas.
* **Atualização concluída**: o processamento foi concluído e todas as atualizações aplicáveis foram feitas.
* **Erro de processamento**: ocorreu um erro ao avaliar a regra de associação e o processamento não pôde ser concluído.
* **Atualização em pausa**: as atualizações dinâmicas da regra de associação foram pausadas pelo administrador. MembershipRuleProcessingState é definido como "Em pausa".

As seguintes mensagens de status podem ser mostradas para o status da **Última atualização da associação**:

* &lt;**Data e hora**&gt;: a última vez em que a associação foi atualizada.
* **Em andamento**: as atualizações estão em andamento no momento.
* **Desconhecido**: não é possível recuperar a hora da última atualização. O motivo pode ser que o grupo foi criado recentemente.

Se ocorrer um erro ao processar a regra de associação de um grupo específico, um alerta será mostrado na parte superior da **página Visão Geral** do grupo. Se nenhuma atualização de associação dinâmica pendente puder ser processada de nenhum dos grupos do locatário por mais de 24 horas, um alerta será mostrado na parte superior da **Todos os grupos**.

![mensagem de erro de processamento](./media/groups-create-rule/processing-error.png)

Esses artigos fornecem mais informações sobre grupos no Azure Active Directory.

* [Consultar grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionando membros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gerenciar configurações de um grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gerenciar associações de um grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](groups-dynamic-membership.md)
