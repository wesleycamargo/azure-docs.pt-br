---
title: O que é o Azure AD Connect e o Connect Health? | Microsoft Docs
description: Descreve as ferramentas usadas para sincronizar e monitorar seu ambiente local com o Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fbd6edb02dfc4080d7692e43324a5b3981091f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110256"
---
# <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect?

O Azure AD Connect é a ferramenta da Microsoft criada para atender e atingir suas metas de identidade híbrida.  Ela fornece os seguintes recursos:
    
- [Sincronização de hash de senha](whatis-phs.md) – um método de entrada que sincroniza o hash da senha do AD local do usuário com o Azure AD.
- [Autenticação de passagem](how-to-connect-pta.md) – um método de entrada que permite que os usuários usem a mesma senha localmente e na nuvem, mas não exige a infraestrutura adicional de um ambiente federado.
- [Integração de federação](how-to-connect-fed-whatis.md) – a federação é uma parte opcional do Azure AD Connect e pode ser usada para configurar um ambiente híbrido usando uma infraestrutura do AD FS local. Ela também fornece recursos de gerenciamento do AD FS, como renovação de certificado e implantações adicionais de servidor do AD FS.
- [Sincronização](how-to-connect-sync-whatis.md) – responsável pela criação de usuários, grupos e outros objetos.  Também é responsável por garantir que as informações de identidade dos usuários e grupos locais correspondam às da nuvem.  Essa sincronização também inclui os hashes de senha.
-   [Monitoramento de Integridade](whatis-hybrid-identity-health.md) – o Azure AD Connect Health pode fornecer monitoramento robusto e fornecer um local central no portal do Azure para exibir essa atividade. 


![O que é o Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>O que é o Azure AD Connect Health?

O Azure AD (Azure Active Directory) Connect Health fornece monitoramento robusto de sua infraestrutura de identidade local. Ele permite que você mantenha uma conexão confiável com o Office 365 e os Microsoft Online Services.  Essa confiabilidade é obtida fornecendo recursos de monitoramento para seus principais componentes de identidade. Além disso, ela torna os pontos de dados principais desses componentes facilmente acessíveis.

As informações são apresentadas no [Portal do Azure AD Connect Health](https://aka.ms/aadconnecthealth). Use o portal do Azure AD Connect Health para exibir alertas, monitorar o desempenho e analisar o uso, bem como outras informações. O Azure AD Connect Health permite a lente única da integridade sobre seus componentes de identidade de chave em um único lugar.

![O que é a Integridade do Azure AD Connect](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Por que usar o Azure AD Connect?
A integração de seus diretórios locais ao AD do Azure torna os usuários mais produtivos ao fornecer uma identidade comum para acesso aos recursos na nuvem e locais. Os usuários e as organizações podem beneficiar-se do seguinte:

* Os usuários podem usar uma única identidade para acessar aplicativos locais e serviços na nuvem, como o Office 365.
* Ferramenta única para fornecer uma experiência de implantação fácil de sincronização e entrada.
* Fornece os recursos mais recentes para seus cenários. O Azure AD Connect substitui as versões mais antigas das ferramentas de integração de identidade, como DirSync e Azure AD Sync. Para saber mais, confira [Comparação das ferramentas de integração de diretórios de Identidade Híbrida](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Por que usar o Azure AD Connect Health?
No Azure AD, os usuários são mais produtivos porque há uma identidade comum para acessar recursos de nuvem e locais. Garantir que o ambiente seja confiável, para que os usuários possam acessar esses recursos, torna-se um desafio.  O Azure AD Connect Health ajuda no monitoramento e na obtenção de informações sobre a sua infraestrutura de identidade local, garantindo assim a confiabilidade do ambiente. É tão simples quanto instalar um agente em cada um de seus servidores de identidade locais.


## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos e hardware](how-to-connect-install-prerequisites.md) 
- [Configurações Expressas](how-to-connect-install-express.md)
- [Configurações personalizadas](how-to-connect-install-custom.md)
- [Instalar agentes do Azure AD Connect Health](how-to-connect-health-agent-install.md) 
