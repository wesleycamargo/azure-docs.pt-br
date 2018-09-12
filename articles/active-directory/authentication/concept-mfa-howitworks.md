---
title: Autenticação Multifator do Azure - Como funciona
description: A Autenticação Multifator do Azure ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 46c99011a22f855f6faf53e03169b2d1e4c6ce85
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669000"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: autenticação multifator do Azure

A segurança da verificação em duas etapas baseia-se na sua abordagem em camadas. O comprometimento de vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga descobrir a senha do usuário, ela será inútil se ele também não estiver de posse do método de autenticação adicional. Isso funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

<center>![Imagem de métodos de autenticação conceitual](./media/concept-mfa-howitworks/methods.png)</center>

A MFA (Autenticação Multifator do Azure) ajuda a proteger o acesso a dados e aplicativos enquanto mantém a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de [métodos de autenticação](concept-authentication-methods.md) fáceis de usar.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a Autenticação Multifator?

A autenticação multifator é fornecida como parte das seguintes ofertas:

* **Licenças do Azure Active Directory Premium** – uso com recursos completos do Serviço de Autenticação Multifator do Azure (Nuvem) ou do Servidor de Autenticação Multifator do Azure (Local).
   * **Serviço de MFA do Azure (Nuvem)** - **Essa opção é o caminho recomendado para novas implantações**. MFA do Azure na nuvem não exige nenhuma infraestrutura local e pode ser usado com usuários federados ou somente na nuvem.
   * **Servidor de MFA do Azure** – se a sua organização quiser gerenciar os elementos de infraestrutura associada e tiver implantado o AD FS no ambiente local, essa maneira poderá ser uma opção.
* **Autenticação Multifator para Office 365** – um subconjunto das funcionalidades de Autenticação Multifator do Azure está disponível como parte da sua assinatura. Para obter mais informações sobre MFA para Office 365, veja o artigo [Planejamento para a autenticação multifator para Implantações do Office 365](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Administradores Globais do Azure Active Directory** – um subconjunto das funcionalidades de Autenticação Multifator do Azure está disponível como um meio para proteger as contas de administrador global.

> [!NOTE]
> Novos clientes não poderão mais comprar Autenticação Multifator do Microsoft Azure como uma oferta independente a partir de 1º de setembro de 2018. A Autenticação Multifator continuará sendo um recurso disponível nas licenças do Azure AD Premium.

### <a name="auth-provider-or-mfa-license"></a>Licença de MFA ou Provedor de Autenticação

Se você tiver o Azure AD Premium ou outro pacote de licenças que inclua o Azure AD Premium, já terá o MFA do Azure. Sua organização não precise de nada adicional para estender a funcionalidade de verificação em duas etapas para todos os usuários. Você só precisa atribuir uma licença a um usuário e, em seguida, você pode ativar o MFA.

Se você não tiver licenças que incluam o MFA do Azure ou não tiver licenças suficientes para cobrir todos os seus usuários, você poderá criar um [Provedor de Autenticação de MFA](concept-mfa-authprovider.md) para estender os recursos completos do MFA para os usuários que necessitam deles.

> [!IMPORTANT]
> Se você não possui licenças suficientes para todos os usuários, você pode criar um provedor de autenticação multifator por usuário para cobrir o restante da sua organização. Não crie um provedor de autenticação por autenticação multifator. Se você fizer isso, poderá acabar pagando solicitações de verificação de usuários que já têm licenças.

## <a name="supportability"></a>Capacidade de suporte

Como a maioria dos usuários está acostumada a usar apenas senhas para a autenticação, é importante que a sua empresa comunique todos os usuários sobre esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o seu suporte técnico para resolver pequenos problemas relacionados a MFA. No entanto, existem alguns cenários em que é necessário desabilitar temporariamente o MFA. Use as diretrizes abaixo para entender como lidar com esses cenários:

* Treine sua equipe de suporte para lidar com cenários em que o usuário não consegue entrar porque não têm acesso a seus métodos de autenticação ou esses métodos não estão funcionando corretamente.
   * Usando políticas de acesso condicional para o serviço de MFA do Azure, sua equipe de suporte pode adicionar um usuário a um grupo que é excluído de uma política que exija MFA.
   * A equipe de suporte pode habilitar um bypass avulso temporário para os usuários do servidor de MFA do Azure permitir que um usuário se autentique sem a verificação em duas etapas. O bypass é temporário e expira após um número de segundos especificado.
   * Usando políticas de acesso condicional para o serviço de MFA do Azure, sua equipe de suporte pode adicionar um usuário a um grupo que é excluído de uma política que exija MFA.
* Use IPs confiáveis ou localizações nomeadas como uma maneira de minimizar os prompts de verificação em duas etapas. Com esse recurso, os administradores de um locatário gerenciado ou federado podem ignorar a verificação em duas etapas para usuários que estão fazendo logon de um local de rede confiável, como a intranet da organização.
* Implante [Azure AD Identity Protection](../active-directory-identityprotection.md) e dispare a verificação em duas etapas com base em eventos de risco.

## <a name="next-steps"></a>Próximas etapas

- Obter um [plano de implantação](https://aka.ms/MFADeploymentPlan) de MFA passo a passo

- Encontrar detalhes sobre o [licenciamento de usuários](concept-mfa-licensing.md)

- Obter detalhes sobre [qual versão implantar](concept-mfa-whichversion.md)

- Encontre respostas a [perguntas frequentes](multi-factor-authentication-faq.md)
