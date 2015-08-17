<properties 
	pageTitle="Administrar seu diretório do Azure AD" 
	description="Um tópico que explica o que é um locatário do Azure AD e como gerenciar um diretório do Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Administrar seu diretório do Azure AD

## O que é um locatário AD Azure?

No local de trabalho físico, o termo “locatário” pode ser definido como um grupo ou empresa que ocupa um prédio. Por exemplo, sua organização pode possuir um escritório em um prédio. Esse prédio pode estar em uma rua com várias outras organizações. Sua organização seria considerada uma locatária desse prédio. Esse prédio é um ativo de sua organização, oferecendo segurança e garantindo que você possa conduzir os negócios com segurança. Ele também é separado das outras empresas em sua rua. Isso garante que sua organização e seus ativos estejam isolados de outras organizações.

No local de trabalho habilitado pela nuvem, um locatário pode ser definido como um cliente ou uma organização que possui e gerencia uma instância específica desse serviço em nuvem. Com a plataforma de identidade fornecida pelo Microsoft Azure, um locatário é simplesmente uma instância dedicada do Azure Active Directory (Azure AD) que sua organização recebe e detém quando se inscreve em um serviço de nuvem da Microsoft, como o Azure ou Office 365.

Cada diretório do Azure AD é distinto e separado de outros diretórios do Azure AD. Assim como um prédio de escritórios corporativos é um ativo seguro específico somente a sua organização, um diretório do Azure AD também foi projetado para ser um ativo seguro para ser usado somente por sua organização. A arquitetura do Azure AD isola os dados as informações de identidade do cliente, evitando que sejam misturados a outros. Isso significa que os usuários e administradores de um diretório do Azure AD não podem acessar acidentalmente ou maliciosamente dados em outro diretório.

![][1]

## Como posso obter um diretório do Azure AD?

O Azure AD oferece os principais recursos de gerenciamento de diretório e de identidade por trás da maioria dos serviços de nuvem da Microsoft, incluindo:

- As tabelas
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

Quando você se inscreve para qualquer um desses serviços de nuvem da Microsoft, você obterá um diretório do Azure AD. Você pode criar diretórios adicionais conforme necessário. Por exemplo, você pode manter seu primeiro diretório como um diretório de produção e, em seguida, criar um outro diretório para teste ou preparo.

> [AZURE.NOTE]Após se inscrever em seu primeiro serviço, recomendamos que você use a mesma conta de administrador associada à sua organização ao se inscrever para outros serviços de nuvem da Microsoft.

Da primeira vez que você se inscrever para um serviço de nuvem da Microsoft, precisará fornecer detalhes sobre sua organização e o registro de nome de domínio na Internet da sua organização. Em seguida, essas informações são usadas para criar uma nova instância de diretório do Azure AD para sua organização. Esse mesmo diretório é usado para autenticar as tentativas de logon quando você se inscreve para vários serviços de nuvem da Microsoft.

Os serviços adicionais utilizam completamente quaisquer contas de usuário, políticas, configurações ou integrações de diretórios locais existentes configurados para ajudar a melhorar a eficiência entre a infraestrutura de identidade local da sua organização e o Azure AD.

Por exemplo, se você originalmente se inscreveu para uma assinatura do Microsoft Intune e concluiu as etapas necessárias para integrar adicionalmente seu Active Directory local ao diretório do Azure AD, implantando a sincronização de diretórios e/ou servidores de conexão única, você pode se inscrever para outro serviço de nuvem da Microsoft, como o Office 365, que também poderá utilizar os mesmos benefícios de integração de diretório utilizados com o Microsoft Intune.

Para obter mais informações sobre como integrar seu diretório local ao Azure AD, consulte [Integração de diretórios](active-directory-aadconnect.md).

### Associar um diretório do Azure AD a uma nova assinatura do Azure

Você pode associar uma nova assinatura do Azure ao mesmo diretório que autentica o logon para uma assinatura existente do Office 365 ou Microsoft Intune. Entre no Portal de Gerenciamento do Azure usando sua conta corporativa ou de estudante. O Portal de Gerenciamento retorna uma mensagem de que não foi possível encontrar nenhuma assinatura para essa conta. Selecione **Inscrever-se no Azure** e seu diretório ficará disponível para administração dentro do portal. Para obter mais informações, consulte [Gerenciar o diretório para sua assinatura do Office 365 no Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Para obter um vídeo sobre questões de uso comuns do Azure AD, consulte [Azure Active Directory - Questões comuns sobre inscrição, logon e uso](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### Criar um diretório do Azure AD inscrevendo-se para um serviço de nuvem da Microsoft como uma organização

Se você ainda não tiver uma assinatura para um serviço de nuvem da Microsoft, use um dos links abaixo para se inscrever. O ato de inscrever-se para seu primeiro serviço criará um diretório do Azure AD automaticamente.

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### Gerenciar um diretório padrão de provisionamento do Azure

Hoje, um diretório é criado automaticamente ao se inscrever para o Azure, e sua assinatura é associada a esse diretório. Mas se você tiver originalmente se inscrito no Azure antes de outubro de 2013, um diretório não terá sido criado automaticamente. Nesse caso, o Azure pode ter "aterrado" sua conta fornecendo um diretório padrão para ela. Sua assinatura é então associada a esse diretório padrão.

O aterramento de diretórios foi realizado em outubro de 2013 como parte de uma melhoria geral no modelo de segurança do Azure. Ele ajuda a oferecer recursos de identidade institucional a todos os clientes do Azure e garante que todos os recursos do Azure são acessados no contexto de um usuário no diretório. Você não pode usar o Azure sem um diretório. Para fazer isso, qualquer usuário que tenha se inscrito antes de 7 de julho de 2013, mas que não tivesse um diretório, precisava ter um criado. Se você já tivesse criado um diretório, sua assinatura era associada a esse diretório.

Não há nenhum custo associado ao uso do Azure AD. O diretório é um recurso gratuito. Há uma camada adicional do Azure Active Directory Premium que é licenciada separadamente e fornece recursos adicionais, como identidade visual para empresas e redefinição de senha com autoatendimento.

Para alterar o nome de exibição do diretório, clique no diretório no portal e em **Configurar**. Conforme explicado neste tópico, você pode adicionar um novo diretório ou excluir um diretório de que não precisa mais. Para associar sua assinatura a um diretório diferente, clique na extensão **Configurações** no painel de navegação esquerdo e, na parte inferior da página **Assinaturas**, clique em **Editar diretório**. Você também pode criar um domínio personalizado usando um nome DNS que você registrou ao invés do domínio padrão *.onmicrosoft.com, que pode ser preferível com um serviço como o SharePoint Online.

## Como posso gerenciar dados de diretório

Como administrador de uma ou mais assinaturas de serviços de nuvem da Microsoft, você pode usar o Portal de Gerenciamento do Azure, o portal de conta do Microsoft Intune ou o Centro de Administração do Office 365 para gerenciar os dados de diretório da sua organização. Você também pode baixar e executar cmdlets do [Microsoft Azure Active Directory Module for Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) para ajudá-lo a gerenciar dados armazenados no Azure AD.

De qualquer um desses portais (ou cmdlets), você pode:

- Criar e gerenciar contas de usuário e de grupo
- Gerenciar serviços de nuvem relacionados nas quais sua organização está inscrita
- Configurar a integração local com seu serviço de diretório

O Portal de Gerenciamento do Azure, o Centro de Administração do Office 365, o portal de conta do Microsoft Intune e os cmdlets do Azure AD realizam leituras e gravações a partir de uma única instância compartilhada do Azure AD que está associada ao diretório da sua organização, conforme mostrado na ilustração a seguir. Dessa forma, os portais (ou cmdlets) agem como uma interface de front-end que recebe e/ou modifica os dados do diretório.

![][2]

Esses portais de conta e os cmdlets associados do PowerShell do Azure AD usados para gerenciar usuários e grupos são criados sobre a plataforma do Azure AD.

Quando você fizer uma alteração nos dados da sua organização usando qualquer um dos portais (ou cmdlets) enquanto estiver conectado sob o contexto de um desses serviços, a alteração também será mostrada nos outros portais da próxima vez que entrar no contexto desse serviço, porque estes dados são compartilhados entre os serviços de nuvem da Microsoft para os quais você se inscreveu. Por exemplo, se você usou o Centro de Administração do Office 365 para bloquear a conexão de um usuário, essa ação impedirá que o usuário faça logon em qualquer outro serviço ao qual sua organização tenha se inscrito atualmente. Se você analisar a conta desse mesmo usuário sob o contexto do portal de contas do Microsoft Intune, você verá que o usuário está bloqueado.

## Como posso adicionar e gerenciar vários diretórios?

Você pode adicionar um diretório do Azure AD no Portal de Gerenciamento do Azure. Selecione a extensão **Active Directory** à esquerda e clique em **Adicionar**.

Você pode gerenciar cada diretório como um recurso completamente independente: cada diretório é um par, com recursos completos e logicamente independente de outros diretórios que você gerencia; não há nenhuma relação de pai-filho entre os diretórios. Essa independência entre diretórios inclui independência de recursos, independência administrativa e independência de sincronização.

- **Independência de recursos**. Se você criar ou excluir um recurso em um diretório, ele não afeta nenhum recurso em outro diretório, com a exceção parcial de usuários externos, como descrito abaixo. Se você usar um domínio personalizado "contoso.com" com um diretório, ele não pode ser usado com nenhum outro diretório. 
- **Independência administrativa**. Se um usuário não administrativo do diretório "Contoso" criar um diretório "Teste", então: 
    - ◦Ferramenta de sincronização do diretório, para sincronizar dados com uma única floresta do AD. 
    - ◦Os administradores do diretório "Contoso" não têm privilégios administrativos diretos para o diretório "Teste", a menos que um administrador de "Teste" conceda especificamente esses privilégios a eles. Os administradores do "Contoso" podem controlar o acesso ao diretório "Teste" por meio de seu controle da conta do usuário que criou "Teste". 

    E se você alterar (adicionar ou remover) uma função de administrador para um usuário em um diretório, a alteração não afeta nenhuma função de administrador que o usuário pode ter em outro diretório.


- **Independência de sincronização**. Você pode configurar cada Azure AD independentemente para que os dados sejam sincronizados a partir de uma única instância de:
    - Ferramenta de sincronização do diretório, para sincronizar dados com uma única floresta do AD.
    - Azure Active Directory Connector para o Forefront Identity Manager, para sincronizar dados com uma ou mais florestas locais e/ou fontes de dados não AD. 

Observe também que, ao contrário de outros recursos do Azure, seus diretórios não são recursos filhos de uma assinatura do Azure. Então, se você cancelar ou permitir que a sua assinatura do Azure expire, ainda poderá acessar os dados de diretório usando o PowerShell do Azure AD, o Azure Graph API ou outras interfaces, como o Centro de Administração do Office 365. Você também pode associar outra assinatura ao diretório.

## Como posso excluir um diretório do Azure AD?
Um administrador global pode excluir um diretório do Azure AD a partir do portal. Quando um diretório é excluído, todos os recursos contidos no diretório também serão excluídos; portanto, você deve se certificar de que não precisa do diretório antes de excluí-lo.

> [AZURE.NOTE]Se o usuário estiver conectado com uma conta comercial ou de estudante, ele não deve tentar excluir seu diretório base. Por exemplo, se o usuário estiver conectado como joe@contoso.onmicrosoft.com, esse usuário não poderá excluir o diretório que tiver contoso.onmicrosoft.com como seu domínio padrão.

### Condições que devem ser atendidas para excluir um diretório do Azure AD

O Azure AD requer que determinadas condições sejam atendidas para excluir um diretório. Isso reduz o risco de a exclusão de um diretório afetar negativamente os usuários ou aplicativos, como a capacidade dos usuários de entrar no Office 365 ou acessar recursos no Azure. Por exemplo, se um diretório para uma assinatura for acidentalmente excluído, então, os usuários não poderiam acessar os recursos do Azure para essa assinatura.

As seguintes condições são verificadas:

- O único usuário no diretório é o administrador global, que excluirá o diretório. Quaisquer outros usuários devem ser excluídos antes que o diretório possa ser excluído. Se os usuários estiverem sincronizados no local, então a sincronização precisará ser desativada e os usuários deverão ser excluídos no diretório de nuvem usando o Portal de Gerenciamento ou o módulo do Azure para Windows PowerShell. Não há nenhum requisito de excluir grupos ou contatos, tais como contatos adicionados a partir do Centro de Administração do Office 365.
- Não pode haver nenhum aplicativo no diretório. Qualquer aplicativo deve ser excluído antes que o diretório possa ser excluído. 
- Não pode haver assinaturas para Microsoft Online Services, como o Microsoft Azure, Office 365 ou Azure AD Premium associadas ao diretório. Por exemplo, se um diretório padrão tiver sido criado para você no Azure, você não poderá excluir esse diretório se sua assinatura do Azure ainda depender desse diretório para autenticação. Da mesma forma, você não pode excluir um diretório se outro usuário tiver associado uma assinatura a ele. Para associar sua assinatura a um diretório diferente, entre no Portal de Gerenciamento do Azure e clique em **Configurações** no painel de navegação esquerdo. Em seguida, na parte inferior da página **Assinaturas**, clique em **Editar diretório**. Para obter mais informações sobre as assinaturas do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure AD](active-directory-how-subscriptions-associated-directory.md). 

    > [AZURE.NOTE]Se o usuário estiver conectado com uma conta comercial ou de estudante, ele não deve tentar excluir seu diretório base. Por exemplo, se o usuário estiver conectado como joe@contoso.onmicrosoft.com, esse usuário não poderá excluir o diretório que tiver contoso.onmicrosoft.com como seu domínio padrão.

- Nenhum provedor de autenticação multifator pode ser vinculado ao diretório.


## Recursos adicionais

- [Fórum do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Fórum de autenticação multifator do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Inscrever-se no Azure como uma organização](sign-up-organization.md)
- [Gerenciar o Azure AD usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png


 

<!---HONumber=August15_HO6-->