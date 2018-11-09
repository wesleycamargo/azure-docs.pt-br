---
title: O que são controles de acesso no acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como os controles de acesso no acesso condicional do Azure Active Directory funcionam.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: a40774faadc4393a1ebbb6e26673ba7dff22560c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092297"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>O que são controles de acesso no acesso condicional do Azure Active Directory? 

Com o [acesso condicional do Azure AD (Azure Active Directory)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Em uma política de acesso condicional, você define a resposta ("faça isso") para o motivo do acionamento de sua política ("quando isso acontecer"). 

![Controle](./media/controls/10.png)


No contexto de acesso condicional, 

- "**Quando isso acontece**" é chamado de **condições**

- "**Então faça isso**" é chamado de **controles de acesso**


A combinação de uma instrução de condição com seus controles representa uma política de acesso condicional.

![Controle](./media/controls/61.png)

Cada controle é um requisito que deve ser atendido pela pessoa ou sistema que está entrando, ou uma restrição sobre que o usuário poderá fazer depois de entrar. 

Há dois tipos de controle: 

- **Controles de concessão** - para controle de acesso

- **Controles de sessão** - para restringir o acesso em uma sessão

Este tópico explica os vários controles que estão disponíveis no acesso condicional do Azure AD. 

## <a name="grant-controls"></a>Controles de concessão

Com controles de concessão, você pode bloquear o acesso completamente ou permitir o acesso com requisitos adicionais ao selecionar os controles desejados. Para vários controles, você pode exigir:

- Todos os controles adicionados devem ser preenchidos (*AND*) 
- Um controle selecionado a ser preenchido (*OR*)

![Controle](./media/controls/17.png)



### <a name="multi-factor-authentication"></a>Autenticação multifator

Você pode usar este controle para exigir autenticação multifator para acessar o aplicativo de nuvem especificado. Esse controle dá suporte aos seguintes provedores multifator: 

- Autenticação Multifator do Azure 

- Um provedor de autenticação multifator local, combinados com os serviços de Federação do Active Directory (AD FS).
 
Usar a autenticação multifator ajuda a proteger recursos para impedir que sejam acessados por um usuário não autorizado que obteve acesso às credenciais principais de um usuário válido.



### <a name="compliant-device"></a>Dispositivo em conformidade

Você pode configurar políticas de acesso condicional com base no dispositivo. O objetivo de uma política de acesso condicional baseada em dispositivo é conceder acesso aos recursos configurados somente de [dispositivos gerenciados](require-managed-devices.md). Exigir um dispositivo compatível é uma opção para a qual você precisa definir, o que é um dispositivo gerenciado. Se essa opção for selecionada, sua política de acesso condicional concederá acesso a tentativas de acesso feitas com dispositivos que estão [registrados](../devices/overview.md) no Azure Active Directory e são marcados como compatíveis por Intune (para qualquer dispositivo OS) ou pelo seu sistema de terceiros MDM para dispositivos Windows 10. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados.

Para saber mais, confira [configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo adicionado ao Azure AD híbrido

Exigir um dispositivo adicionado no Azure AD híbrido é outra opção para a qual você precisa configurar políticas de acesso condicional com base no dispositivo. Esse requisito se refere a desktops, laptops e tablets corporativos com Windows que ingressaram em um Active Directory local. Se essa opção for selecionada, sua política de acesso condicional concederá acesso a tentativas de acesso feitas com dispositivos que ingressaram no seu Active Directory local e no Azure Active Directory.  

Para saber mais, confira [configurar políticas de acesso condicional com base no dispositivo do Azure Active Directory](require-managed-devices.md).





### <a name="approved-client-app"></a>Aplicativo do cliente aprovado

Como seus funcionários usam dispositivos móveis para as tarefas pessoais e de trabalho, talvez você queira poder proteger os dados da empresa acessados por meio de dispositivos, mesmo no caso em que eles não são gerenciados por você.
Você pode usar [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger os dados da empresa independentemente de qualquer solução de gerenciamento de dispositivo móvel (MDM).


Com aplicativos de cliente aprovados, você pode exigir que um aplicativo cliente tente acessar seus aplicativos de nuvem para oferecer suporte a [políticas de proteção do aplicativo Intune](https://docs.microsoft.com/intune/app-protection-policy). Por exemplo, você pode restringir o acesso ao Exchange Online para o aplicativo do Outlook. Uma política de acesso condicional que exige um dispositivo confiável também é conhecida como [política de acesso condicional com base no dispositivo](app-based-conditional-access.md). Para obter uma lista de aplicativos cliente aprovados e com suporte, veja [requisito de aplicativo cliente aprovado](technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Termos de Uso

Você pode exigir que um usuário em seu locatário concorde com os termos de uso antes de obter acesso a um recurso. Como administrador, você pode configurar e personalizar termos de uso, carregando um documento PDF. Se um usuário estiver escopo desse controle, ele receberá acesso a um aplicativo somente se concordar com os termos de uso. 


### <a name="custom-controls-preview"></a>Controles personalizados (versão prévia) 

Você pode criar controles personalizados em Acesso Condicional que redirecionam os usuários para um serviço compatível para atender requisitos adicionais fora do Azure Active Directory. Isso permite que você use determinados provedores de verificação e autenticação multifator externos para impor regras de Acesso Condicional ou para criar seu próprio serviço personalizado. Para satisfazer esse controle, o navegador do usuário é redirecionado para um serviço externo, realiza qualquer autenticação ou atividades de validação necessárias e, em seguida, é redirecionado ao Azure Active Directory. Se o usuário foi autenticado ou validado com êxito, ele continua no fluxo de Acesso Condicional. 

## <a name="custom-controls"></a>Controles personalizados

Os controles personalizados são uma funcionalidade do Azure Active Directory Premium P1 Edition. Ao usar controles personalizados, seus usuários são redirecionais para um serviço compatível a fim de atender a requisitos adicionais fora do Azure Active Directory. Para satisfazer esse controle, o navegador do usuário é redirecionado para um serviço externo, realiza qualquer autenticação ou atividades de validação necessárias e, em seguida, é redirecionado ao Azure Active Directory. O Azure Active Directory verifica a resposta e, se o usuário foi autenticado ou validado com êxito, ele continua no fluxo de acesso condicional.

Esses controles permitem o uso de determinados serviços externos ou personalizados como controles de acesso condicionais e geralmente estendem as funcionalidades do Acesso Condicional.

Provedores que atualmente oferecem um serviço compatível incluem:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para obter mais informações sobre esses serviços, entre em contato diretamente com os provedores.

### <a name="creating-custom-controls"></a>Criando controles personalizados

Para criar um controle personalizado, você deve entrar em contato com o provedor que você deseja utilizar. Cada provedor não Microsoft tem seu próprio processo e requisitos para inscrição, assinatura ou outro modo de tornar-se parte do serviço, bem como para indicar que você deseja integrar-se com acesso condicional. Nesse ponto, o provedor lhe fornecerá um bloco de dados no formato JSON. Esses dados permitem que o provedor e o acesso condicional funcionem em conjunto para o seu locatário, cria o novo controle e define como o acesso condicional pode determinar se os usuários executaram com êxito a verificação com o provedor.

Copie os dados JSON e, em seguida, cole-os na caixa de texto relacionada. Não faça alterações ao JSON, a menos que você compreenda explicitamente a alteração que você está fazendo. Fazer qualquer alteração pode interromper a conexão entre o provedor e a Microsoft e potencialmente bloquear você e seus usuários do acesso às respectivas contas.

A opção para criar um controle personalizado está na seção **Gerenciar** da página **Acesso condicional**.

![Controle](./media/controls/82.png)

Clicar em **Novo controle personalizado** abrirá uma folha com uma caixa de texto para os dados JSON do seu controle.  


![Controle](./media/controls/81.png)


### <a name="deleting-custom-controls"></a>Excluindo controles personalizados

Para excluir um controle personalizado, primeiro você deverá assegurar que ele não esteja sendo usado em nenhuma política de acesso condicional. Depois de concluído:

1. Vá para a lista de Controles personalizados

2. Clique em...  

3. Selecione **Excluir**.

### <a name="editing-custom-controls"></a>Editando controles personalizados

Para editar um controle personalizado, você deve excluir o controle atual e criar um novo controle com as informações atualizadas.




## <a name="session-controls"></a>Controles de sessão

Os controles de sessão permitem a limitação da experiência dentro de um aplicativo na nuvem. Os controles de sessão são impostos por aplicativos de nuvem e contam com informações adicionais sobre a sessão fornecidas pelo Azure AD para o aplicativo.

![Controle](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usar restrições de aplicativo impostas

Você pode usar esse controle para exigir que o Azure AD passe informações sobre o dispositivo para os aplicativos na nuvem selecionados. As informações do dispositivo permitem que os aplicativos na nuvem saibam se uma conexão é iniciada a partir de um dispositivo compatível ou associado a um domínio. Esse controle suporta apenas o SharePoint Online e o Exchange Online como aplicativos de nuvem selecionados. Quando selecionado, o aplicativo na nuvem usa as informações do dispositivo para fornecer aos usuários, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações, consulte:

- [Ativando acesso limitado com o SharePoint Online](https://aka.ms/spolimitedaccessdocs) 

- [Ativando acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)




## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 
