---
title: "Introdução ao licenciamento no Azure Active Directory | Microsoft Docs"
description: "Como o licenciamento do Azure Active Directory funciona, como começar a trabalhar com as práticas recomendadas"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Licenciar a si mesmo e seus usuários no Azure Active Directory

> [!div class="op_single_selector"]
> * [Instruções do Portal do Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Informações sobre como obter o portal clássico do Azure](active-directory-licensing-what-is.md)
>
>

O Azure AD (Azure Active Directory) é uma solução e plataforma de identidade como um serviço (IDaaS) da Microsoft. O Azure AD é oferecido em edições de serviço diferentes:

* O Azure Active Directory Gratuito, que está disponível em qualquer serviço da Microsoft, como Office 365, Dynamics, Microsoft Intune ou Azure. O Azure AD não gera nenhum encargo de consumo nesse modo.

* Edições pagas do Azure AD, como:
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 e P2)
  - AD Basic do Azure
  - Autenticação Multifator do Azure

Como muitos serviços online da Microsoft, a maioria das versões pagas do AD do Azure é fornecida por meio de direitos de usuário, como é no Office 365, Microsoft Intune e AD do Azure. Nesses casos, a compra do serviço é representada por uma ou mais assinaturas e cada assinatura inclui algumas licenças adquiridas na pré-compra em seu locatário. Os direitos por usuário são obtidos ao:

* Atribuir uma licença. 
* Criar um vínculo entre o usuário e o produto.
* Habilitar os componentes de serviço para o usuário.
* Consumir uma das licenças pré-pagas.

[Experimente o Azure AD Premium agora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Para obter uma visão geral ampla dos recursos de serviço do Azure AD, confira [O que é o Azure AD?](active-directory-whatis.md). Para obter mais informações, veja nossa [página de Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/).

> [!NOTE]
> As assinaturas pré-pagas do Azure permitem a criação de recursos do Azure para, então, mapeá-los para seu método de pagamento. Não há contagens de licença associadas à assinatura. Ao conceder uma permissão de usuário para operar os recursos mapeados Azure na assinatura, eles serão associados à assinatura e podem gerenciar os recursos de assinatura.

## <a name="how-does-azure-active-directory-licensing-work"></a>Como funciona a licença do Azure Active Directory?

Os serviços do Azure AD baseados em licença funcionam ao ativar uma assinatura no locatário de serviço do Azure AD. Depois que a assinatura estiver ativa, as funcionalidades de serviço poderão ser gerenciadas por administradores do Azure AD e usadas por usuários licenciados.

### <a name="manage-subscription-information"></a>Gerenciamento de informações da assinatura

Quando você comprar o Enterprise Mobility + Security, Azure AD Premium ou Azure AD Basic, seu locatário será atualizado com a assinatura, incluindo período de validade e licenças pré-pagas. As informações da assinatura, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis no Portal do Azure: em **Azure Active Directory**, abra o bloco **Licenças** para o diretório específico. A folha **Licenças** também é o melhor lugar para gerenciar suas atribuições de licença.

Cada assinatura consiste em um ou mais planos de serviço, como o Azure AD, Autenticação Multifator, Intune, Exchange Online ou SharePoint Online.  O gerenciamento de licença do Azure AD *não* requer gerenciamento de nível de plano de serviço. O Office 365 é diferente porque depende desse modo de configuração avançada para gerenciar o acesso aos serviços incluídos. O Azure AD depende da configuração de serviço para habilitar recursos e gerenciar permissões individuais.

> [!IMPORTANT]
> As assinaturas do Azure AD Premium, Azure AD Basic e Enterprise Mobility + Security restringem-se aos seus diretórios/locatários provisionados. As assinaturas não podem ser divididas entre diretórios nem usadas para dar direitos a usuários em outros diretórios. É possível mover uma assinatura entre diretórios, mas requer o envio de um tíquete de suporte ou cancelamento e nova compra para compras diretas.
>
> Quando o Azure AD ou o Enterprise Mobility + Security for comprado por meio de uma assinatura de Licenciamento por Volume, quando o contrato incluir outros serviços Online da Microsoft (por exemplo, o Office 365), a ativação acontecerá automaticamente. 

### <a name="assign-licenses"></a>Atribuir licenças

Embora a aquisição de uma assinatura seja tudo o que você precisa para configurar funcionalidades pagas, você ainda deve distribuir licenças para recursos pagos do Azure AD aos usuários. Qualquer usuário que deve ter acesso ou é gerenciado por meio de um recurso pago do Azure AD deve ter uma licença atribuída. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como o Azure AD Premium, Básico ou o Enterprise Mobility + Security.


O gerenciamento de usuários, em seu diretório, que devem ter uma licença pode ser obtido ao: 

* Atribuir licenças aos grupos no [Portal do Azure](active-directory-licensing-whatis-azure-portal.md).
* Atribuir licenças diretamente aos usuários por meio do portal, do PowerShell ou de APIs. 

Ao atribuir licenças a um grupo, uma licença é atribuída a todos os membros do grupo. Se usuários forem adicionados ou removidos do grupo, a licença apropriada será atribuída ou removida. A atribuição de grupo pode usar qualquer gerenciamento de grupo disponível e é consistente com a atribuição baseada em grupo a aplicativos.

Você pode usar [atribuição de licença baseada em grupo](active-directory-licensing-whatis-azure-portal.md) para configurar as regras, como o seguinte:
* Todos os usuários em seu diretório obtêm automaticamente uma licença
* Todos com o cargo apropriado obtêm uma licença
* Você pode delegar a decisão de outros gerenciadores na organização (ao usar [grupos de autoatendimento](active-directory-accessmanagement-self-service-group-management.md))

Para obter uma discussão detalhada sobre a atribuição de licenças para grupos, incluindo cenários avançados e cenários de licenciamento do Office 365, consulte [Atribuir licenças a usuários por associação de grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="get-started-with-azure-ad-licensing"></a>Introdução ao licenciamento do Azure AD

É fácil começar a usar o Azure AD. Você sempre pode criar seu diretório como parte de uma inscrição para uma [avaliação gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

As práticas recomendadas a seguir podem ajudar a garantir que seu locatário esteja alinhado com outros serviços da Microsoft que você pode estar consumindo e com seus objetivos para o serviço:

- Caso já esteja usando algum dos serviços organizacionais da Microsoft, você já tem um locatário do Azure AD. É útil usar o mesmo locatário para outros serviços, para que o gerenciamento de identidade principal, incluindo provisionamento e SSO (logon único) híbrido, possa ser utilizado nos serviços. Com o logon único, os usuários se beneficiam de funcionalidades avançadas entre serviços. Dessa forma, se você decidir comprar um serviço pago do Azure AD para a sua força de trabalho, recomendamos que você use o mesmo locatário novamente.

- É recomendável que você use um novo locatário no Portal do Azure se você estiver planejando:
  - Usar o Azure AD para um conjunto diferente de usuários (como parceiros ou clientes).
  - Avaliar os serviços do Azure AD isoladamente de seu serviço de produção.
  - Configurar um ambiente de área restrita para seus serviços.

  O novo diretório é criado com sua conta como um usuário externo com permissões de administrador global. Ao entrar no Portal do Azure com essa conta, você poderá ver esse locatário e acessar todas as tarefas de administração.

> [!NOTE]
> O Azure AD dá suporte a "usuários convidados", que são contas de usuário em um locatário do Azure AD criadas usando uma conta da Microsoft ou uma identidade do Azure AD de outro locatário. No momento, o portal de administração do Office 365 não oferece suporte para esses usuários. Os usuários convidados com contas da Microsoft não podem acessar o portal de administração do Office 365, enquanto os usuários convidados de outros locatários do Azure AD são ignorados. No último caso, apenas a conta local do usuário, no locatário do Azure AD ou do Office 365 no qual o usuário foi originalmente criado, está acessível.

### <a name="select-one-or-more-license-trials"></a>Selecione uma ou mais avaliações de licença

É possível ativar uma assinatura de avaliação do Azure AD Premium ou do Enterprise Mobility + Security no **Azure Active Directory** &gt; **Início rápido**.

![Selecione uma versão de avaliação de licença](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

As licenças de avaliação agora estão disponíveis na folha **Licenças**.

### <a name="assign-licenses-to-users-and-groups"></a>Atribuir licenças a usuários e grupos

Depois que a assinatura estiver ativa, você deverá atribuir uma licença a si mesmo. Em seguida, atualize o navegador para garantir que esteja vendo todos os recursos. A próxima etapa é atribuir licenças aos usuários que precisam de acesso a recursos pagos do Azure AD. Conforme descrito em [Atribuir licenças](#assign-licenses), uma maneira fácil de atribuir licenças é identificar o grupo que representa o público-alvo desejado e atribuir a licença a ele. Dessa forma, os usuários adicionados ou removidos do grupo durante seu ciclo de vida são atribuídos ou removidos da licença, respectivamente.

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador deve especificar a propriedade **Local de uso** para o usuário. É possível definir essa propriedade em **Usuário** &gt; **Perfil** &gt; **Configurações** no Portal do Azure. Ao usar a atribuição de licença de grupo, qualquer usuário, cujo local de uso não está especificado, herda o local do diretório.

Para atribuir uma licença, no **Azure Active Directory** &gt; **Licenças** &gt; **Todos os Produtos**, selecione um ou mais produtos e selecione **Atribuir** na barra de comandos.

![Selecione uma licença para atribuir](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Você pode usar a folha **Usuários e grupos** para escolher vários usuários ou grupos ou desabilitar os planos de serviço no produto. Use a caixa de pesquisa na parte superior para pesquisar nomes de usuário e grupo.

![Selecione um usuário ou grupo para atribuição de licença](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Ao atribuir uma licença para o grupo, pode levar algum tempo para que todos os usuários herdem a licença, dependendo do número de usuários no grupo. É possível verificar o status de processamento na folha **Grupo**, no bloco **Licença**.

![Status de atribuição de licença](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Erros de atribuição podem ocorrer durante a atribuição de licença do Azure AD, mas são relativamente raros ao gerenciar os produtos do Azure AD e do Enterprise Mobility + Security. Os possíveis erros de atribuição estão limitados a:
- Conflito de atribuição: quando um usuário recebeu anteriormente uma licença que é incompatível com a licença atual. Nesse caso, a atribuição da nova licença exigirá a remoção da atual.
- Licenças disponíveis excedidas: quando o número de usuários em grupos atribuídos excede as licenças disponíveis, o status de atribuição do usuário reflete uma falha ao atribuir devido à falta de licenças.

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Licenciamento da colaboração B2B do Azure AD

A colaboração B2B permite chamar usuários convidados no seu locatário do Azure AD para fornecer acesso aos serviços do Azure AD e a qualquer recurso do Azure que você disponibilizar.  

Não há nenhum custo para convidar usuários B2B e atribuí-los a um aplicativo no Azure AD. Até 10 aplicativos por usuário convidado e 3 relatórios básicos também são gratuitos para usuários de colaboração B2B. Se o usuário convidado tiver as licenças apropriadas atribuídas no locatário do Azure AD do parceiro, ele será licenciado também na sua.

Não é obrigatório, mas se você desejar fornecer acesso a recursos pagos do Azure AD, os usuários convidados B2B deverão ser licenciados com licenças adequadas do Azure AD. Um locatário que está convidando com uma licença paga do Azure AD pode atribuir direitos de usuário de colaboração B2B a outros cinco usuários convidados adicionais para o locatário. Para obter informações e cenários, consulte [Diretrizes de licenciamento de colaboração B2B](active-directory-b2b-licensing.md).

### <a name="view-assigned-licenses"></a>Exibir licenças atribuídas

É mostrada uma exibição resumida de licenças disponíveis e atribuídas no **Azure Active Directory** &gt; **Licenças** &gt; **Todos os produtos**.

![Exibir resumo da licença](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Uma lista detalhada de usuários e grupos atribuídos está disponível ao selecionar um produto específico. A lista de **Usuários licenciados** mostra todos os usuários atualmente consumindo uma licença e se a licença foi atribuída diretamente ao usuário ou se ela é herdada de um grupo.

![Exibir detalhes da licença](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

Da mesma forma, a lista de **Grupos Licenciados** mostra todos os grupos aos quais as licenças foram atribuídas. Selecione um usuário ou grupo para abrir a folha **Licenças**, que mostra todas as licenças atribuídas àquele objeto.

### <a name="remove-a-license"></a>Remover uma licença

Para remover uma licença, vá até o usuário ou grupo e abra o bloco **Licenças**. Selecione a licença e clique em **Remover**.

![Remover uma licença](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

As licenças herdadas pelo usuário de um grupo não podem ser removidas diretamente. Em vez disso, remova o usuário do grupo do qual eles estão herdando a licença.

### <a name="extend-trials"></a>Estender avaliações

As extensões de avaliação para clientes estão disponíveis como entrada de autoatendimento no portal do Office 365. Um administrador de cliente pode ir até o portal do Office (o acesso depende de permissões para o portal do Office) e selecionar a avaliação do Azure AD Premium. Clicar a **Estender avaliação** link inicia o processo de extensão. Um cartão de crédito é necessário, mas ele não é cobrado.

![Estender uma avaliação no portal do Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre cenários avançados de gerenciamento de licença por meio de grupos, leia o artigo [Atribuindo licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md).

Aqui estão informações sobre como configurar e usar outros recursos pagos do Azure AD:

* [Redefinição de senha de autoatendimento](active-directory-manage-passwords.md)
* [Gerenciamento de grupo de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md)
* [Compra direta de licenças do AD Premium do Azure](http://aka.ms/buyaadp)

