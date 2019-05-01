---
title: Solicitar acesso a um pacote de acesso no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como usar o portal de meu acesso para solicitar acesso a um pacote de acesso no gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541549"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Solicitar acesso a um pacote de acesso no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Entre portal do meu acesso

A primeira etapa é entrar portal do meu acesso em que você pode solicitar acesso a um pacote de acesso.

**Função de pré-requisito:** Solicitante

1. Procure um email ou uma mensagem do Gerenciador de projeto ou de negócios que você está trabalhando com. O email deve incluir um link para o pacote de acesso, que você precisará acessar. O link será iniciado com:

    `https://myaccess.microsoft.com`

1. Abra o link.

1. Entre portal do meu acesso.

    Certifique-se de que você use sua conta institucional. Se você não tiver certeza, verifique com seu gerente de projeto ou de negócios.

## <a name="request-an-access-package"></a>Solicitar um pacote de acesso

Depois de encontrar o pacote de acesso no portal do meu acesso, você pode enviar uma solicitação.

**Função de pré-requisito:** Solicitante

1. Clique na marca de seleção para selecionar o pacote de acesso.

    ![O portal de acesso - pacotes de acesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Clique em **solicitar acesso** para abrir o painel de acesso de solicitação.

1. Se o **justificativa de negócios** caixa é exibida, digite uma justificativa para que precisam de acesso.

1. Se **solicitar por determinado período?** estiver habilitado, selecione **Yes** ou **não**.

1. Se necessário, especifique a data de início e data de término.

    ![Meu portal de acesso – solicitação de acesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Quando terminar, clique em **enviar** para enviar sua solicitação.

1. Clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

    Se o pacote de acesso requer aprovação, a solicitação é agora em um estado de aprovação pendente.

## <a name="cancel-a-request"></a>Cancelar uma solicitação

Se você enviar uma solicitação de acesso e a solicitação ainda está no **aprovação pendente** de estado, você pode cancelar a solicitação.

**Função de pré-requisito:** Solicitante

1. No portal do meu acesso, à esquerda, clique em **histórico de solicitações** para ver uma lista de suas solicitações e o status.

1. Clique o **exibição** link para a solicitação que você deseja cancelar.

1. Se a solicitação ainda está no **aprovação pendente** de estado, você pode clicar em **solicitação de cancelamento** para cancelar a solicitação.

    ![Meu portal de acesso – solicitação de cancelamento](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Clique em **histórico de solicitações** para confirmar a solicitação foi cancelada.

## <a name="select-a-policy"></a>Selecione uma política

Se você está solicitando acesso a um pacote de acesso que tem várias políticas que se aplicam, você pode ser solicitado para selecionar uma política. Por exemplo, um Gerenciador de pacotes de acesso podem ser feitas a um pacote de acesso com duas diretivas para dois grupos de funcionários internos. A primeira política pode permitir o acesso por 60 dias e exigir aprovação. A segunda política pode permitir o acesso por 2 dias e não exigir aprovação. Se você encontrar esse cenário, você deve selecionar a política que você deseja usar.

**Função de pré-requisito:** Solicitante

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar as solicitações de acesso](entitlement-management-request-approve.md)
- [Notificações de email e o processo de solicitação](entitlement-management-process.md)
