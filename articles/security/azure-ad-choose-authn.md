---
title: Escolher o método de autenticação certo para sua solução de identidade híbrida do Azure AD | Microsoft Docs
description: Este guia destina-se a ajudar CEOs, CIOs, CISOs, Arquitetos de identidade principais, arquitetos corporativos e tomadores de decisão de TI e de Segurança responsáveis por escolher um método de autenticação para sua solução de identidade híbrida do Azure AD em organizações de médio a grande porte.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 102fa06be3734fa6993616f752922433ee0dee7f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolhendo o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory 

Este artigo é o primeiro de uma série de artigos para ajudar as organizações a implementar uma solução de identidade híbrida completa do Azure AD. Uma solução de identidade híbrida completa do Azure AD foi descrita como a Estrutura de Transformação Digital de Identidade Híbrida e abrange os resultados de negócios, e o foco das organizações de metas deve ser certificar-se de ter implementado uma solução de identidade híbrida robusta e segura. O primeiro resultado comercial da estrutura explica os requisitos para as organizações protegerem o processo de autenticação quando os usuários acessam aplicativos de nuvem. A primeira meta de negócios no resultado de negócios protegido por autenticação é a capacidade dos usuários de entrar em aplicativos de nuvem usando seus nomes de usuário e senhas locais. Esse processo de entrada e a maneira como os usuários se autenticam torna tudo que está na nuvem possível.

Escolher o método de autenticação correto é a principal preocupação para organizações que desejam migrar seus aplicativos para a nuvem. Essa decisão não deve ser tomada levianamente pelos seguintes motivos:

1. É a primeira decisão para uma organização que deseja se mover para a nuvem. 

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem, controlando o acesso a todos os dados e recursos na nuvem.

3. É a base de todos os outros recursos de segurança avançada e de experiência do usuário no Azure AD.

4. Dificuldade em alterar o método de autenticação depois de implementado.

Com a identidade sendo o novo plano de controle da segurança de TI, a autenticação é a proteção de acesso da organização para o novo mundo de nuvem. As organizações devem se certificar de que o plano de controle de identidade torna sua segurança mais forte e mantém seus aplicativos de nuvem protegidos contra invasores.

### <a name="out-of-scope"></a>Fora do escopo

As organizações que não têm um espaço de diretório local existente não são o foco deste artigo. Normalmente, essas empresas criam identidades somente na nuvem, o que não requer uma solução de identidade híbrida. As identidades somente na nuvem existem apenas na nuvem e não estão associadas a identidades locais correspondentes.  

## <a name="choosing-the-right-authentication-method"></a>Escolhendo o método de autenticação correto

Com a solução de identidade híbrida do Azure AD como o novo plano de controle, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. A implementação do método de autenticação é configurada usando o Azure AD Connect que também provisiona usuários na nuvem. 

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. 

O Azure AD é compatível com os seguintes métodos de autenticação para soluções de identidade híbrida:

### <a name="cloud-authentication"></a>Autenticação na nuvem
Quando você escolhe esse método de autenticação, o Azure AD trata o processo de entrada para usuários. Juntamente com o SSO (logon único) contínuo, os usuários podem entrar em aplicativos de nuvem sem precisar inserir suas credenciais novamente. Com a autenticação na nuvem, é possível escolher entre duas opções: 

**PHS (Sincronização de Hash de Senha)** – a maneira mais simples de habilitar a autenticação para objetos de diretório locais no Azure AD. A Sincronização de Hash de Senha permite que os usuários usem o mesmo nome de usuário e senha que eles usam localmente sem a necessidade de implantar qualquer infraestrutura adicional. Alguns recursos premium do Azure AD, como Proteção de Identidade, requer a Sincronização de Hash de Senha, independentemente de qual método de autenticação foi selecionado.

> [!NOTE] 
> As senhas nunca são armazenadas em texto não criptografado ou são criptografadas com um algoritmo reversível no Azure AD. Para obter mais informações sobre o processo real de Sincronização de Hash de Senha, consulte [Implemente a sincronização de hash de senha com a sincronização do Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**PTA (Autenticação de Passagem)** – Fornece uma validação de senha simples para serviços de autenticação do Azure AD usando um agente de software em execução em um ou mais servidores locais para validar os usuários diretamente com seu Active Directory local garantindo que a validação da senha não aconteça na nuvem. Empresas com um requisito normativo que as impede de ter suas senhas com hash na nuvem e, portanto, não podem usar a Sincronização de Hash de Senha, usariam esse método de autenticação. Para obter mias informações sobre o processo de autenticação de passagem real, consulte [User sign-in with Azure Active Directory Pass-through authentication](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) (Entrada do usuário com autenticação de passagem do Azure Active Directory).

### <a name="federated-authentication"></a>Autenticação federada
Quando você escolhe esse método de autenticação, o Azure AD transmite o processo de autenticação a um sistema de autenticação confiável separado, por exemplo, Serviços de Federação do Active Directory (AD FS) locais para validar a senha do usuário. O sistema de autenticação pode fornecer os requisitos de autenticação adicionais, como a autenticação baseada em cartão inteligente ou uma autenticação multifator de terceiros. Para obter mais informações, consulte [Implantando os Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A seção a seguir ajudará você a decidir qual método de autenticação é adequado para você, usando uma árvore de decisão. Ela ajudará a determinar se você deve implantar autenticação federada ou de nuvem para sua solução de identidade híbrida do Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Árvore de decisão da autenticação do Azure AD

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Considerações detalhadas sobre métodos de autenticação

### <a name="cloud-authentication-password-hash-sync"></a>Autenticação na nuvem: sincronização de hash de senha 

* **Esforço:** a Sincronização de Hash de Senha requer o menor esforço com relação à implantação, à manutenção e à infraestrutura para organizações que precisam apenas permitir que seus usuários entrem no Office 365, em aplicativos SaaS e em outros recursos baseados no Azure AD. Quando habilitada, a Sincronização de Hash de Senha faz parte do processo de sincronização do Azure AD Connect e é executada a cada dois minutos. 

* **Experiência do usuário:** recomenda-se que as organizações implantem o SSO (logon único) contínuo com Sincronização de Hash de Senha para melhorar a experiência de entrada do usuário, evitando solicitações desnecessárias quando eles se conectarem.

* **Cenários avançados:** se as organizações decidirem, será possível usar insights de identidades com relatórios do Azure AD Identity Protection, como o relatório de credenciais vazado. O Windows Hello para Empresas é outra opção que tem [requisitos específicos quando você usa a Sincronização de Hash de Senha](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification). As organizações que exigem autenticação multifator com Sincronização de Hash de Senha devem usar a autenticação multifator do Azure AD e não podem usar métodos de autenticação multifator locais ou de terceiros.

* **Continuidade de negócios:** a Sincronização de Hash de Senha está inerente e altamente disponível como um serviço de nuvem que pode ser expandido para todos os datacenters da Microsoft. Recomenda-se que um segundo servidor do Azure AD Connect seja implantado em modo de preparo em uma configuração de espera para fins de recuperação de desastre.

* **Considerações:** no momento, a Sincronização de Hash de Senha não aplica imediatamente as alterações nos estados de conta locais. Nessa situação, um usuário terá acesso a aplicativos na nuvem até que o estado de conta de usuário seja sincronizado com o Azure AD. Se as organizações desejarem superar essa limitação, recomenda-se que um novo ciclo de sincronização seja ativado após os administradores realizarem atualizações em massa nos estados de contas de usuário locais, como desabilitar contas. Conta bloqueada é outro estado de conta de usuário que será sincronizado no próximo ciclo. 

> [!NOTE] 
> No momento, o estado de senha expirada não é sincronizado com o Azure AD usando o Azure AD Connect. 

Consulte [como implementar a Sincronização de Hash de Senha](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) para ver as etapas de implantação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação na nuvem: autenticação de passagem  

* **Esforço:** para autenticação de passagem, é necessário um ou mais (recomenda-se três) agentes leves instalados em servidores existentes com acesso a seus Active Directory Domain Services locais, incluindo o acesso a seus controladores de domínio locais do AD. Esses agentes precisam de acesso de saída à Internet e têm acesso aos controladores de domínio. Por esse motivo, não é permitido a implantação de agentes em uma rede de perímetro, porque ela requer acesso de rede irrestrito a controladores de domínio. Todo o tráfego de rede é criptografado e limitado a solicitações de autenticação. Para obter mais informações sobre esse processo, consulte a [análise aprofundada sobre segurança](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) na autenticação de passagem.

* **Experiência do usuário:** recomenda-se que as organizações implantem o logon único contínuo com autenticação de passagem para melhorar a experiência de entrada do usuário, evitando solicitações desnecessárias quando eles se conectarem.

* **Cenários avançados:** a autenticação de passagem garante que as solicitações de autenticação sejam negadas imediatamente quando o estado de conta do usuário local está desabilitado, bloqueado ou quando a senha expirou ou após passarem as horas de logon permitidas ao usuário. As organizações que requerem autenticação multifator com autenticação de passagem devem usar a autenticação multifator do Azure AD e não podem usar um método de autenticação multifator local ou de terceiros. Os recursos avançados, como o relatório de credenciais vazadas da Proteção de Identidade requererão que a Sincronização de Hash de Senha seja implantada independentemente se você escolher a autenticação de passagem.

* **Continuidade de negócios:** recomenda-se que você implante dois agentes de passagem extra, além do primeiro agente no servidor do Azure AD Connect para garantir a alta disponibilidade de solicitações de autenticação. Quando você tiver três agentes implantados, um deles ainda poderá falhar quando outro agente estiver inoperante para manutenção. Outro benefício da implantação da Sincronização de Hash de Senha, além da autenticação de passagem, é que ela poderá funcionar como método de autenticação de backup quando o método de autenticação principal não estiver mais disponível, por exemplo, quando os servidores locais não estão disponíveis.

* **Considerações:** se você usar a Sincronização de Hash de Senha como um método de autenticação de backup para autenticação de passagem e os agentes não puderem validar as credenciais do usuário, então o failover para a Sincronização de Hash de Senha não acontecerá automaticamente. Seria necessário alterar o método de logon manualmente usando o Azure AD Connect. A autenticação de passagem só é compatível com aplicativos de nuvem que usam autenticação moderna e protocolos específicos do Exchange Online, como o ActiveSync, POP3 e IMAP4. Por exemplo, [o Microsoft Office 2013 e posteriores são compatíveis com a autenticação moderna, mas não com versões anteriores](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) sobre mais informações de suporte de aplicativo do Office. Consulte as [perguntas frequentes](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) e outras considerações sobre autenticação de passagem, incluindo o suporte à ID alternativa.

Consulte a [implementação da autenticação de passagem](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) para ver etapas de implantação.

### <a name="federated-authentication"></a>Autenticação Federada

* **Esforço:** usar um sistema de autenticação federada depende de um sistema externo para autenticar usuários. Algumas empresas desejam reutilizar seus investimentos em sistemas federados existentes com sua solução de identidade híbrida do Azure AD. A manutenção e o gerenciamento do sistema federado fica fora do controle do Azure AD. Cabe à organização usar o sistema federado para garantir que ele seja implantado de forma segura e que possa lidar com a carga de autenticação. 

* **Experiência do usuário:** a experiência do usuário de autenticação federada depende de implementação dos recursos, da topologia e da configuração do farm de federação. Algumas organizações requerem essa flexibilidade para adaptar e configurar o acesso ao farm de federação para atender às suas necessidades de segurança. Por exemplo, é possível configurar os usuários e os dispositivos conectados internamente para conectar os usuários automaticamente, sem notificá-los a inserir suas credenciais, porque eles já entraram em seus dispositivos. Por outro lado, se necessário, alguns recursos avançados de segurança podem dificultar processo de entrada do usuário.

* **Cenários avançados:** normalmente, a solução de autenticação federada é exigida quando os clientes têm um requisito de autenticação não compatível nativamente com o Azure AD; informações detalhadas estão [listadas aqui](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), mas os requisitos comuns incluem:

    * Autenticação que requer cartões inteligentes ou certificados
    * Usando o servidor MFA local ou provedor multifator de terceiros.
    * Autenticação que usa solução de autenticação de terceiros. Consulte a [lista de compatibilidade de federação do Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Os usuários devem entrar usando o sAMAccountName, por exemplo, DOMAIN\username, em vez de usar um nome UPN, por exemplo, user@domain.com
    * Uma implementação do Windows Hello para Empresas dependente de uma PKI (Infraestrutura de Chave Pública) Empresarial interna para confiança de certificado

* **Continuidade de negócios:** normalmente, os sistemas federados requerem uma matriz de servidores de carga balanceada, também conhecida como um farm, configurada em uma rede interna e em uma topologia de rede de perímetro para garantir alta disponibilidade para solicitações de autenticação. A Sincronização de Hash de Senha pode ser implantada com a Autenticação federada como método de autenticação de backup quando o método de autenticação principal não está mais disponível, por exemplo, quando os servidores locais não estão disponíveis. Algumas organizações empresariais de grande porte requerem uma solução de federação para dar suporte a vários pontos de entrada de Internet configurados com a DNS geográfica para solicitações de autenticação de baixa latência.

* **Considerações:** normalmente, os sistemas federados exigem um investimento mais significativo em infraestrutura local. A maioria das organizações escolhe essa opção se elas já têm um investimento de federação local e é um requisito de negócios de alta segurança para usar um único provedor de identidade. A federação é mais complexa de operar e de resolver problemas em comparação com soluções de autenticação na nuvem. O uso IDs de usuário com um domínio não roteável que não pode ser verificado no Azure AD na entrada precisa de configuração extra para ser implementado. Esse requisito é conhecido como suporte à ID de logon alternativa. Consulte [Configurando a ID de logon alternativa](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para ver requisitos e limitações.

Consulte como [implantar serviços federados](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/deploying-federation-servers) para ver as etapas da implantação.

> [!NOTE] 
> Quando você implanta sua solução de identidade híbrida do Azure AD, é necessário implementar uma das tecnologias do Azure AD Connect compatível. Saiba mais sobre configurações compatíveis e não compatíveis em [Topologias para o Azure AD Connect](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

O diagrama a seguir descreve os componentes de arquitetura de alto nível necessários para cada método de autenticação que você pode usar com sua solução de identidade híbrida do Azure AD. Ele fornece uma visão geral para comparar as diferenças entre as soluções.

O diagrama a seguir descreve a simplicidade de uma solução de Sincronização de Hash de Senha:

![PHS](media/azure-ad/azure-ad-authn-image2.png)

O diagrama a seguir descreve os requisitos do agente da autenticação de passagem:

![PTA](media/azure-ad/azure-ad-authn-image3.png)

O diagrama a seguir descreve os componentes necessários para federação em seu perímetro e na rede interna da sua organização:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="recommendations-and-considerations-from-azure-ad"></a>Recomendações e considerações do Azure AD

Seu sistema de identidade garante que seus usuários tenham acesso a aplicativos de nuvem e de linha de negócios que você migra e disponibiliza na nuvem. A autenticação controla o acesso aos aplicativos para manter os usuários autorizados produtivos e os atores ruins fora dos dados confidenciais de sua organização. Por esse motivo, considere as seguintes recomendações ao escolher o método de autenticação correto para sua organização. 

Use ou habilite a Sincronização de Hash de Senha independentemente do método de autenticação escolhido, pelos seguintes motivos:

1. **Alta disponibilidade e recuperação de desastre:** a autenticação de passagem e a federação dependem da infraestrutura local. Para autenticação de passagem, isso inclui o hardware de servidor e a rede. Ele também é dependente de controladores de domínio para responder a solicitações de autenticação dos agentes de autenticação de passagem. Para a federação, o espaço local é ainda mais dependente, uma vez que requer que os servidores em sua rede de perímetro para usar um proxy nas solicitações de autenticação e nos servidores de federação interna. Para evitar pontos únicos de falhas, sua organização deverá implantar servidores redundantes para garantir que solicitações de autenticação sejam sempre atendidas se qualquer componente falhar. Muitos componentes precisam de manutenção para permanecerem íntegros e as interrupções são mais prováveis quando a manutenção não é planejada nem implementada corretamente. As interrupções podem ser evitadas usando a Sincronização de Hash de Senha, porque o serviço de autenticação na nuvem do Microsoft Azure AD é dimensionado globalmente e está sempre disponível.

2. **Sobrevivência de interrupção local:** as consequências de uma interrupção local devido a um ataque cibernético ou a um desastre podem ser significativas, variando de danos à reputação até a paralisação da organização, que é incapaz de lidar com o ataque. No último ano, muitas organizações foram vítimas de ataques de malware, inclusive ransomware direcionados, o que fez seus servidores locais ficarem inoperantes. Ao ajudar os clientes a lidarem com esses tipos de ataques, a Microsoft observou duas categorias de organizações:

   a. As organizações que anteriormente habilitavam a Sincronização de Hash de Senha ficavam online novamente em questão de horas ao alterar seu método de autenticação para usar a Sincronização de Hash de Senha. Usando o acesso ao email por meio do Office 365, elas puderam trabalhar para resolver problemas e ter acesso a outras cargas de trabalho baseadas em nuvem.

   b. As organizações que anteriormente não habilitaram a Sincronização de Hash de Senha precisavam recorrer a sistemas de email de consumidor externo não confiáveis para comunicações e para resolver os problemas. Nesses casos, elas demoraram semanas ou mais para voltar à operação.

3. **Proteção de identidade:** uma das melhores maneiras de proteger os usuários na nuvem é o Azure AD Identity Protection. A Microsoft examina a Internet continuamente para ver se há listas de usuário e senha que maus atores vendem e disponibilizam na dark Web. O Azure AD poderá usar essas informações para verificar se os nomes de usuário e senhas em sua organização estiverem comprometidos. Portanto, é fundamental habilitar a Sincronização de Hash de Senha, independentemente do método de autenticação usado, seja autenticação federada ou de passagem. As credenciais vazadas são apresentadas como um relatório e podem ser usadas para bloquear ou forçar um usuário a alterar sua senha quando tentam entrar com uma senha vazada.

Por fim, acordo com a [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), a Microsoft tem o conjunto mais completo de funções de Gerenciamento de acesso e de identidade. A Microsoft combate sete trilhões de eventos cibernéticos por dia e fornece logon para usuários autorizados a milhares de aplicativos SaaS, como o Office 365 de praticamente qualquer dispositivo. 

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implantar a fim de dar suporte ao acesso aos aplicativos de nuvem. Para atender a vários requisitos técnicos, de negócios e de segurança, as organizações podem escolher entre a Sincronização de Hash de Senha, a autenticação de passagem e a federação. Com cada método de autenticação, sua organização pode escolher se seus requisitos de negócios são endereçados pelo esforço necessário para implantar a solução e pela experiência do usuário do processo de entrada. Também é necessário avaliar se sua organização precisa dos cenários avançados e dos recursos de continuidade de negócios de cada método de autenticação. Finalmente, é necessário avaliar as considerações de cada método de autenticação para ver se alguma delas impede você de implementar sua escolha.

## <a name="next-steps"></a>Próximas etapas

Atualmente, as ameaças estão presentes 24 horas por dia e vêm de qualquer lugar. Implementar o método de autenticação correto ajudará a reduzir seus riscos de segurança e a proteger suas identidades. 

[Comece](https://docs.microsoft.com/en-us/azure/active-directory/get-started-azure-ad) a usar o Azure AD e implante a solução de autenticação adequada para sua organização.

Se você estiver considerando migrar da autenticação federada para uma na nuvem, saiba mais [sobre como alterar o método de entrada](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Para ajudá-lo a planejar e implementar a migração, é possível usar [esses planos de projeto para auxiliá-lo](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication).
