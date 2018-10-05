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
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181763"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Identidade híbrida e soluções de identidade da Microsoft
Hoje, as empresas e corporações estão se tornando, cada vez mais, uma mistura de aplicativos locais e na nuvem.  Ter aplicativos e usuários que precisam de acesso a esses aplicativos, tanto localmente quanto na nuvem, tornou-se um cenário desafiador.

As soluções de identidade da Microsoft abrangem locais e recursos baseados em nuvem, criando uma identidade de usuário único para autenticação e autorização em todos os recursos, independentemente do local. Chamamos isso de identidade híbrida.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft criada para atender e atingir suas metas de identidade híbrida.  Isso permite que você forneça uma identidade comum para os usuários dos aplicativos do Office 365, Azure e SaaS integrados ao AD do Azure.  Ela fornece os seguintes recursos:
    
- [Sincronização](how-to-connect-sync-whatis.md) – esse componente é responsável pela criação de usuários, grupos e outros objetos. Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.  É responsável por sincronizar hashes de senha com o Azure AD.
-   [AD FS e integração de federação](how-to-connect-fed-whatis.md) – a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Também fornece recursos de gerenciamento do AD FS, como renovação de certificado e implantações adicionais de servidor do AD FS.
-   [Autenticação de Passagem](how-to-connect-pta.md) – outro componente opcional que permite que os usuários usem o mesma senha localmente e na nuvem, mas não exige a infraestrutura adicional de um ambiente federado
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


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
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









