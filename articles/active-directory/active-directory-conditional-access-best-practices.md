---
title: "Práticas recomendadas para o acesso condicional no Azure Active Directory | Microsoft Docs"
description: "Saiba sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: 9cecbfd1fd5db8fffc9fbdfb2d6ca949b6ff385a
ms.contentlocale: pt-br
ms.lasthandoff: 06/22/2017


---
<a id="best-practices-for-conditional-access-in-azure-active-directory" class="xliff"></a>

# Práticas recomendadas para o acesso condicional no Azure Active Directory

Este tópico fornece informações sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional. Antes de ler este tópico, você deve se familiarizar com os conceitos e a terminologia descritos em [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)

<a id="what-you-should-know" class="xliff"></a>

## O que você deve saber

<a id="do-i-need-to-assign-a-user-to-my-policy" class="xliff"></a>

### É necessário atribuir um usuário à minha política?

Ao configurar uma política de acesso condicional, você deve atribuir pelo menos um grupo a ela. Uma política de acesso condicional que não tenha usuários e grupos atribuídos nunca será disparada.

Quando você pretende atribuir vários usuários e grupos a uma política, comece atribuindo apenas um usuário ou um grupo e depois teste sua configuração. Se sua política funcionar como o esperado, você poderá adicionar atribuições adicionais a ela.  


<a id="how-are-assignments-evaluated" class="xliff"></a>

### Como as atribuições são avaliadas?

Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, para disparar uma política, todas as atribuições deverão ser satisfeitas.  

Se você precisar configurar uma condição de local que se aplique a todas as conexões feitas de fora da rede da sua organização, poderá fazer isso:

- Incluindo **todos os locais**
- Excluindo **todos os IPs confiáveis**

<a id="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured" class="xliff"></a>

### O que acontece se você tiver políticas configuradas no portal clássico do Azure e no portal do Azure?  
Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

<a id="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal" class="xliff"></a>

### O que acontece se você tiver políticas no portal do Intune Silverlight e no Portal do Azure?
Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

<a id="what-happens-if-i-have-multiple-policies-for-the-same-user-configured" class="xliff"></a>

### O que acontece se eu tiver várias políticas configuradas para o mesmo usuário?  
Para cada entrada, o Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes que o acesso seja concedido ao usuário.


<a id="does-conditional-access-work-with-exchange-activesync" class="xliff"></a>

### O acesso condicional funciona com o Exchange ActiveSync?

Sim, você não pode usar o Exchange ActiveSync em uma política de acesso condicional.


<a id="what-you-should-avoid-doing" class="xliff"></a>

## O que você deve evitar

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, muita flexibilidade também significa que você deve examinar cuidadosamente cada política de configuração antes de liberá-la, a fim de evitar resultados indesejados. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, evite as configurações a seguir:


**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.

- **Exigir dispositivo compatível** - Para usuários que ainda não registraram seus dispositivos, esta política bloqueia todo acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.

- **Exigir ingresso no domínio** - Esta política de bloqueio de acesso também tem o potencial para bloquear o acesso de todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.


**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.


<a id="common-scenarios" class="xliff"></a>

## Cenários comuns

<a id="requiring-multi-factor-authentication-for-apps" class="xliff"></a>

### Exibir a autenticação multifator para aplicativos

Muitos ambientes têm aplicativos que exigem um nível mais alto de proteção do que outros.
Isso é, por exemplo, o caso para aplicativos que têm acesso a dados confidenciais.
Se você quiser adicionar outra camada de proteção para esses aplicativos, poderá configurar uma política de acesso condicional que exija a autenticação multifator quando os usuários acessarem esses aplicativos.


<a id="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted" class="xliff"></a>

### Exigir autenticação multifator para acesso de redes que não são confiáveis

Esse cenário é semelhante ao cenário anterior porque adiciona um requisito para a autenticação multifator.
No entanto, a principal diferença é a condição para esse requisito.  
Embora o foco do cenário anterior fosse aplicativos com acesso a dados confidenciais, o foco deste cenário é em locais confiáveis.  
Em outras palavras, você pode ter um requisito para autenticação multifator se um aplicativo for acessado por um usuário de uma rede em que você não confia.


<a id="only-trusted-devices-can-access-office-365-services" class="xliff"></a>

### Somente os dispositivos confiáveis podem acessar os serviços do Office 365

Se você estiver usando o Intune em seu ambiente, poderá começar imediatamente usando a interface de política de acesso condicional no console do Azure.

Muitos clientes do Intune estão usando o acesso condicional para garantir que somente os dispositivos confiáveis possam acessar os serviços do Office 365. Isso significa que os dispositivos móveis estão registrados no Intune e atendem aos requisitos da política de conformidade e que os computadores com Windows fazem parte de um domínio local. Uma melhoria-chave é que você não precisa definir a mesma política para cada um dos serviços do Office 365.  Quando você criar uma nova política, configure os aplicativos de nuvem para incluir cada um dos aplicativos do O365 que você deseja proteger com o acesso condicional.

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

