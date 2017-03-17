---
title: "Licenciar usuários do Azure Active Directory no Portal Clássico do Azure | Microsoft Docs"
description: "Descrição de licenciamento do Active Directory do Microsoft Azure, como ele funciona, como começar e práticas recomendadas, incluindo o Office 365, Microsoft Intune, e edições do Azure Active Directory Premium e Basic"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d769e8c6-7581-43f5-a3b4-de4b1dca2344
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 7df8805139b6e3cc23041a8201b496fb7fce8063
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-microsoft-azure-active-directory-licensing-in-the-azure-classic-portal"></a>O que é o licenciamento do Microsoft Azure Active Directory no Portal Clássico do Azure?

> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Portal clássico do Azure](active-directory-licensing-what-is.md)
>
>


O Active Directory do Azure (Azure AD) é a identidade da Microsoft como uma solução e plataforma de serviço (IDaaS). O AD do Azure é oferecido em várias versões técnicas e funcionais, desde o AD gratuito do Azure, disponível com qualquer serviço da Microsoft, como o Office 365, Dynamics, Microsoft Intune e Azure (o AD do Azure não gera encargos de consumo nesse modo), até versões pagas do AD do Azure, como o Enterprise Mobility Suite (EMS), AD Premium e Basic do Azure, além de MFA (Multi-Factor Authentication). Como muitos serviços online da Microsoft, a maioria das versões pagas do AD do Azure é fornecida por meio de direitos de usuário, como é no Office 365, Microsoft Intune e AD do Azure. Nesses casos, a compra do serviço é representada com uma ou mais assinaturas e cada assinatura inclui um número de pré-compra de licenças em seu locatário. Direitos de usuário são obtidos por meio da atribuição de licença, criando um vínculo entre o usuário e o produto, habilitando os componentes de serviço para o usuário e consumindo uma das licenças pré-pagas.

[Experimente o Azure AD Premium agora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [!NOTE]
> O portal de administração do AD do Azure é uma parte do portal clássico do Azure. Embora o uso do Azure AD não exija nenhuma compra do Azure, o acesso a esse portal requer uma assinatura ativa do Azure ou uma [assinatura de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/).
>
>

Para obter uma visão geral ampla dos recursos de serviço do AD do Azure, confira [O que é o AD do Azure](active-directory-whatis.md).
[Saiba mais sobre os níveis de serviço do AD do Azure](https://azure.microsoft.com/support/legal/sla/)

> [!NOTE]
> As assinaturas pré-pagas do Azure são diferentes: enquanto também representadas em seu diretório, essas assinaturas permitem a criação de recursos do Azure e os mapeiam para seu método de pagamento. Nesse caso, NÃO há contagens de licença associadas à assinatura. A associação de usuários com a assinatura, o acesso dos usuários para gerenciar recursos de assinatura, é alcançada ao conceder permissões a eles para operar em recursos do Azure mapeados para a assinatura.
>
>

## <a name="how-does-azure-ad-licensing-work"></a>Como funciona o trabalho de licenciamento do AD do Azure?
Os Serviços do AD do Azure baseados em licença (baseados em direito) funcionam ativando uma assinatura no locatário de serviço/diretório do AD do Azure. Quando a assinatura está ativa, os recursos de serviço podem ser gerenciados por administradores de serviço/diretório e usados por usuários licenciados.

Quando você compra ou ativa o Enterprise Mobility Suite, Azure AD Premium ou Azure AD Basic, seu diretório é atualizado com a assinatura, incluindo período de validade e licenças pré-pagas. As informações de assinatura, incluindo status, o próximo evento de ciclo de vida e o número de licenças atribuídas ou disponíveis estão disponíveis no portal clássico do Azure, na guia Licenças do diretório específico. Este é também o melhor lugar para gerenciar suas atribuições de licença.

Cada assinatura consiste em um ou mais planos de serviço, cada um mapeando o nível funcional incluído do tipo de serviço; por exemplo, Azure AD, Azure MFA, Microsoft Intune, Exchange Online ou SharePoint Online. O gerenciamento de licença do AD do Azure não requer gerenciamento de nível de plano de serviço. Isso é diferente do Office 365, que conta com esse modo de configuração avançada para gerenciar o acesso aos serviços incluídos. O AD do Azure depende da configuração de serviço para habilitar recursos e gerenciar permissões individuais.

Em geral, as informações de assinatura do Azure AD são gerenciadas por meio do portal clássico do Azure, na guia Licenças do diretório específico. Assinaturas de Azure AD, com exceção do AD Premium, não aparecem no portal do Office.

> [!IMPORTANT]
> O Azure AD Premium e Básico, bem como assinaturas do Enterprise Mobility Suite, restringem-se a seu diretório/locatário provisionado. As assinaturas não podem ser divididas entre diretórios nem usadas para dar direitos a usuários em outros diretórios. É possível mover uma assinatura entre diretórios, mas requer o envio de um tíquete de suporte ou cancelamento e nova compra, no caso de compras diretas.
>
> Ao comprar o Azure AD ou o Enterprise Mobility Suite por meio do Licenciamento por Volume, a ativação da assinatura ocorrerá automaticamente quando o contrato incluir outros serviços Online da Microsoft, por exemplo, o Office 365.
>
>

Recursos pagos do AD do Azure estendem a amplitude do diretório. Os exemplos incluem:

* Atribuição baseada em grupos para aplicativos, que é habilitada no aplicativo específico que você está gerenciando.
* Estão disponíveis recursos de gerenciamento de grupos avançados e de autoatendimento na configuração do diretório ou dentro do grupo específico.
* Os relatórios de segurança premium estão na guia Relatório
* A detecção de aplicativos de nuvem aparece no portal do Azure em Identidade.

### <a name="assigning-licenses"></a>Atribuindo licenças
Ao passo que adquirir uma assinatura é tudo o que você precisa para configurar recursos pagos, o uso de recursos pagos do Azure AD requer a distribuição de licenças para os indivíduos certos. Em geral, qualquer usuário que deve ter acesso ou é gerenciado por meio de um recurso pago do AD do Azure deve ter uma licença atribuída. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como o AD Premium, Basic do Azure ou o Enterprise Mobility Suite.

O gerenciamento de usuários em seu diretório deve ter uma licença e é simples. Ele pode ser realizado atribuindo a um grupo a criação de regras de atribuição pelo portal de administração do AD do Azure ou atribuindo licenças diretamente aos indivíduos certos por meio de um portal, PowerShell ou APIs. Ao atribuir licenças a um grupo, uma licença é atribuída a todos os membros do grupo. Se usuários forem adicionados ou removidos do grupo, a licença apropriada será atribuída ou removida. A atribuição de grupo pode usar qualquer gerenciamento de grupo disponível e é consistente com a atribuição baseada em grupo a aplicativos. Usando essa abordagem, você pode configurar regras, de modo que todos os usuários no diretório recebam atribuição automaticamente, garantir que qualquer pessoa com o cargo apropriado tenha uma licença ou até mesmo delegar a decisão a outros gerentes na organização.

Com a atribuição de licença baseada em grupo, qualquer usuário sem um local de uso herdará o local do diretório durante a atribuição. Esse local pode ser alterado pelo administrador a qualquer momento. Em casos em que a atribuição automática falhou devido a erro, as informações do usuário naquele tipo de licença refletirão o estado.

## <a name="getting-started-with-azure-ad-licensing"></a>Introdução ao licenciamento do AD do Azure
A introdução ao AD do Azure é fácil; você sempre pode criar seu diretório como parte da assinatura de uma avaliação gratuita do Azure. [Saiba mais sobre como se inscrever como uma organização](sign-up-organization.md). As informações a seguir podem ajudá-lo a garantir que seu diretório esteja mais bem alinhado com outros serviços da Microsoft que você possa estar consumindo ou planejando consumir, e suas metas em obter o serviço.

Estas são algumas das práticas recomendadas:

* Se você está usando algum dos serviços organizacionais da Microsoft, já tem um diretório do Azure AD. Nesse caso, você deve continuar a usar o mesmo diretório para outros serviços, para que o gerenciamento de identidade principal, incluindo provisionamento e SSO híbrido, possa ser utilizado nos serviços. Os usuários terão uma experiência de logon único e vão se beneficiar de recursos mais sofisticados entre os serviços. Como resultado, se você decidir comprar um serviço pago do Azure AD para a sua força de trabalho, recomendamos que você use o mesmo diretório para fazer isso.
* Se você planeja usar o Azure AD para um conjunto diferente de usuários (parceiros, clientes e assim por diante) ou se gostaria de avaliar os serviços do Azure AD mas fazê-lo em um ambiente isolado do seu serviço de produção, ou ainda se estiver procurando configurar um ambiente de área restrita para os seus serviços, recomendamos que você primeiro crie um novo diretório por meio do portal clássico do Azure. [Saiba como criar um novo diretório do AD do Azure no portal clássico do Azure](active-directory-licensing-directory-independence.md). O novo diretório será criado com sua conta como um usuário externo com permissões de administrador global. Ao entrar no portal clássico do Azure com essa conta, você poderá ver esse diretório e acessar todas as tarefas de administração do diretório. É recomendável criar uma conta local com privilégios apropriados para gerenciar outros serviços da Microsoft (aqueles não acessíveis pelo portal clássico do Azure). [Saiba mais sobre como criar contas de usuário no AD do Azure](active-directory-create-users.md).

> [!NOTE]
> O AD do Azure dá suporte a "usuários externos", que são contas de usuário em uma instância do AD do Azure, criadas usando uma MSA (Conta da Microsoft) ou uma identidade do AD do Azure de outro diretório. Enquanto estamos ocupados estendendo essa funcionalidade a todos os serviços organizacionais da Microsoft, neste momento essas contas não têm suporte em algumas experiências de serviços; por exemplo, o portal de administração do Office 365 não dá suporte a esses usuários atualmente. Como resultado, usuários externos com contas da Microsoft não poderão acessar o portal de administração do Office 365, e usuários externos de outros diretórios do AD do Azure serão ignorados. No último caso, apenas a conta local do usuário, o diretório do AD do Azure ou do Office 365 onde o usuário foi originalmente criado estariam acessíveis por meio dessas experiências.
>
>

Como indicado, o AD do Azure tem diferentes versões pagas. Essas versões têm algumas pequenas diferenças em sua disponibilidade de compra:

| Produto | EA/VL | Aberto | CSP | Direitos de uso do MPN | Compra direta | Avaliação |
| --- | --- | --- | --- | --- | --- | --- |
| Enterprise Mobility Suite |X |X |X |X | |X |
| AD Premium do Azure |X |X |X | |X |X |
| AD Basic do Azure |X |X |X |X |<br /> |<br /> |

### <a name="select-one-or-more-license-trials"></a>Selecione uma ou mais avaliações de licença
 Em todos os casos, você pode ativar uma assinatura de avaliação do Azure AD Premium ou o Enterprise Mobility Suite selecionando a avaliação específica desejada na guia Licenças em seu diretório. Qualquer avaliação contém uma assinatura de 30 dias com 100 licenças.

![Planos de licença de avaliação do Active Directory do Azure](./media/active-directory-licensing-what-is/trial_plans.png)

![Planos de licença de avaliação do Enterprise Mobility Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Ativar planos de licença de avaliação](./media/active-directory-licensing-what-is/active_license_trials.png)

### <a name="assign-licenses"></a>Atribuir licenças
Quando a assinatura estiver ativa, você deve atribuir uma licença a si mesmo e atualizar o navegador para garantir a exibição de todos os seus recursos. A próxima etapa é atribuir licenças aos usuários que precisarão de acesso ou inclusão nos recursos pagos do AD do Azure. Como mencionamos anteriormente em "Atribuindo licenças", a melhor maneira de fazer isso é identificar o grupo que representa o público-alvo desejado e atribuí-lo à licença; dessa forma, os usuários adicionados ou removidos do grupo ao longo de seu ciclo de vida terão a licença atribuída ou removida.

Para atribuir uma licença a um grupo ou usuários individuais, selecione o plano de licença que deseja atribuir e clique em **Atribuir** na barra de comandos.

![Ativar planos de licença de avaliação](./media/active-directory-licensing-what-is/assign_licenses.png)

Uma vez na caixa de diálogo de atribuição do plano selecionado, você pode selecionar os usuários e adicioná-los à coluna **Atribuir** à direita. Você pode percorrer a lista de usuários ou pesquisar indivíduos específicos usando a lupa no canto superior direito da grade de usuário. Para atribuir grupos, selecione "Grupos" no menu **Mostrar** e clique no botão de verificação à direita para atualizar as atribuições exibidas.

![Atribuindo licenças a grupos](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Agora você pode pesquisar ou chamar grupos e adicioná-los à coluna **Atribuir** da mesma maneira. Você pode usá-los para atribuir uma combinação de usuários e grupos em uma única operação. Para completar o processo de atribuição, clique no botão de seleção no canto inferior direito da página.

![Mensagem de progresso da atribuição de licença](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Quando um grupo é atribuído, seus membros herdam as licenças dentro de 30 minutos, mas normalmente dentro de 1-2 minutos.

Erros de atribuição podem ocorrer durante a atribuição de licença do AD do Azure, mas são relativamente raros. Os possíveis erros de atribuição estão limitados a:

* Conflito de atribuição - quando um usuário recebeu anteriormente uma licença que é incompatível com a licença atual. Nesse caso, a atribuição da nova licença exigirá a remoção da anterior.
* Licenças disponíveis excedidas - quando o número de usuários em grupos atribuídos excede as licenças disponíveis, o status de atribuição dos usuários refletirá uma falha ao atribuir devido à falta de licenças.

### <a name="view-assigned-licenses"></a>Exibir licenças atribuídas
Uma exibição resumida das licenças atribuídas, incluindo evento próximo, disponível e atribuído do ciclo de vida da assinatura, é mostrada na guia **Licenças** .

![Exibir o número de licenças atribuídas](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Uma lista detalhada de usuários e grupos atribuídos, incluindo status e caminho da atribuição (direta ou herdada de um ou mais grupos) estão disponíveis ao navegar em um plano de licença.

![Exibição de detalhes de licenças atribuídas a um plano de licença](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Remover licenças é tão fácil quanto atribuí-las. Se o usuário for atribuído diretamente ou a um grupo atribuído, você poderá remover a licença selecionando o tipo de licença, selecionando **Remover**, adicionando o usuário ou grupo à lista de remoção e confirmando a ação. Como alternativa, você pode abrir um tipo de licença, selecionar o usuário ou grupo específico, e tocar em **Remover** na barra de comandos. Para encerrar a herança de um usuário de uma licença de um grupo, basta remover o usuário do grupo.

### <a name="extending-trials"></a>Estendendo avaliações
As extensões de avaliação para clientes estão disponíveis como autoatendimento no portal do Office 365. Um administrador de cliente pode navegar até o [portal do Office](https://portal.office.com/#Billing) (o acesso depende de permissões para o portal do Office) e selecionar a avaliação do AD do Azure Premium. Clique no link **Estender avaliação** e siga as instruções. Você terá de inserir um cartão de crédito, mas não será cobrado.

![Estendendo uma avaliação de licença no portal do Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Clientes também podem solicitar uma extensão da avaliação enviando uma solicitação de suporte. Um administrador de cliente pode navegar até a [página de suporte](http://aka.ms/extendAADtrial) do portal do Office 365 (o acesso depende de permissões para o portal do Office). Nesta página, selecione "Assinaturas e Avaliações" em Recursos e "Perguntas de avaliação" em Sintoma. Finalmente, insira informações sobre as circunstâncias

![Estendendo uma avaliação usando uma solicitação de suporte](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode estar pronto para configurar e usar alguns recursos do AD Premium do Azure.

* [Redefinição de senha de autoatendimento](active-directory-manage-passwords.md)
* [Gerenciamento de grupo de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Atribuição de grupo a aplicativos](active-directory-manage-groups.md)
* [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md)
* [Compra direta de licenças do AD Premium do Azure](http://aka.ms/buyaadp)

