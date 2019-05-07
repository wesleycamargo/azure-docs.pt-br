---
title: Práticas recomendadas de segurança de identidade e acesso do Azure | Microsoft Docs
description: Este artigo fornece um conjunto de práticas recomendadas para gerenciamento de identidade e controle de acesso usando recursos internos do Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2a669f5b46db4d5de7d1d6863b94e6c117667aee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153235"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Práticas recomendadas de Gerenciamento de Identidade do Azure e segurança de controle de acesso
Neste artigo, abordaremos uma coleção de práticas recomendadas de segurança de controle de acesso e gerenciamento de identidades do Azure. Essas práticas recomendadas derivam da nossa experiência com o [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:

* O que é a prática recomendada
* Por que é ideal habilitar essa prática recomendada
* O que poderá acontecer se você não habilitar a prática recomendada
* Possíveis alternativas à prática recomendada
* Como você pode aprender a habilitar a prática recomendada

Este artigo sobre práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure se baseia em um consenso e nos recursos e conjuntos de recursos da plataforma Azure existentes quando este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As práticas recomendadas de segurança de controle de acesso e gerenciamento de identidade do Azure discutidas neste artigo incluem:

* Tratar identidade como o perímetro de segurança primário
* Centralizar o gerenciamento de identidade
* Gerenciar Locatários conectados
* Habilitar logon único
* Ativar acesso condicional
* Habilitar o gerenciamento de senhas
* Impor a verificação de autenticação multifator para usuários
* Use o controle de acesso baseado em função
* Exposição menor de contas privilegiadas
* Controlar os locais onde os recursos estão localizados
* Usar o Azure AD para autenticação de armazenamento

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Tratar identidade como o perímetro de segurança primário

Muitos consideram que a identidade seja primário perímetro de segurança. Essa é uma mudança de enfoque tradicional de segurança de rede. Perímetros de rede cada vez mais porosos e a defesa do perímetro não pode ser tão eficiente quanto era antes da explosão da [BYOD](https://aka.ms/byodcg) dispositivos e aplicativos de nuvem.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) é a solução do Azure para gerenciamento de identidades e acesso. O Azure AD é um multilocatário baseado em nuvem, identidade e diretório de serviço de gerenciamento da Microsoft. Combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução.

As seções a seguir listam as práticas recomendadas de segurança de acesso e identidade usando o Microsoft Azure Active Directory.

## <a name="centralize-identity-management"></a>Centralizar o gerenciamento de identidade

Em um cenário de [identidade híbrida](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?), recomendamos que você integre seus locais e diretórios na nuvem. Integração permite que sua equipe de TI gerenciar contas de um local, independentemente de onde uma conta é criada. Integração também ajuda os usuários a serem mais produtivos fornecendo uma identidade comum para acessar os recursos de nuvem e locais.

**Melhor prática**: Estabelecer uma única instância do AD do Azure. Consistência e um único fontes autoritativas aumentará a clareza e reduzir os riscos de segurança de erros humanos e complexidade da configuração.
**Detalhe**: Designar um único Azure AD diretório como a fonte autoritativa para contas corporativas e organizacionais.

**Melhor prática**: Integrar seus diretórios locais ao Azure AD.  
**Detalhe**: Use o [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) para sincronizar seu diretório local com seu diretório na nuvem.

> [!Note]
> Há [fatores que afetam o desempenho do Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Certifique-se de que o Azure AD Connect tem capacidade suficiente para manter com baixo desempenho sistemas de segurança impedindo e produtividade. As organizações grandes ou complexas (organizações de provisionamento mais de 100.000 objetos) devem seguir a [recomendações](../active-directory/hybrid/whatis-hybrid-identity.md) para otimizar sua implementação do Azure AD Connect.

**Melhor prática**: Não sincronize contas para o Azure AD com altos privilégios na sua instância do Active Directory existente.
**Detalhe**: Não altere o padrão [configuração do Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) que filtra essas contas. Essa configuração minimiza o risco dos adversários dinamização da nuvem aos ativos locais (que poderia criar um incidente de grande).

**Melhor prática**: Ativar a sincronização de hash de senha.  
**Detalhe**: Sincronização de hash de senha é um recurso usado para sincronizar hashes de senha do usuário de uma instância do Active Directory local para um Azure baseado em nuvem instância do AD. Esta sincronização ajuda a proteger contra credenciais vazadas sendo repetidas contra ataques anteriores.

Mesmo se você decidir usar a federação com os serviços de Federação do Active Directory (AD FS) ou outros provedores de identidade, você pode definir opcionalmente a sincronização de hash de senha como um backup no caso de seus servidores locais falhem ou fiquem temporariamente indisponíveis. Esta sincronização permite que os usuários se conectem ao serviço usando a mesma senha que eles usam para entrar em sua instância local do Active Directory. Ele também permite a proteção de identidade detectar credenciais comprometidas, comparando os hashes de senha sincronizada com senhas conhecidas como compormetidas, se um usuário tiver usado o mesmo endereço de email e a senha em outros serviços que não estão conectados ao Azure AD.

Para obter mais informações, consulte [Implementar a sincronização de senha com a sincronização do Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Melhor prática**: Para o desenvolvimento de novos aplicativos, use o Azure AD para autenticação.
**Detalhe**: Use os recursos corretos para dar suporte à autenticação:

  - Azure AD para funcionários
  - [B2B do AD do Azure](https://docs.microsoft.com/azure/active-directory/b2b/) para os usuários convidados e parceiros externos
  - [B2C do Azure AD](https://docs.microsoft.com/azure/active-directory-b2c/) para controlar como os clientes se inscrever, entrar e gerenciam seus perfis ao usarem seus aplicativos

Organizações que não integram suas identidades locais com sua identidade de nuvem podem ter mais sobrecarga no gerenciamento de contas. Essa sobrecarga aumenta a probabilidade de erros e violações de segurança.

> [!Note]
> Você precisa escolher quais diretórios críticos contas residirá no e processa se a estação de trabalho do administrador usada é gerenciado pelos serviços de nuvem novo ou existente. Usando processos de provisionamento de identidade e de gerenciamento existente pode reduzir alguns riscos, mas também pode criar o risco de um invasor comprometer uma conta local e na nuvem a dinamização. Talvez você queira usar uma estratégia diferente para funções diferentes (por exemplo, os administradores de TI versus administradores de unidade de negócios). Você tem duas opções. Primeira opção é criar contas do AD do Azure que não estão sincronizadas com a instância do Active Directory local. Junte-se a estação de trabalho do administrador para o Azure AD, que você pode gerenciar e aplicar patches usando o Microsoft Intune. Segunda opção é usar contas de administrador existente com a sincronização para a instância do Active Directory local. Use estações de trabalho existentes em seu domínio do Active Directory para gerenciamento e segurança.

## <a name="manage-connected-tenants"></a>Gerenciar Locatários conectados
Sua organização de segurança precisar de visibilidade para avaliar o risco e determinar se as políticas da sua organização e os requisitos regulamentares, estão sendo seguidas. Você deve garantir que sua organização de segurança tenha visibilidade sobre todas as assinaturas conectado à sua rede e o ambiente de produção (via [do Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Um [administrador de empresa/Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) no Azure AD podem elevar o acesso para o [administrador de acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) função e ver todas as assinaturas e grupos gerenciados, conectados ao seu ambiente.

Ver [elevar o acesso para gerenciar todas as assinaturas do Azure e grupos de gerenciamento](../role-based-access-control/elevate-access-global-admin.md) garantir que você e seu grupo de segurança podem visualizar todas as assinaturas ou grupos de gerenciamento conectado ao seu ambiente. Após a avaliação de riscos, você deve remover esse acesso com privilégios elevados.

## <a name="enable-single-sign-on"></a>Habilitar logon único

Em um mundo de dispositivos móveis e nuvem em primeiro lugar, você deseja habilitar o logon único (SSO) para dispositivos, aplicativos e serviços de qualquer lugar para que os usuários possam ser produtivos sempre. Quando você tiver várias soluções de identidade para gerenciar, isso se torna um problema administrativo não apenas para TI, mas também para os usuários que precisem se lembrar de várias senhas.

Ao usar a mesma solução de identidade para todos os aplicativos e recursos, você pode obter o SSO. E seus usuários podem usar o mesmo conjunto de credenciais para entrar e acessar os recursos que eles precisam, se os recursos estiverem localizados no local ou na nuvem.

**Melhor prática**: Habilitar SSO.  
**Detalhe**: O Azure AD [estende o Active Directory local](../active-directory/connect/active-directory-aadconnect.md) para a nuvem. Os usuários podem usar sua conta escolar ou corporativa primária para seus dispositivos ingressados no domínio, os recursos da empresa e todos os aplicativos de SaaS que eles precisam para realizar seu trabalho e web. Os usuários não precisam se lembrar de vários conjuntos de nomes de usuário e senhas e o acesso do aplicativo pode ser automaticamente provisionado (ou desprovisionado) com base em suas associações de grupo da organização e seu status como funcionário. E você pode controlar o acesso para aplicativos da galeria ou para seus próprios aplicativos locais que você já desenvolveu e publicado por meio de [com o Proxy de aplicativo Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Use o SSO para permitir que os usuários acessem seus [aplicativos de SaaS](../active-directory/active-directory-appssoaccess-whatis.md) com base em sua conta corporativa ou de estudante no Microsoft Azure Active Directory. Isso é aplicável não apenas a aplicativos de SaaS da Microsoft, mas também a outros aplicativos, como [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). É possível configurar seu aplicativo para usar o Microsoft Azure Active Directory como uma [identidade baseada em um servidor SAML](../active-directory/fundamentals-identity.md). Como um controle de segurança do Azure AD emite um token que permite aos usuários entrar no aplicativo, a menos que eles receberam acesso por meio do Microsoft Azure Active Directory. Você pode conceder acesso diretamente ou por meio de um grupo do qual fazem parte.

As organizações que não criam uma identidade comum para estabelecer SSO para seus usuários e aplicativos estão mais expostas a cenários em que os usuários têm várias senhas. Nesses cenários aumentam a probabilidade de reutilização de senhas ou usar senhas fracas.

## <a name="turn-on-conditional-access"></a>Ativar acesso condicional

Os usuários podem acessar os recursos da organização usando uma grande variedade de dispositivos e aplicativos de qualquer lugar. Como um administrador de TI, você deseja certificar-se de que esses dispositivos atendem aos padrões de segurança e conformidade. Por causa disso, concentrar-se apenas em quem pode acessar um recurso que não é mais suficiente.

Para balancear a segurança e a produtividade, você precisa pensar sobre como um recurso é acessado antes que você pode tomar uma decisão sobre o controle de acesso. Com acesso condicional do Azure AD, você pode atender a esse requisito. Com acesso condicional, você pode tomar decisões de controle de acesso automatizado com base nas condições para acessar seus aplicativos de nuvem.

**Melhor prática**: Gerenciar e controlar o acesso aos recursos corporativos.  
**Detalhe**: Configure o [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md) do Azure AD com base em um grupo, localização e sensibilidade de aplicativo para aplicativos SaaS e aplicativos conectados ao Azure AD.

**Melhor prática**: Bloquear protocolos de autenticação herdados.
**Detalhe**: Os invasores exploram pontos fracos em protocolos mais antigos todos os dias, particularmente para ataques de senha de spray. Configure o acesso condicional para bloquear protocolos herdados. Consulte o vídeo [do Azure AD: Regras de](https://www.youtube.com/watch?v=wGk0J4z90GI) para obter mais informações.

## <a name="enable-password-management"></a>Habilitar o gerenciamento de senhas

Se você tiver vários locatários ou deseja permitir que os usuários [redefinam suas próprias senhas](../active-directory/active-directory-passwords-update-your-own-password.md), é importante que você use as políticas de segurança apropriadas para evitar abusos.

**Melhor prática**: Configurar SSPR (redefinição de senha self-service) para seus usuários.  
**Detalhe**: Use o recurso de [redefinição de senha self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) do Azure AD.

**Melhor prática**: Monitorar como ou se o SSPR realmente está sendo usado.  
**Detalhe**: Monitore os usuários que estão se registrando com o uso do [relatório de atividade de registro de redefinição de senha](../active-directory/active-directory-passwords-get-insights.md) do Azure AD. O recurso de relatório do Microsoft Azure AD fornece ajuda a responder perguntas usando relatórios predefinidos. Se você estiver licenciado adequadamente, também poderá criar consultas personalizadas.

**Melhor prática**: Estenda as políticas de senha com base em nuvem à sua infraestrutura local.
**Detalhe**: Aprimore as políticas de senha em sua organização, executando as mesmas verificações para alterações de senha local, como faria para alterações de senha com base em nuvem. Instale [proteção por senha do Azure AD](../active-directory/authentication/concept-password-ban-bad.md) para Windows Server Active Directory agentes locais estender as listas de senhas em sua infraestrutura existente. Os usuários e administradores que alterar, definir ou redefinir as senhas locais são necessários para estar em conformidade com a mesma política de senha que os usuários somente na nuvem.

## <a name="enforce-multi-factor-verification-for-users"></a>Impor a verificação de autenticação multifator para usuários

É recomendável que você exija a verificação em duas etapas para todos os seus usuários. Isso inclui os administradores e outros em sua organização que pode ter um impacto significativo se sua conta for comprometida (por exemplo, gerentes financeiros).

Há várias opções para exigir a verificação em duas etapas. A melhor opção para você depende de suas metas, a edição do Microsoft Azure Active Directory que você está executando e o programa de licenciamento. Consulte [Como exigir verificação em duas etapas para um usuário](../active-directory/authentication/howto-mfa-userstates.md) para determinar a melhor opção para você. Consulte a[Microsoft Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) e [Autenticação Multifator do Microsoft Azure](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) páginas de preço para obter mais informações sobre as licenças de preço e preços.

Estas são as opções e benefícios para habilitar a verificação em duas etapas:

**Opção 1**: [Habilitar a Autenticação Multifator alterando o estado do usuário](../active-directory/authentication/howto-mfa-userstates.md).   
**Benefício**: Esse é o método tradicional para exigir a verificação em duas etapas. Funciona com ambos [Autenticação Multifator do Azure na nuvem e o Servidor de Autenticação Multifator do Microsoft Azure](../active-directory/authentication/concept-mfa-whichversion.md). O uso desse método exigirá que os usuários realizem a verificação em duas etapas sempre que entrarem e substituírem as políticas de acesso condicional.

Para determinar onde a autenticação multifator precisa ser habilitado, consulte [qual versão do MFA do Azure é ideal para minha organização?](../active-directory/authentication/concept-mfa-whichversion.md).

**Opção 2**: [Habilitar a Autenticação Multifator com a política de acesso condicional](../active-directory/authentication/howto-mfa-getstarted.md).
**Benefício**: Essa opção permite que você solicite a verificação em duas etapas sob condições específicas usando o [acesso condicional](../active-directory/active-directory-conditional-access-azure-portal.md). As condições específicas podem ser entrada do usuário de locais diferentes, não confiável de dispositivos ou aplicativos que você considere arriscadas. Definir condições específicas em que você exige verificação em duas etapas permite que você evite solicitação constante para seus usuários, que podem ser uma experiência de usuário desagradáveis.

Essa é a maneira mais flexível para habilitar a verificação em duas etapas para seus usuários. Habilitar uma política de acesso condicional funciona apenas para autenticação multifator do Azure na nuvem e é um recurso premium do Microsoft Azure Active Directory. Você pode encontrar mais informações sobre esse método em [implantar autenticação de multifator do Azure baseado em nuvem](../active-directory/authentication/howto-mfa-getstarted.md).

**Opção 3**: Habilitar a Autenticação Multifator com políticas de acesso condicional ao avaliar o usuário e o risco de entrada do [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Benefício**: Essa opção permite que você:

- Detecte possíveis vulnerabilidades que afetam as identidades da organização.
- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização.
- Investigar incidentes suspeitos e tomar as devidas providências para resolvê-los.

Este método usa a avaliação de risco do Azure AD Identity Protection para determinar se a verificação em duas etapas é necessária com base no usuário e risco de entrada para todos os aplicativos de nuvem. Este método requer o licenciamento do Azure Active Directory P2. Você pode encontrar mais informações sobre esse método no [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> A opção 1, que habilita a Autenticação Multifator do Microsoft Azure alterando o estado do usuário, substitui as políticas de acesso condicionais. Como opções 2 e 3 usam políticas de acesso condicional, você não pode usar a opção 1 com eles.

As organizações que não adicionam camadas adicionais de proteção de identidade, como verificação em duas etapas, são mais suscetíveis a ataques de roubo de credencial. Um ataque de roubo de credencial pode levar ao comprometimento de dados.

## <a name="use-role-based-access-control"></a>Use o controle de acesso baseado em função
Gerenciamento de acesso para recursos de nuvem é fundamental para qualquer organização que usa a nuvem. [Controle de acesso baseado em função (RBAC)](../role-based-access-control/overview.md) ajuda você a gerenciar quem tem acesso aos recursos do Azure, o que podem fazer com esses recursos e quais áreas eles têm acesso.

Como designar grupos ou funções individuais responsáveis por funções específicas do Azure ajuda a evitar confusão que pode levar a humana e erros de automação que criar riscos de segurança. Restringir o acesso com base nos princípios de segurança de [divulgação restrita àqueles diretamente interessados](https://en.wikipedia.org/wiki/Need_to_know) e no [privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é fundamental para as organizações que desejam impor políticas de segurança para acesso a dados.

Sua equipe de segurança precisa visibilidade sobre os recursos do Azure para avaliar e remediar o risco. Se a equipe de segurança tem responsabilidades operacionais, eles precisam de permissões adicionais para realizar seus trabalhos.

Você pode usar [RBAC](../role-based-access-control/overview.md) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso.

**Melhor prática**: Separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso a usuários que precisam para realizar seus trabalhos. Em vez de dar a todos permissões irrestritas na sua assinatura do Azure ou recursos, permitir apenas determinadas ações em um escopo específico.
**Detalhe**: Use [funções RBAC internas](../role-based-access-control/built-in-roles.md) no Azure para atribuir privilégios aos usuários.

> [!Note]
> Permissões específicas é criar uma complexidade desnecessária e a confusão, acumular em uma configuração de "herdada" que é difícil de corrigir sem medo de quebrar alguma coisa. Evite permissões específicas do recurso. Em vez disso, use grupos de gerenciamento para toda a empresa permissões e grupos de recursos para permissões em assinaturas. Evite permissões específicas do usuário. Em vez disso, atribua o acesso a grupos no Azure AD.

**Melhor prática**: Conceda as equipes com acesso de responsabilidades do Azure para ver os recursos do Azure para que possam avaliar e remediar o risco de segurança.
**Detalhe**: Conceda as equipes de segurança o RBAC [leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader) função. Você pode usar o grupo de gerenciamento raiz ou o grupo de gerenciamento do segmento, dependendo do escopo das responsabilidades:

- **Grupo de gerenciamento raiz** para as equipes responsáveis por todos os recursos da empresa
- **Grupo de gerenciamento do segmento** para equipes com escopo limitado (geralmente devido a limites organizacionais de normas ou outros)

**Melhor prática**: Conceda as permissões apropriadas para as equipes de segurança que tenham responsabilidades operacionais diretas.
**Detalhe**: Examine as funções internas de RBAC para a atribuição de função apropriada. Se as funções internas não atenderem às necessidades específicas da sua organização, você pode criar [funções personalizadas para recursos do Azure](../role-based-access-control/custom-roles.md). Como com as funções internas, você pode atribuir funções personalizadas para usuários, grupos e entidades de serviço na assinatura, grupo de recursos e escopos de recursos.

**Práticas recomendadas**: Acesso de Grant a Central de segurança do Azure para funções de segurança que precisam dela. A Central de segurança permite que as equipes de segurança identificar e corrigir riscos rapidamente.
**Detalhe**: Adicione as equipes de segurança com essas necessidades para o RBAC [administrador de segurança](../role-based-access-control/built-in-roles.md#security-admin) função para que eles podem exibir as políticas de segurança, estados de segurança, editar as políticas de segurança, exibir alertas e recomendações e ignorar alertas e recomendações. Você pode fazer isso usando o grupo de gerenciamento raiz ou o grupo de gerenciamento do segmento, dependendo do escopo das responsabilidades.

Organizações que não impõem o controle de acesso de dados usando recursos como o RBAC pode estar dando mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento dos dados, permitindo que os usuários a acessar os tipos de dados (por exemplo, alto impacto nos negócios) que não deveriam ter.

## <a name="lower-exposure-of-privileged-accounts"></a>Exposição menor de contas privilegiadas

Proteger com privilégios o acesso é a primeira etapa crítica para proteger os ativos de negócios. Minimizando o número de pessoas que têm acesso a informações seguras ou recursos reduz a chance de um usuário mal-intencionado obter acesso, ou um usuário autorizado afetar acidentalmente um recurso confidencial.

As contas privilegiadas são aquelas que administram e gerenciam sistemas de TI. Os invasores virtuais visam essas contas para obter acesso aos sistemas e aos dados de uma organização. Para proteger o acesso privilegiado, isole as contas e os sistemas do risco de exposição a um usuário mal-intencionado.

É recomendável que você desenvolva e execute um roteiro para proteger o acesso privilegiado contra invasores virtuais. Para obter informações sobre como criar um roteiro detalhado para proteger as identidades e acesso que são gerenciados ou relatado no Microsoft Azure AD Microsoft Azure, Office 365 e outros serviços de nuvem, examine [acesso privilegiado de proteção para implantações de nuvem e híbridos no Microsoft Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

A seguir, um resumo das práticas recomendadas encontradas na [proteção de acesso privilegiado para implantações de nuvem e híbridos no Microsoft Azure Active Directory](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Melhor prática**: Gerenciar, controlar e monitorar o acesso a contas privilegiadas.   
**Detalhe**: Ative o [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Depois de ativar o Privileged Identity Management, você receberá mensagens de notificação por email para alterações da função de acesso privilegiado. Essas notificações fornecem aviso antecipado quando usuários adicionais são adicionados às funções altamente privilegiadas no seu diretório.

**Melhor prática**: Verifique se todas as contas de administrador críticos são gerenciadas contas do Azure AD.
**Detalhe**: Remova as contas de consumidor críticos funções de administrador (por exemplo, contas da Microsoft, como hotmail.com, live.com e outlook.com).

**Melhor prática**: Certifique-se de que todas as funções de administrador crítica ter uma conta separada para tarefas administrativas para evitar o phishing e outros ataques de comprometer os privilégios administrativos.
**Detalhe**: Crie uma conta de administrador separada que tem os privilégios necessários para executar as tarefas administrativas de atribuído. Bloquear o uso dessas contas administrativas para as ferramentas de produtividade diários, como o email do Microsoft Office 365 ou a navegação na web arbitrária.

**Melhor prática**: Identificar e categorizar as contas que estão em funções altamente privilegiadas.   
**Detalhe**: Depois de ativar o Azure AD Privileged Identity Management, exiba os usuários que estão no administrador global, administrador de função com privilégios e outras funções altamente privilegiadas. Remova as contas que não são mais necessárias nessas funções e categorize as contas que são atribuídas a funções de administrador:

- Atribuídas individualmente a usuários administrativos e podem ser usadas para fins não administrativos (por exemplo, email pessoal)
- Individualmente atribuídas para usuários administrativos e designadas para fins administrativos apenas
- Compartilhada por vários usuários
- Para cenários de acesso de emergência
- Para scripts automatizados
- Para usuários externos

**Melhor prática**: Implementar acesso JIT ("Just-In-Time") para reduzir ainda mais o tempo de exposição de privilégios e aumentar sua visibilidade sobre o uso de contas privilegiadas.   
**Detalhe**: O Gerenciamento de identidades com privilégios do AD do Azure:

- Limitar os usuários a assumir seus privilégios JIT.
- Atribua funções por uma duração reduzida com confiança de que os privilégios são revogados automaticamente.

**Melhor prática**: Definir pelo menos duas contas de acesso de emergência.   
**Detalhe**: Contas de acesso de emergência ajudam as organizações a restringir o acesso privilegiado em um ambiente existente do Azure Active Directory. Essas contas são altamente privilegiadas e não são atribuídas a indivíduos específicos. Contas de acesso de emergência são limitadas a cenários em que as contas administrativas normais não podem ser usadas. As organizações devem limitar o uso da conta de emergência para somente a quantidade necessária de tempo.

Avalie as contas que são atribuídas ou qualificadas para a função de administrador global. Se você não vir nenhuma conta somente na nuvem usando o `*.onmicrosoft.com` domínio (usado para acesso de emergência), crie-os. Para obter mais informações, consulte [Gerenciamento de contas administrativas de acesso de emergência no Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Melhor prática**: Ter um processo de "vigilância" em vigor em caso de emergência.
**Detalhe**: Siga as etapas em [proteção de acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Melhor prática**: Exigir que todas as contas de administrador crítico seja sem senha (preferencial) ou exigir a autenticação multifator.
**Detalhe**: Use o [o aplicativo Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) para entrar em qualquer conta do Azure AD sem usar uma senha. Como o [Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), o Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário que estiver associada a um dispositivo e usa a autenticação biométrica ou um PIN.

Exigir autenticação multifator do Azure na entrada para todos os usuários individuais que são atribuídos permanentemente a uma ou mais das funções de administrador do AD do Azure: Administrador global, administrador de função com privilégios, administrador do Exchange Online e administrador do SharePoint Online. Habilitar [a autenticação multifator para suas contas de administrador](../active-directory/authentication/howto-mfa-userstates.md) e certifique-se de que os usuários de conta de administrador tenham registrado.

**Melhor prática**: Para contas de administrador críticos, ter uma estação de trabalho administrativa onde as tarefas de produção não são permitidas (por exemplo, navegação e email). Isso protegerá suas contas de administrador de vetores de ataque que usam a navegação e o email e reduzir significativamente o risco de um incidente de grande.
**Detalhe**: Use uma estação de trabalho do administrador. Escolha um nível de segurança da estação de trabalho:

- Dispositivos de produtividade altamente segura fornecem segurança avançada para navegação e outras tarefas de produtividade.
- [Privilegiado (PAWs) as estações de trabalho de acesso](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) fornecem um sistema operacional dedicado que é protegido contra ataques da internet e vetores de ameaça para as tarefas confidenciais.

**Melhor prática**: Cancelar o provisionamento de contas de administrador quando os funcionários saírem de sua organização.
**Detalhe**: Ter um processo em vigor que desativa ou exclui contas de administrador quando os funcionários saírem de sua organização.

**Melhor prática**: Teste regularmente as contas de administrador por meio de técnicas de ataque atual.
**Detalhe**: Use o simulador de ataque do Office 365 ou um terceiro propondo a executar cenários de ataque realista em sua organização. Isso pode ajudá-lo a localizar usuários vulneráveis antes que ocorra um ataque real.

**Melhor prática**: Realizar etapas para diminuir as técnicas de ataque usadas com mais frequência.  
**Detalhe**: [Identifique as contas da Microsoft em funções administrativas que precisam ser alternadas para contas corporativas ou de estudante](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Garanta contas de usuário separadas e emails de encaminhamento para as contas de administrador global](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Certifique-se de que as senhas de contas administrativas foram alteradas recentemente](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Ativar a sincronização de hash de senha](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Exigir Autenticação Multifator do Microsoft Azure para usuários em todas as funções privilegiadas, bem como os usuários expostos](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Obter o Office 365 Secure Score (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Verifique as diretrizes de segurança e conformidade do Office 365 (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configure o Office 365 Activity Monitoring (se estiver usando o Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Estabelecer os proprietários de plano de resposta de incidente/emergência](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Continuar a proteger as contas administrativas privilegiadas locais](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Se você não proteger o acesso privilegiado, talvez você têm muitos usuários em funções altamente privilegiadas e são mais vulneráveis a ataques. Atores mal-intencionados, incluindo os invasores virtuais, contas de administrador de destino com frequência e outros elementos de acesso privilegiado para obter acesso a dados confidenciais e sistemas usando o roubo de credenciais.

## <a name="control-locations-where-resources-are-created"></a>Controlar os locais onde os recursos estão localizados

É muito importante habilitar os operadores de nuvem a executar tarefas, mas impedi-los de romper convenções que são necessárias para gerenciar recursos de sua organização. As organizações que desejam controlar os locais em que os recursos são criados devem codificar esses locais.

Você pode usar [o Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar políticas de segurança cujas definições descrevem as ações ou os recursos que são especificamente negados. Atribua essas definições de política no escopo desejado, como a assinatura, grupo de recursos ou um recurso individual.

> [!NOTE]
> As políticas de segurança não são o mesmo que o RBAC. Na verdade, usam o RBAC para autorizar usuários a criar esses recursos.
>
>

As organizações que não estão controlando como os recursos são criados são mais suscetíveis a usuários que podem usar o serviço de maneira imprópria, criando mais recursos do que o necessário. Proteger o processo de criação de recursos é uma etapa importante para proteger um cenário de multilocatário.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorar ativamente as atividades suspeitas

Uma sistema de monitoramento de identidade Active Directory pode detectar o comportamento suspeito e disparar um alerta para uma investigação mais aprofundada rapidamente. A tabela a seguir lista os dois recursos do Azure Active Directory que podem ajudar as organizações a monitorar suas identidades:

**Melhor prática**: Ter um método para identificar:

- Tenta entrar em [sem rastreamento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- [Força bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) ataques contra uma conta específica.
- Tentativas de entrada de vários locais.
- Entradas de [dispositivos infectados](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Entradas de endereços IP suspeitos.

**Detalhe**: Use os [relatórios de anomalia](../active-directory/active-directory-view-access-usage-reports.md) do Azure AD Premium. Ter processos e procedimentos em vigor para que os administradores de TI executem esses relatórios diariamente ou sob demanda (geralmente em um cenário de resposta a incidentes).

**Melhor prática**: Ter um sistema de monitoramento ativo que notifica você a respeito dos riscos e pode ajustar o nível de risco (alto, médio ou baixo) a seus requisitos de negócios.   
**Detalhe**: Use o [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), que sinaliza os riscos atuais em seu próprio painel e envia notificações com resumos diárias por email. Para ajudar a proteger as identidades da organização, você pode configurar políticas de risco que respondem automaticamente a problemas detectados quando um nível de risco especificado for atingido.

As organizações que não monitoram ativamente os seus sistemas de identidade estão em risco de ter as credenciais de usuário comprometidas. Sem o conhecimento que atividades suspeitas estão ocorrendo através dessas credenciais, as organizações não podem reduzir esse tipo de ameaça.

## <a name="use-azure-ad-for-storage-authentication"></a>Usar o Azure AD para autenticação de armazenamento
[O armazenamento do Azure](../storage/common/storage-auth-aad.md) dá suporte à autenticação e autorização com o Azure AD para o armazenamento de BLOBs e armazenamento de filas. Autenticação do Azure AD, você pode usar o controle de acesso baseado em função do Azure para conceder permissões específicas a usuários, grupos e aplicativos para baixo para o escopo de um contêiner de blob individuais ou a fila.

É recomendável que você use [Azure AD para autenticar o acesso ao armazenamento](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Próxima etapa

Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.
