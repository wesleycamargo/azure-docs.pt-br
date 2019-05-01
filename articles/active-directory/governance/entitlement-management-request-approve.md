---
title: Aprovar ou negar solicitações de acesso no gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Saiba como usar o portal de acesso Meus para aprovar ou negar as solicitações para um pacote de acesso no gerenciamento de direitos do Active Directory do Azure (visualização).
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541519"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Aprovar ou negar solicitações de acesso no gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com o gerenciamento de direitos do AD do Azure, você pode configurar políticas para solicitar aprovação para pacotes de acesso e escolha um ou mais aprovadores. Este artigo descreve como designados aprovadores pode aprovar ou negar solicitações de pacotes de acesso.

## <a name="open-request"></a>Solicitação aberta

É a primeira etapa para aprovar ou negar as solicitações de acesso localizar e abrir a solicitação de acesso com aprovação pendente. Há duas maneiras para abrir a solicitação de acesso.

**Função de pré-requisito:** Aprovador

1. Procure um email do Microsoft Azure que solicita que você deseja aprovar ou negar uma solicitação. Aqui está um email de exemplo:

    ![Aprovar a solicitação para acessar o email de pacote](./media/entitlement-management-shared/email-approve-request.png)

1. Clique o **aprovar ou negar a solicitação** link para abrir a solicitação de acesso.

1. Entre portal do meu acesso.

Se você não tiver o email, você pode encontrar as solicitações de acesso a sua aprovação seguindo estas etapas.

1. Entre portal do meu acesso na [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. No menu à esquerda, clique em **aprovações** para ver uma lista de solicitações de acesso com aprovação pendente.

1. Sobre o **pendente** guia, encontre a solicitação.

## <a name="approve-or-deny-request"></a>Aprovar ou negar a solicitação

Depois de abrir uma solicitação pendente de aprovação de acesso, você pode ver os detalhes que ajudarão você a fazer um aprovar ou negar decisões.

**Função de pré-requisito:** Aprovador

1. Clique o **exibição** link para abrir o painel de solicitações de acesso.

1. Clique em **detalhes** para ver detalhes sobre a solicitação de acesso.

    Os detalhes incluem o nome do usuário, organização, acesso a data de início e término se fornecido, justificativa de negócios, quando a solicitação foi enviada e quando a solicitação irá expirar.

1. Clique em **aprovar** ou **negar**.

1. Se necessário, insira um motivo.

    ![Meu portal de acesso – solicitação de acesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Clique em **enviar** para enviar sua decisão.

    Se uma política é configurada com vários aprovadores, somente um aprovador precisa tomar uma decisão sobre a aprovação pendente. Depois que um aprovador enviou sua decisão para a solicitação de acesso, a solicitação é concluída e não está mais disponível para outros aprovadores aprovar ou negar a solicitação. Outros aprovadores podem ver a decisão de solicitação e o tomador de decisões em seu portal meu acesso. Neste momento, há suporte para apenas a aprovação de fase única.

    Se nenhum dos aprovadores configurados for capaz de aprovar ou negar a solicitação de acesso, a solicitação expira após a duração da solicitação configurado. O usuário é notificado de que sua solicitação de acesso expirou e que eles precisam para reenviar a solicitação de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Notificações de email e o processo de solicitação](entitlement-management-process.md)
