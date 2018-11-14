---
title: Como reativar espaços de nomes desabilitados do Azure Access Control Service (ACS)
description: Saiba como encontrar e habilitar os namespaces do Serviço de Controle de Acesso do Azure (ACS) e solicitar uma extensão para mantê-los habilitados até 4 de fevereiro de 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019707"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Como: Reativar namespaces do Serviço de Controle de Acesso desabilitado

Em novembro de 2017, anunciamos que o Serviço de Coleta de Auditoria (ACS), um Serviço do Microsoft Azure Active Directory (Azure AD), está sendo desativado em 7 de novembro de 2018.

Desde então, enviamos vários e-mails para o email de administrador das assinaturas do ACS sobre a aposentadoria do ACS 12 meses, 9 meses, 6 meses, 3 meses, 1 mês, 2 semanas, 1 semana e 1 dia antes da data de aposentadoria de novembro 7, 2018.

Em 3 de outubro de 2018, anunciamos (por e-mail e [uma postagem no blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) uma oferta de extensão a clientes que não podem concluir sua migração antes de 7 de novembro de 2018. O comunicado também continha instruções para solicitar a extensão.

## <a name="why-your-namespace-is-disabled"></a>Por que o seu namespace está desabilitado

Se você não tiver optado pela extensão, começaremos a desativar os namespaces do ACS a partir de 7 de novembro de 2018. Se você perdeu as comunicações e ainda gostaria de optar pela extensão até 4 de fevereiro de 2019, siga as instruções nas seções a seguir.

> [!NOTE]
> Você deve ser um administrador da assinatura para executar os comandos do PowerShell e solicitar uma extensão.

## <a name="find-and-enable-your-acs-namespaces"></a>Localizar e ativar seus namespaces do ACS

Você pode usar o ACS PowerShell para listar todos os namespaces do ACS e reativar os que foram desabilitados.

1. Faça o download e instale o PowerShell do ACS:
    1. Vá para a Galeria do PowerShell e baixe [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instale o módulo:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obter uma lista de todos os comandos possíveis:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Para obter ajuda sobre um comando específico, execute:

        ```
        Get-Help [Command-Name] -Full
        ```
    
        em que `[Command-Name]` é o nome do comando do ACS.
1. Liste suas assinaturas do Azure disponíveis usando o cmdlet **Get-AcsSubscription**.
1. Liste seus namespaces do ACS usando o cmdlet **Get-AcsNamespace**.
1. Confirme se os namespaces estão desabilitados confirmando que `State` é `Disabled`.

    [![Confirme se os namespaces estão desativados](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Você também pode usar `nslookup {your-namespace}.accesscontrol.windows.net` para confirmar se o domínio ainda estiver ativo.

1. Habilite seu (s) namespace (s) ACS usando o **cmdlet Enable-AcsNamespace**.

    Depois de ativar seu namespace, você pode solicitar uma extensão para que o (s) espaço (s) de nomes não sejam desabilitados novamente antes de 4 de fevereiro de 2019. Após essa data, todas as solicitações para o ACS falhará.

## <a name="request-an-extension"></a>Solicitar uma extensão

1. Navegue até o portal de gerenciamento do namespace do ACS indo até `https://{your-namespace}.accesscontrol.windows.net`.
1. Selecione o botão **Ler Termos** para ler os [Termos de Uso atualizados](https://azure.microsoft.com/support/legal/access-control/), que direcionarão você para uma página com os Termos de Uso atualizados.

    [![Selecione o botão de termos de leitura](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Selecione **Solicitar extensão** no banner na parte superior da página. O botão só será ativado depois que você ler os [Termos de Uso atualizados](https://azure.microsoft.com/support/legal/access-control/).

    [![Selecione o botão de solicitação de extensão](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. Depois que a solicitação de extensão for registrada, a página será atualizada com um novo banner na parte superior da página.

    [![Página atualizada com faixa atualizada](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Ajuda e suporte

- Se você tiver problemas depois de seguir estas instruções, entre em contato com [suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Se você tiver dúvidas ou comentários sobre a desativação do ACS, entre em contato conosco em acsfeedback@microsoft.com.

## <a name="next-steps"></a>Próximas etapas

- Examine as informações sobre a desativação do Serviço de Coleta de Acesso no [Como: migrar do serviço de controle de acesso do Azure](active-directory-acs-migration.md).
