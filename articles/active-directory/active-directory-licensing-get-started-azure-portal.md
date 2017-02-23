---

title: "Introdução ao licenciamento do Azure Active Directory no portal do Azure | Microsoft Docs"
description: "Descrição de licenciamento do Azure Active Directory, como ele funciona, como começar e práticas recomendadas, incluindo o Office 365, Microsoft Intune, e edições do Azure Active Directory Premium e Basic"
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
ms.date: 02/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 0dbe9072cd561f550883cd514a6d25381d095ba8
ms.openlocfilehash: 648b1d51a5e414e67bda475dcef1419ee1692868


---

# <a name="get-started-with-azure-active-directory-licensing-in-the-azure-portal"></a>Introdução ao licenciamento do Azure Active Directory no portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Portal clássico do Azure](active-directory-licensing-what-is.md)
>
>

O Active Directory do Azure (Azure AD) é a identidade da Microsoft como uma solução e plataforma de serviço (IDaaS). O Azure AD é oferecido em várias versões técnicas e funcionais, desde o AD gratuito do Azure, disponível com qualquer serviço da Microsoft, como o Office 365, Dynamics, Microsoft Intune e Azure (o Azure AD não gera encargos de consumo nesse modo), até versões pagas do Azure AD, como o Enterprise Mobility Suite (EMS), Azure AD Premium (P1 e P2) e Azure AD Basic, além da Autenticação Multifator do Azure (MFA). Como muitos serviços online da Microsoft, a maioria das versões pagas do AD do Azure é fornecida por meio de direitos de usuário, como é no Office 365, Microsoft Intune e AD do Azure. Nesses casos, a compra do serviço é representada com uma ou mais assinaturas e cada assinatura inclui um número de pré-compra de licenças em seu locatário. Direitos de usuário são obtidos por meio da atribuição de licença, criando um vínculo entre o usuário e o produto, habilitando os componentes de serviço para o usuário e consumindo uma das licenças pré-pagas.

[Experimente o Azure AD Premium agora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Para obter uma visão geral ampla dos recursos de serviço do AD do Azure, confira [O que é o AD do Azure](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/). Para saber mais, veja nossa página de Contratos de Nível de Serviço.

> [!NOTE]
> As assinaturas pré-pagas do Azure são diferentes: enquanto também representadas em seu diretório, essas assinaturas permitem a criação de recursos do Azure e os mapeiam para seu método de pagamento. Nesse caso, NÃO há contagens de licença associadas à assinatura. A associação de usuários com a assinatura, o acesso dos usuários para gerenciar recursos de assinatura, é alcançada ao conceder permissões a eles para operar em recursos do Azure mapeados para a assinatura.

## <a name="how-does-azure-ad-licensing-work"></a>Como funciona o trabalho de licenciamento do AD do Azure?

Os Serviços do Azure AD baseados em licença funcionam por meio da ativação de uma assinatura no locatário de serviço/diretório do Azure AD. Quando a assinatura está ativa, os recursos de serviço podem ser gerenciados por administradores de serviço/diretório e usados por usuários licenciados.

Quando você compra ou ativa o Enterprise Mobility Suite, Azure AD Premium ou Azure AD Basic, seu diretório é atualizado com a assinatura, incluindo período de validade e licenças pré-pagas. As informações da assinatura, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis no portal do Azure, no bloco Azure Active Directory &gt; **Licenças** para o diretório específico. Este é também o melhor lugar para gerenciar suas atribuições de licença.

Cada assinatura consiste em um ou mais planos de serviço, cada um mapeando o nível funcional incluído do tipo de serviço; por exemplo, Azure AD, Azure MFA, Microsoft Intune, Exchange Online ou SharePoint Online.  O gerenciamento de licença do Azure AD não requer gerenciamento de nível de plano de serviço. Isso é diferente do Office 365, que conta com esse modo de configuração avançada para gerenciar o acesso aos serviços incluídos. O AD do Azure depende da configuração de serviço para habilitar recursos e gerenciar permissões individuais.

> [!IMPORTANT]
> O Azure AD Premium e Básico, bem como assinaturas do Enterprise Mobility Suite, restringem-se a seu diretório/locatário provisionado. As assinaturas não podem ser divididas entre diretórios nem usadas para dar direitos a usuários em outros diretórios. É possível mover uma assinatura entre diretórios, mas requer o envio de um tíquete de suporte ou cancelamento e nova compra, no caso de compras diretas.
>
> Ao comprar o Azure AD ou o Enterprise Mobility Suite por meio do Licenciamento por Volume, a ativação da assinatura ocorrerá automaticamente quando o contrato incluir outros serviços Online da Microsoft, por exemplo, o Office 365.

### <a name="assigning-licenses"></a>Atribuindo licenças

Ao passo que adquirir uma assinatura é tudo o que você precisa para configurar recursos pagos, o uso de recursos pagos do Azure AD requer a distribuição de licenças para os indivíduos certos. Em geral, qualquer usuário que deve ter acesso ou é gerenciado por meio de um recurso pago do AD do Azure deve ter uma licença atribuída. Uma atribuição de licença é um mapeamento entre um usuário e um serviço comprado, como o AD Premium, Basic do Azure ou o Enterprise Mobility Suite.

O gerenciamento de usuários em seu diretório deve ter uma licença e é simples. Ele pode ser realizado atribuindo licenças para grupos no portal do Azure ou atribuindo licenças diretamente aos indivíduos certos por meio do portal, PowerShell ou APIs. Ao atribuir licenças a um grupo, uma licença é atribuída a todos os membros do grupo. Se usuários forem adicionados ou removidos do grupo, a licença apropriada será atribuída ou removida. A atribuição de grupo pode usar qualquer gerenciamento de grupo disponível e é consistente com a atribuição baseada em grupo a aplicativos. Usando essa abordagem, você pode configurar regras, de modo que todos os usuários no diretório recebam atribuição automaticamente, garantir que qualquer pessoa com o cargo apropriado tenha uma licença ou até mesmo delegar a decisão a outros gerentes na organização. Para obter uma discussão detalhada sobre a atribuição de licenças para grupos, incluindo cenários avançados e cenários de licenciamento do Office 365, veja este artigo.

## <a name="getting-started-with-azure-ad-licensing"></a>Introdução ao licenciamento do AD do Azure

A introdução ao Azure AD é fácil; você sempre pode criar seu diretório como parte da assinatura de uma avaliação gratuita do Azure. [Saiba mais sobre como se inscrever como uma organização](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/). As informações a seguir podem ajudá-lo a garantir que seu diretório esteja mais bem alinhado com outros serviços da Microsoft que você possa estar consumindo ou planejando consumir, e suas metas em obter o serviço.

Estas são algumas das práticas recomendadas:

- Se você está usando algum dos serviços organizacionais da Microsoft, já tem um diretório do Azure AD. Nesse caso, você deve continuar a usar o mesmo diretório para outros serviços, para que o gerenciamento de identidade principal, incluindo provisionamento e SSO (logon único) híbrido, possa ser utilizado nos serviços. Os usuários terão uma experiência de logon único e vão se beneficiar de recursos mais sofisticados entre os serviços. Dessa forma, se você decidir comprar um serviço pago do Azure AD para a sua força de trabalho, recomendamos que você use o mesmo diretório para fazer isso.

- Se você planeja usar o Azure AD para um conjunto diferente de usuários (parceiros, clientes e assim por diante) ou se gostaria de avaliar os serviços do Azure AD mas fazê-lo em um ambiente isolado do seu serviço de produção, ou ainda se estiver procurando configurar um ambiente de área restrita para os seus serviços, recomendamos que você primeiro crie um novo diretório por meio do portal clássico do Azure. Saiba como criar um novo diretório do Azure AD no portal clássico do Azure. O novo diretório será criado com sua conta como um usuário externo com permissões de administrador global. Ao entrar no portal do Azure com essa conta, você poderá ver esse diretório e acessar todas as tarefas de administração do diretório.

> [!NOTE]
> O AD do Azure dá suporte a "usuários externos", que são contas de usuário em uma instância do AD do Azure, criadas usando uma MSA (Conta da Microsoft) ou uma identidade do AD do Azure de outro diretório. Enquanto estamos ocupados estendendo essa funcionalidade a todos os serviços organizacionais da Microsoft, neste momento essas contas não têm suporte em algumas experiências de serviços; por exemplo, o portal de administração do Office 365 não dá suporte a esses usuários atualmente. Dessa forma, os usuários externos com contas da Microsoft não poderão acessar o portal de administração do Office 365, e usuários externos de outros diretórios do Azure AD serão ignorados. No último caso, apenas a conta local do usuário, o diretório do Azure AD ou do Office 365 onde o usuário foi originalmente criado estão acessíveis por meio dessas experiências.

Como indicado, o AD do Azure tem diferentes versões pagas. Essas versões têm algumas pequenas diferenças em sua disponibilidade de compra:

|  Produto       |              EA/VL  | Aberto  | CSP |  Direitos de uso do MPN  | Compra direta |  Avaliação |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  AD Basic do Azure          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>Selecione uma ou mais avaliações de licença

Você pode ativar uma assinatura de avaliação do Azure AD Premium ou do Enterprise Mobility Suite no Azure Active Directory &gt; **Início rápido**.

![Selecione uma versão de avaliação de licença](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

As licenças de avaliação agora estará disponíveis no **licenças** folha.

### <a name="assign-licenses-to-users-and-groups"></a>Atribuir licenças a usuários e grupos

Quando a assinatura estiver ativa, você deve atribuir uma licença a si mesmo e atualizar o navegador para garantir a exibição de todos os seus recursos. A próxima etapa é atribuir licenças aos usuários que precisarão de acesso ou inclusão nos recursos pagos do AD do Azure. Como mencionamos anteriormente em [Atribuindo licenças](#assigning-licenses), a melhor maneira de fazer isso é identificar o grupo que representa o público-alvo desejado e atribuí-lo à licença; dessa forma, os usuários adicionados ou removidos do grupo ao longo de seu ciclo de vida terão a licença atribuída ou removida.

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todos os locais; antes de uma licença pode ser atribuída a um usuário, o administrador deve especificar a propriedade "Local de uso" ao usuário. Isso pode ser feito em usuário &gt; perfil &gt; seção de configurações no portal do Azure. Ao usar a atribuição de grupo de licenças, qualquer usuário sem um local de uso especificado herdará o local do diretório.

Para atribuir uma licença a um grupo ou a usuários individuais, no Azure Active Directory &gt; Licenças &gt; Todos os Produtos, selecione um ou mais produtos e clique no botão **Atribuir** na barra de comandos.

![selecionar uma licença para atribuir](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Isso abrirá uma nova folha de onde você pode escolher vários usuários ou grupos e, opcionalmente, desabilitar os planos de serviço no produto. A barra de pesquisa na parte superior pode ser usada para procurar nomes de usuário e grupo.

![Selecione um usuário ou grupo para atribuição de licença](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Ao atribuir uma licença para o grupo, ele pode levar algum tempo, dependendo do número de usuários no grupo, antes de todos os usuários herdam a licença. O status de processamento pode ser verificado na lâmina do grupo, sob o **licenças** lado a lado.

![status de atribuição de licença](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Erros de atribuição podem ocorrer durante a atribuição de licença do AD do Azure, mas é relativamente raro quando Gerenciando o Azure AD e produtos de EMS. Os possíveis erros de atribuição estão limitados a:
- Conflito de atribuição: quando um usuário recebeu anteriormente uma licença que é incompatível com a licença atual. Nesse caso, a atribuição da nova licença exigirá a remoção da atual.
- Licenças disponíveis excedidas: quando o número de usuários em grupos atribuídos excede as licenças disponíveis, o status de atribuição dos usuários refletirá uma falha ao atribuir devido à falta de licenças.

Informações detalhadas sobre a atribuição de grupo de licença estão disponíveis neste artigo.

### <a name="view-assigned-licenses"></a>Exibir licenças atribuídas

É mostrada uma exibição resumida de licenças disponíveis e atribuídas em Azure Active Directory &gt; **Licenças** &gt; **Todos os produtos**.

![Resumo da licença de exibição](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Uma lista detalhada de usuários e grupos atribuídos está disponível ao clicar em um produto específico. **Licenciado a usuários** mostra todos os usuários atualmente consumindo uma licença, incluindo se a licença foi atribuída diretamente ao usuário ou se ela é herdada de um grupo.

![Exibir detalhes de licença](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

Da mesma forma, **licenciado grupos** mostra todos os grupos nos quais licenças foram atribuídos. Clique no usuário ou grupo nessas exibições abrirá o **licenças** folha mostrando todas as licenças atribuídas àquele objeto.

### <a name="removing-a-license"></a>Removendo uma licença

Para remover uma licença, vá para o usuário ou grupo e abra o **licenças** lado a lado. Selecione a licença e clique em **remover**.

![remover uma licença](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Observe que herdadas pelo usuário de um grupo de licenças não podem ser removidas diretamente. Em vez disso, remova o usuário do grupo do qual eles estão herdando a licença.

### <a name="extending-trials"></a>Estendendo avaliações

As extensões de avaliação para clientes estão disponíveis como autoatendimento no portal do Office 365. Um administrador de cliente pode navegar até o portal do Office (o acesso depende de permissões para o portal do Office) e selecionar a avaliação do Azure AD Premium. Clicar a **Estender avaliação** link inicia o processo de extensão. Um cartão de crédito é necessário, mas ele não será cobrado.

![estender uma avaliação no portal do Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre cenários avançados de licença de gerenciamento por meio de grupos de ler este artigo

Agora você pode estar pronto para configurar e usar alguns recursos do AD Premium do Azure.

* [Redefinição de senha de autoatendimento](active-directory-manage-passwords.md)
* [Gerenciamento de grupo de autoatendimento](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Atribuição de grupo a aplicativos](active-directory-manage-groups.md)
* [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md)
* [Compra direta de licenças do AD Premium do Azure](http://aka.ms/buyaadp)



<!--HONumber=Feb17_HO2-->


