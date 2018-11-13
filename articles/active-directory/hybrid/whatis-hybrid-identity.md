---
title: Conectar o Active Directory com o Azure Active Directory. | Microsoft Docs
description: O Azure AD Connect integrará seus diretórios locais com o Azure Active Directory.  Isso permite que você forneça uma identidade comum para os aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure.
keywords: introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é o Azure AD Connect, instalar o active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979464"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Identidade híbrida e soluções de identidade da Microsoft
As soluções de identidade híbridas do [Microsoft Azure AD (Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md) permitem que você sincronize objetos de diretório locais com o Azure AD enquanto ainda está gerenciando seus usuários locais. A primeira decisão a tomar ao planejar sincronizar seu Windows Server Active Directory localmente com o Azure AD é se você deseja usar identidades gerenciadas ou a identidade federada. 

- **Identidades gerenciadas** - Contas de usuário e grupos que são sincronizados com um Active Directory no local e a autenticação do usuário é gerenciada pelo Azure.   
- As **identidades federadas** permitem mais controle sobre os usuários ao separar a autenticação do usuário do Azure e a delegação de autenticação para um provedor de identidade local confiável. 

Há várias opções disponíveis para a configuração de identidade híbrida. Conforme considera qual modelo de identidade melhor atenda às necessidades de sua organização, você também precisa pensar no tempo, na infraestrutura existente, na complexidade e no custo. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. No entanto, se seus requisitos realmente mudarem, você também tem a flexibilidade para mudar para um modelo de identidade diferente.

## <a name="managed-identity"></a>Identidade gerenciada 

A identidade gerenciada é a maneira mais simples de sincronizar objetos de diretório locais (usuários e grupos) com o Azure AD. 

![Identidade híbrida sincronizada](./media/whatis-hybrid-identity/managed.png)

Embora a identidade gerenciada seja o método mais rápido e fácil, os usuários ainda precisam manter uma senha separada para os recursos baseados em nuvem. Para evitar isso, você também pode (opcionalmente) [sincronizar um hash das senhas dos usuários](how-to-connect-password-hash-synchronization.md) ao seu diretório do Azure AD. Sincronizar os hashes de senha permite aos usuários fazer logon em recursos organizacionais baseados em nuvem com o mesmo nome de usuário e senha que eles usam localmente. O Azure AD Connect verifica periodicamente seu diretório local quanto a alterações e mantém seu diretório do Azure AD sincronizado. Quando um atributo de usuário ou senha é alterado no Active Directory local, ele é automaticamente atualizado no Azure AD. 

Para a maioria das organizações que precisam apenas habilitar seus usuários para fazer logon no Office 365, aplicativos SaaS e outros recursos baseados no Azure AD, a opção de sincronização de hash de senha padrão é recomendável. Se isso não funcionar para você, será necessário decidir entre a autenticação de passagem e o AD FS.

> [!TIP]
> As senhas de usuário são armazenadas Windows Server Active Directory local na forma de um valor de hash que representa a senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o algoritmo de hash). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. Não é possível usar o hash de senha para entrar na sua rede local. Quando você opta por sincronizar senhas, o Azure AD Connect extrai os hashes de senha do Active Directory local e aplica o processamento de segurança extra ao hash de senha antes de ele ser sincronizado com o Azure AD. A sincronização de hash de senha também pode ser usada em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no Azure AD. Além disso, você pode habilitar o SSO (Logon único) para usuários em computadores ingressados no domínio que estejam conectados à rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para acessar com segurança os recursos de nuvem. 
>

## <a name="pass-through-authentication"></a>Autenticação de passagem

A [Autenticação de passagem do Azure AD](how-to-connect-pta.md) fornece uma solução de validação de senha simples para serviços baseados no Azure AD usando o Active Directory local. Se as políticas de conformidade e segurança para a sua organização não permitirem enviar as senhas dos usuários, mesmo em um formulário de hash e você só precisará dar suporte ao SSO de área de trabalho para dispositivos ingressados no domínio, é recomendável que você avalie o uso da autenticação de passagem. A autenticação de passagem não exige qualquer implantação na DMZ, o que simplifica a infraestrutura de implantação quando comparada ao AD FS. Quando os usuários entram usando o Azure AD, esse método de autenticação valida as senhas dos usuários diretamente no seu Active Directory local.

![Autenticação de passagem](./media/whatis-hybrid-identity/pass-through-authentication.png)

Com a autenticação de passagem, não é necessária uma infraestrutura de rede complexa e você não precisa armazenar senhas locais na nuvem. Combinada com o logon único, a autenticação de passagem fornece uma experiência realmente integrada ao entrar no Azure AD ou outros serviços de nuvem.

A autenticação de passagem é configurada por meio do Azure AD Connect, que utiliza um agente local simples que escuta solicitações de validação de senha. O agente pode ser implantado facilmente em várias máquinas para fornecer alta disponibilidade e balanceamento de carga. Como todas as comunicações são apenas de saída, não há requisitos para o conector ser instalado em uma DMZ. Os requisitos do computador do servidor para o conector são os seguintes:

- Windows Server 2012 R2 ou posterior
- Ingressado em um domínio na floresta em que os usuários são validados

## <a name="federated-identity-ad-fs"></a>Identidade federada (AD FS)

Para obter mais controle sobre como os usuários acessam o Office 365 e outros serviços de nuvem, você pode configurar a sincronização de diretórios com SSO (logon único) usando os [Serviços de Federação do Active Directory (AD FS)](how-to-connect-fed-whatis.md). Federar as entradas de seus usuários com o AD FS delega a autenticação para um servidor local que valida as credenciais do usuário. Nesse modelo, as credenciais do Active Directory local nunca são passadas para o Azure AD.

![Identidade federada](./media/whatis-hybrid-identity/federated-identity.png)

Também chamado de federação de identidade, esse método de entrada garante que todas as autenticações de usuário sejam controladas localmente e permite que os administradores implementem níveis mais rigorosos de controle de acesso. A federação de identidade com o AD FS é a opção mais complicada e exige a implantação de servidores adicionais no seu ambiente local. A federação de identidade também o compromete a fornecer suporte 24 horas por dia, 7 dias por semana para sua infraestrutura do Active Directory e AD FS. Esse nível alto de suporte é necessário porque se seu acesso à Internet local, seu controlador de domínio ou seus servidores AD FS estiverem indisponíveis, os usuários não poderão entrar nos serviços de nuvem.

> [!TIP]
> Se você optar por usar a Federação com o Active Directory Federation Services (AD FS), outra opção será configurar a sincronização de hash de senha como um backup em caso de falha na infraestrutura do AD FS.
>

## <a name="common-scenarios-and-recommendations"></a>Cenários e recomendações comuns

Aqui estão alguns dos cenários comuns de gerenciamento de acesso e identidade híbrida com recomendações sobre qual opção de identidade híbrida pode ser apropriada para cada um.

|Eu preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronizar automaticamente na nuvem as contas de novo usuário, de contato e de grupo criadas no meu Active Directory local.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Configurar meu locatário para cenários híbridos do Office 365|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Habilitar os usuários para entrar e acessar serviços de nuvem usando suas senhas locais|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Implementar o logon único usando credenciais corporativas|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Certificar-me de que os hashes de senha não sejam armazenados na nuvem| |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Habilitar soluções de autenticação multifator de nuvem| |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Habilitar soluções de autenticação multifator locais| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Suporte à autenticação de cartão inteligente para meus usuários<sup>4</sup>| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|
|Exibir notificações de expiração de senha no Portal do Office e na área de trabalho do Windows 10| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Sincronização de hash de senha com logon único.
>
> <sup>2</sup> Autenticação de passagem e Logon único. 
>
> <sup>3</sup> Logon único federado com o AD FS.
>
> <sup>4</sup> O AD FS pode ser integrado com a Enterprise PKI para permitir a entrada usando certificados. Esses certificados podem ser certificados suaves implantados por meio dos canais de provisionamento confiáveis como certificados de cartão inteligente (incluindo cartões PIV/CAC), MDM, GPO ou Hello for Business (certificado confiável). Para obter mais informações sobre o suporte à autenticação de cartão inteligente, consulte [este blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft criada para atender e atingir suas metas de identidade híbrida.  Isso permite que você forneça uma identidade comum para os usuários dos aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure.  Ela fornece os seguintes recursos:
    
- [Sincronização](how-to-connect-sync-whatis.md) – esse componente é responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.  É responsável por sincronizar hashes de senha com o Azure AD.
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) -um componente opcional que permite que os usuários usem o mesma senha no local e na nuvem, sincronizando um hash da senha de usuário com o Azure AD.
-   [AD FS e integração de federação](how-to-connect-fed-whatis.md) – a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Também fornece recursos de gerenciamento do AD FS, como renovação de certificado e implantações adicionais de servidor do AD FS.
-   [Autenticação de Passagem](how-to-connect-pta.md) – outro componente opcional que permite que os usuários usem o mesma senha localmente e na nuvem, mas não exige a infraestrutura adicional de um ambiente federado
-   [Integração do PingFederate e federação](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) -outra opção de federação que permite que você use o PingFederate como seu provedor de identidade.
-   [Monitoramento de Integridade](whatis-hybrid-identity-health.md) – o Azure AD Connect Health pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade. 


![O que é o Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>O que é o Azure AD Connect Health?

O Azure AD (Azure Active Directory) Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local e os serviços de sincronização. Ele permite manter uma conexão confiável com o Office 365 e o Microsoft Online Services, fornecendo recursos de monitoramento para os componentes de identidade de chave, como servidores do AD FS (Serviços de Federação do Active Directory), servidores do Azure AD Connect (também conhecido como Mecanismo de Sincronização), controladores de domínio do Active Directory etc. Ele também torna os pontos de dados principais sobre esses componentes facilmente acessíveis para que você possa usá-los e obter informações importantes para tomar decisões informadas.

As informações são apresentadas no [Portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). No portal do Azure AD Connect Health, você poderá exibir alertas, o monitoramento de desempenho, a análise de uso e outras informações. O Azure AD Connect Health permite a lente única da integridade sobre seus componentes de identidade de chave em um único lugar.

![O que é a Integridade do Azure AD Connect](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Como aumentam os recursos do Azure AD Connect Health, o portal fornece um único painel por meio das lentes de identidade. Você obtém um ambiente ainda mais robusto, íntegro e integrado para seus usuários aumentar sua capacidade de fazer essas coisas.


## <a name="why-use-azure-ad-connect"></a>Por que usar o Azure AD Connect?
A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos ao fornecer uma identidade comum para acesso aos recursos na nuvem e locais. Os usuários e as organizações podem beneficiar-se do seguinte:

* Os usuários podem usar uma única identidade para acessar aplicativos locais e serviços na nuvem, como o Office 365.
* Ferramenta única para fornecer uma experiência de implantação fácil de sincronização e entrada.
* Fornece os recursos mais recentes para seus cenários. O Azure AD Connect substitui as versões mais antigas das ferramentas de integração de identidade, como DirSync e Azure AD Sync. Para saber mais, confira [Comparação das ferramentas de integração de diretórios de Identidade Híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Por que usar o Azure AD Connect Health?
Ao integrar seus diretórios locais com o Azure AD, os usuários são mais produtivos porque há uma identidade comum para acessar recursos de nuvem e locais. No entanto, essa integração gera os desafios de garantir de que esse ambiente esteja íntegro, para que os usuários possam acessar de forma confiável recursos locais e na nuvem por meio de qualquer dispositivo. O Azure AD Connect Health ajuda você a monitorar e obter percepções sobre sua infraestrutura de identidade local usada para acessar o Office 365 ou outros aplicativos do Azure AD. É tão simples quanto instalar um agente em cada um de seus servidores de identidade locais.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health para AD FS](how-to-connect-health-adfs.md)
O Azure AD Connect Health para AD FS dá suporte ao AD FS 2.0 no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Isso também oferece o suporte de monitoramento de servidores proxy do AD FS ou proxy de aplicativo Web que dão suporte à autenticação para acesso à extranet. Com uma instalação fácil e rápida do Agente de Integridade, o Azure AD Connect Health para AD FS oferece um conjunto de recursos principais.

#### <a name="key-benefits-and-best-practices"></a>Principais benefícios e melhores práticas

- *Segurança aprimorada*
  - [Tendências de bloqueio de extranet](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Relatório de entradas de falha](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - Em [conformidade com a privacidade](reference-connect-health-user-privacy.md)    
- *Receber alertas sobre todos os [problemas críticos do ADFS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Configuração e disponibilidade do servidor 
    - [Desempenho e conectividade](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Manutenção regular    
- *Fácil de implantar e gerenciar*
  - [Instalação do agente](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) rápida 
  - Atualização automática do agente até o mais recente 
  - Dados disponíveis no portal em minutos    
- *Ricas [métricas de uso](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - Uso dos principais aplicativos
  - Locais de rede e conexão TCP
  - Solicitações de token por servidor    
- *Ótima experiência do usuário* 
  - Forma do painel do portal do Azure
  - [Alertas por emails](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Realce de recurso

*   Monitoramento com alertas para saber quando os servidores do AD FS e proxy do AD FS não estão íntegros
*   Notificações de email para alertas críticos
*   Tendências nos dados de desempenho, que são úteis para o planejamento de capacidade do AD FS
*   Análise de uso para logons no AD FS com tabelas dinâmicas (aplicativos, usuários, local de rede etc.)
*   Relatórios para o AD FS, como os 50 primeiros usuários com tentativas inválidas de nome de usuário/senha e o último endereço IP
*   Relatório de IP arriscado para inícios de sessão do AD FS com falha

Leia mais aqui sobre o [Uso do Azure AD Connect Health com AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
O Azure AD Connect Health para sincronização monitora e fornece informações sobre as sincronizações que ocorrem entre seu Active Directory local e o Azure AD. O Azure AD Connect Health para sincronização fornece o seguinte conjunto de recursos principais:

* Monitoramento com alertas para saber quando um servidor do Azure AD Connect, também conhecido como Mecanismo de Sincronização, não está íntegro
* Notificações de email para alertas críticos
* Informações operacionais de sincronização, incluindo gráficos de latência para operações de sincronização e tendências em operações diferentes, como adições, atualizações, exclusões
* Informações rápidas sobre propriedades de sincronização e a última exportação com êxito para o Azure AD
* Os relatórios sobre erros de sincronização no nível do objeto \(não exigem o Azure AD Premium\)

Leia mais aqui sobre o [Uso o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health para AD DS](how-to-connect-health-adds.md)
O Azure AD Connect Health para AD DS (Active Directory Domain Services) fornece monitoramento para controladores de domínio instalados no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. A instalação do Agente de Integridade permite que você monitore o ambiente do AD DS local diretamente da nuvem. O Azure AD Connect Health para o AD DS fornece o seguinte conjunto de recursos principais:

* Alertas de monitoramento para detectar quando controladores de domínio não estão íntegros, juntamente com as notificações por email para alertas críticos
* O painel de Controladores de Domínio que fornece uma visão geral da integridade e do status operacional dos controladores de domínio
* O painel de Status de Replicação com informações mais recentes de replicação e links para guias de solução de problemas quando são detectados erros
* Acesso rápido em qualquer lugar a grafos de dados de desempenho de contadores de desempenho comuns, necessários para fins de monitoramento e solução de problemas

Leia mais aqui sobre o [Uso do Azure AD Connect Health com AD DS](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Próximas etapas


- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md)|
- [Autenticação de passagem](how-to-connect-pta.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
- [Instalar agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
- [Histórico de versão](reference-connect-version-history.md)
- [Comparação de ferramentas de integração de diretório](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Perguntas frequentes do Azure AD Connect](reference-connect-faq.md)









