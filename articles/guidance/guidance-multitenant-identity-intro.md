<properties
   pageTitle="Gerenciamento de identidade para aplicativos multilocatário | Microsoft Azure"
   description="Introdução ao gerenciamento de identidades em aplicativos multilocatário"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Gerenciamento de identidade para aplicativos multilocatário: Introdução

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

Digamos que você esteja gravando um aplicativo SaaS corporativo para ser hospedado na nuvem. É óbvio que o aplicativo terá usuários:

![Usuários](media/guidance-multitenant-identity/users.png)

Mas esses usuários pertencem a organizações:

![Usuários da organização](media/guidance-multitenant-identity/org-users.png)

Exemplo: a Tailspin vende assinaturas para seu aplicativo SaaS. A Contoso e a Fabrikam inscrevem-se no aplicativo. Quando Alice (`alice@contoso`) entra, o aplicativo deve saber que Alice faz parte da Contoso.

- Alice _deve_ ter acesso aos dados da Contoso.
- Alice _não deve_ ter acesso aos dados da Fabrikam.

Este guia mostrará como gerenciar identidades de usuário em um aplicativo multilocatário usando o [Azure Active Directory][AzureAD] (Azure AD) para manipular a entrada e a autenticação.

## O que é a multilocação?

Um _locatário_ é um grupo de usuários. Em um aplicativo SaaS, o locatário é um assinante ou o cliente do aplicativo. _Multilocação_ é uma arquitetura onde vários locatários compartilham a mesma instância física do aplicativo. Embora locatários compartilhem recursos físicos (como VMs ou armazenamento), cada locatário obtém sua própria instância lógica do aplicativo.

Normalmente, os dados de aplicativo são compartilhados entre os usuários dentro de um locatário, mas não com outros locatários.

![Multilocatário](media/guidance-multitenant-identity/multitenant.png)

Compare essa arquitetura com uma arquitetura de locatário único, onde cada locatário tem uma instância física dedicada. Em uma arquitetura de locatário único, você pode adicionar locatários gerando novas instâncias do aplicativo.

![Locatário único](media/guidance-multitenant-identity/single-tenant.png)

### Multilocação e dimensionamento horizontal

É comum adicionar mais instâncias para dimensionar na nuvem. Isso é conhecido como _dimensionamento horizontal_ ou _expansão_. Considere um aplicativo Web. Para lidar com mais tráfego, você pode adicionar mais VMs de servidor e colocá-las atrás de um balanceador de carga. Cada VM executa uma instância física separada do aplicativo Web.

![Balancear a carga de um site](media/guidance-multitenant-identity/load-balancing.png)

Qualquer solicitação pode ser roteada para qualquer instância. Em conjunto, o sistema funciona como uma única instância lógica. Você pode subdividir uma VM ou criar uma nova VM sem afetar os usuários. Nesta arquitetura, cada instância física é multilocatário e você pode dimensionar adicionando mais instâncias. Se uma instância falhar, não deverá afetar nenhum locatário.

## Identidade em um aplicativo multilocatário

Em um aplicativo multilocatário, você deve considerar os usuários no contexto de locatários.

**Autenticação**

- Os usuários entram no aplicativo com suas credenciais da organização. Eles não precisam criar novos perfis de usuário para o aplicativo.
- Os usuários dentro da mesma organização fazem parte do mesmo locatário.
- Quando um usuário faz logon, o aplicativo sabe a qual locatário o usuário pertence.

**Autorização**

- Ao autorizar ações de um usuário (digamos, exibição de um recurso), o aplicativo deve levar em conta o locatário do usuário.
- Os usuários podem ser atribuídos a funções dentro do aplicativo, como "Admin" ou "Usuário Padrão". As atribuições de função devem ser gerenciadas pelo cliente e não pelo provedor de SaaS.

**Exemplo:** Alice, uma funcionária da Contoso, navega para o aplicativo em seu navegador e clica no botão "Entrar". Ela é redirecionada para uma tela de logon onde insere suas credenciais corporativas (nome de usuário e senha). Neste ponto, ele está conectada ao aplicativo como `alice@contoso.com`. O aplicativo também sabe que Alice é um usuário administrador deste aplicativo. Por ser administrador, ela pode ver uma lista de todos os recursos que pertencem à Contoso. No entanto, ela não poderá exibir recursos da Fabrikam pois é admin somente dentro de seu locatário.

Neste guia, vamos examinar especificamente o uso do Azure AD para gerenciamento de identidades.

- Supomos que o cliente armazene seus perfis de usuário no Azure AD (incluindo locatários do Office 365 e Dynamics CRM)
- Os clientes com Active Directory local (AD) podem usar o [Azure AD Connect][ADConnect] para sincronizar o AD local com o Azure AD.

Se um cliente com o AD local não conseguir usar o Azure AD Connect (devido à política de TI corporativa ou por outros motivos), o provedor de SaaS poderá federar com o AD do cliente por meio do AD FS (Serviços de Federação do Active Directory). Essa opção é descrita em [Federar com o AD FS de um cliente].

Este guia não considera outros aspectos da multilocação, como particionamento de dados, configuração por locatário e assim por diante.

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[parte de uma série]: guidance-multitenant-identity.md
[Federar com o AD FS de um cliente]: guidance-multitenant-identity-adfs.md
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->