---
title: Delegar funções de administrador no Azure Active Directory | Microsoft Docs
description: Modelos de delegação, exemplos e segurança de função no Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/09/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: ad772a2e0c036c30aca2918496ac01f31157fc64
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "40209194"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegar administração no Azure Active Directory

Junto com o crescimento organizacional vem a complexidade adicional, sendo que uma resposta comum é reduzir parte da sobrecarga do gerenciamento de acesso com as funções de administrador do Azure Active Directory (AD). É possível atribuir os menores privilégios possíveis para os usuários acessarem seus aplicativos e realizarem suas tarefas. Talvez você não queira atribuir a função de Administrador Global para cada proprietário do aplicativo, mas a compensação pode ser que você force responsabilidades de gerenciamento de aplicativo nos Administradores Globais existentes. Há muitas razões para mudar uma organização rumo a uma administração mais descentralizada. Este artigo pode ajudá-lo a planejar a delegação em sua organização.

<!--What about reporting? Who has which role and how do I audit?-->


## <a name="centralized-versus-delegated-permissions"></a>Permissões centralizadas versus delegadas

À medida que uma organização cresce, pode ser difícil de controlar quais usuários têm funções administrativas específicas. Uma organização pode estar sujeita a violações de segurança se um funcionário tem direitos de administrador que não deveria ter. Em geral, o número de administradores e a granularidade de suas permissões dependem do tamanho e da complexidade da implantação.

* Em implantações pequenas ou de prova de conceito, um ou alguns administradores fazer tudo; não há nenhuma delegação. Nesse caso, crie cada administrador com a função de Administrador Global.
* Em implantações maiores com mais computadores, aplicativos e áreas de trabalho, mais delegação é necessária. Vários administradores podem ter responsabilidades funcionais (funções) mais específicas. Por exemplo, alguns podem ser Administradores de Identidades Privilegiadas e outras podem ser Administradores de Aplicativos. Além disso, um administrador pode gerenciar apenas determinados grupos de objetos, como dispositivos.
* Implantações ainda maiores podem exigir permissões ainda mais granulares, além de possivelmente administradores com funções híbridas ou não convencionais.

No portal do Azure AD, é possível [exibir todos os membros de qualquer função](directory-manage-roles-portal.md), o que pode ajudá-lo a verificar rapidamente a implantação e as permissões delegadas.

Se você estiver interessado em delegar acesso aos recursos do Azure e não acesso administrativo no Azure AD, consulte [Atribuir uma função RBAC (controle de acesso baseado em função)](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planejamento de delegação

Como o obstáculo é desenvolver um modelo de delegação que atenda às necessidades exclusivas de sua organização, a verdade é que existem modelos muito simples que podem ser aplicados com pouca modificação. Desenvolver um modelo de delegação é um processo de design iterativo e sugerimos que você siga estas etapas:

* Definir as funções necessárias
* Delegar a administração de aplicativos
* Conceder a capacidade de registrar aplicativos
* Delegar a propriedade de aplicativos
* Desenvolver um plano de segurança
* Estabelecer contas de emergência
* Proteger as funções de administrador
* Tornar temporária a elevação de privilégios

## <a name="define-roles"></a>Definir funções

Determine quais tarefas do Active Directory são realizadas por administradores e como essas tarefas são mapeadas para as funções. Por exemplo, a criação de sites do Active Directory é uma tarefa de administração de serviço, enquanto a modificação de associação de grupo de segurança geralmente se enquadra na administração de dados. É possível [exibir as descrições detalhada da função ](directory-manage-roles-portal.md) no portal do Azure.

Cada tarefa deve ser avaliada por frequência, importância e dificuldade. Esses aspectos são vitais da definição de tarefa, porque decidem se uma permissão deve ser delegada. Tarefas que são executadas rotineiramente têm risco limitado, e cuja conclusão é trivial, são excelentes candidatas para delegação. Por outro lado, as tarefas que são executadas raramente, mas têm um grande impacto em toda a organização, exigindo altos níveis de habilidade, devem ser consideradas com muito cuidado antes de delegar. Em vez disso, é possível [elevar temporariamente uma conta para a função requerida](../active-directory-privileged-identity-management-configure.md) ou reatribuir a tarefa.

## <a name="delegate-app-administration"></a>Delegar a administração de aplicativos

A proliferação de aplicativos dentro de sua organização pode sobrecarregar o modelo de delegação. Se a organização coloca a responsabilidade sobre o gerenciamento do acesso de aplicativos no Administrador Global, é provável que esse modelo aumente sua sobrecarga com o passar do tempo. Caso tenha concedido a função de Administrador Global para algo como configurar aplicativos corporativos a pessoas, agora você pode descarregá-las para as seguintes funções com menos privilégios. Isso ajuda a melhorar sua postura de segurança e reduz a probabilidade de erros inconvenientes. As funções de administrador de aplicativos com mais privilégios são:

* A função **Administrador de Aplicativos**, que concede a capacidade de gerenciar todos os aplicativos no diretório, incluindo registros, configurações de logon único, licenciamento e atribuições de usuários e grupos, configurações do Proxy de Aplicativo e o consentimento. Ela não concede a capacidade de gerenciar o Acesso Condicional.
* A função **Administrador de Aplicativos de Nuvem**, que concede todas as capacidades do Administrador de aplicativos, exceto que não concede acesso às configurações do Proxy de Aplicativo (porque ele não tem nenhuma permissão no local).### Delegar permissões de proprietário de aplicativo por aplicativo

## <a name="delegate-app-registration"></a>Delegar o registro do aplicativo

Por padrão, todos os usuários podem criar registros do aplicativo. Se você quiser conceder seletivamente a capacidade de criar registros do aplicativo, será necessário definir **Usuários podem registrar aplicativos** como Não nas Configurações de Usuário e, em seguida, atribuir a função de Desenvolvedor de Aplicativos. Essa função concede a capacidade de criar registros de aplicativo somente quando **Usuários podem registrar aplicativos** está desativado. Os Desenvolvedores de Aplicativos também podem dar consentimento para si próprios quando **Usuários podem consentir para aplicativos que acessam dados da empresa em seu nome** é definido como Não. Quando um Desenvolvedor de Aplicativos cria um novo registro de aplicativo, ele é automaticamente adicionado como o primeiro proprietário.

## <a name="delegate-app-ownership"></a>Delegar a propriedade de aplicativos

Para refinar ainda mais a delegação de acesso do aplicativo, é possível atribuir a propriedade para aplicativos empresariais individuais. Isso complementa o suporte existente para atribuir proprietários de registro de aplicativo. A propriedade é atribuída em uma base de aplicativo por empresa na folha aplicativos empresariais. A vantagem é que os proprietários podem gerenciar apenas os aplicativos empresariais que possuem. Por exemplo, é possível atribuir um proprietário para o aplicativo Salesforce e esse proprietário pode gerenciar o acesso e a configuração para o Salesforce e nenhum outro aplicativo. Um aplicativo empresarial pode ter vários proprietários e um usuário pode ser o proprietário de muitos aplicativos empresariais. Há duas funções de proprietário de aplicativo:

* A função **Proprietário de Aplicativo Empresarial** concede a capacidade de gerenciar os aplicativos empresariais que o usuário possui, incluindo configurações de logon único, atribuições de usuários e grupos e adicionar mais proprietários. Ela não concede a capacidade de gerenciar as configurações do Proxy de Aplicativo ou o acesso condicional.
* A função **Proprietário de Registro de Aplicativo** concede a capacidade de gerenciar registros de aplicativo para o aplicativo que o usuário possui, incluindo o manifesto do aplicativo e adicionar mais proprietários.

## <a name="develop-a-security-plan"></a>Desenvolver um plano de segurança

O Azure AD fornece um guia abrangente para planejar e executar um plano de segurança nas funções de administrador do Azure AD, [Acesso privilegiado de proteção para implantações de nuvem e híbridas](directory-admin-roles-secure.md). 

## <a name="establish-emergency-accounts"></a>Estabelecer contas de emergência

Para manter o acesso ao repositório de gerenciamento de identidade quando surgem problemas, prepare contas de acesso de emergência de acordo com [Criar contas administrativas de acesso de emergência](directory-emergency-access.md).

## <a name="secure-your-administrator-roles"></a>Proteger as funções de administrador

Os invasores que tomam o controle de contas com privilégios pode causar enorme danos, então, proteja essas contas em primeiro lugar, usando a [política de acesso de linha de base](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) que está disponível, por padrão, para todos os locatários do Azure AD (em visualização pública). A política impõe a autenticação multifator em contas do Azure AD com privilégios. As seguintes funções do Azure AD são cobertas pela política de linha de base do Azure AD:

* Administrador global
* Administrador do SharePoint
* Administrador do Exchange
* Administrador de acesso condicional
* Administrador de segurança

## <a name="elevate-privilege-temporarily"></a>Elevar o privilégio temporariamente

Para a maioria das atividades diárias, nem todos os usuários necessitam de direitos de administrador global. E nem todos os usuários devem ser permanentemente atribuídos à função de Administrador Global. Quando os usuários não precisam atuar como um Administrador Global, eles devem ativar a atribuição de função no [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) do Azure AD em sua própria conta ou em uma conta administrativa alternativa.

## <a name="next-steps"></a>Próximas etapas

Para obter uma referência para as descrições de função do Azure AD, consulte [Atribuir funções de administrador no Azure AD](directory-assign-admin-roles.md)