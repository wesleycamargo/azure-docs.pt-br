---
title: "Práticas recomendadas para o acesso condicional no Azure Active Directory | Microsoft Docs"
description: "Saiba sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8c6707505a6331b53e06b1de60575dd3637ea477
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Práticas recomendadas para o acesso condicional no Azure Active Directory

Este tópico fornece informações sobre o que você deve saber e o que você deve evitar fazer ao configurar políticas de acesso condicional. Antes de ler este tópico, você deve se familiarizar com os conceitos e a terminologia descritos em [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="whats-required-to-make-a-policy-work"></a>O que é necessário para fazer uma política funcionar?

Quando você cria uma nova política, não há nenhum usuário, grupo, aplicativo ou controle de acesso selecionado.

![Aplicativos na nuvem](./media/active-directory-conditional-access-best-practices/02.png)


Para fazer sua política funcionar, você deve configurar o seguinte:


|O que           | Como                                  | Porque|
|:--            | :--                                  | :-- |
|**Aplicativos na nuvem** |Você precisa selecionar um ou mais aplicativos.  | A meta de uma política de acesso condicional é habilitar você a ajustar como os usuários autorizados podem acessar seus aplicativos.|
| **Usuários e grupos** | Você precisa selecionar pelo menos um usuário ou grupo autorizado a acessar os aplicativos na nuvem que você selecionou. | Uma política de acesso condicional que não tenha usuários e grupos atribuídos nunca será disparada. |
| **Controles de acesso** | Você precisa selecionar pelo menos um controle de acesso. | O processador de políticas precisa saber o que fazer se as condições forem atendidas.|


Além desses requisitos básicos, em muitos casos, você também precisa configurar uma condição. Enquanto uma política também funcione sem uma condição configurada, as condições são o fator determinante para ajustar o acesso aos seus aplicativos.


![Aplicativos na nuvem](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>Como as atribuições são avaliadas?

Todas as atribuições são avaliadas com **AND** lógicos. Se você tiver mais de uma atribuição configurada, para disparar uma política, todas as atribuições deverão ser satisfeitas.  

Se você precisar configurar uma condição de local que se aplique a todas as conexões feitas de fora da rede da sua organização, poderá fazer isso:

- Incluindo **todos os locais**
- Excluindo **todos os IPs confiáveis**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>O que acontece se você tiver políticas configuradas no portal clássico do Azure e no portal do Azure?  
Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>O que acontece se você tiver políticas no portal do Intune Silverlight e no Portal do Azure?
Ambas as políticas são aplicadas pelo Azure Active Directory e o usuário só obterá acesso quando todos os requisitos forem atendidos.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>O que acontece se eu tiver várias políticas configuradas para o mesmo usuário?  
Para cada entrada, o Azure Active Directory avalia todas as políticas e garante que todos os requisitos sejam atendidos antes que o acesso seja concedido ao usuário.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>O acesso condicional funciona com o Exchange ActiveSync?

Sim, você não pode usar o Exchange ActiveSync em uma política de acesso condicional.


## <a name="what-you-should-avoid-doing"></a>O que você deve evitar

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, muita flexibilidade também significa que você deve examinar cuidadosamente cada política de configuração antes de liberá-la, a fim de evitar resultados indesejados. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

Em seu ambiente, evite as configurações a seguir:


**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.

- **Exigir dispositivo compatível** - Para usuários que ainda não registraram seus dispositivos, esta política bloqueia todo acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.

- **Exigir ingresso no domínio** - Esta política de bloqueio de acesso também tem o potencial para bloquear o acesso de todos os usuários em sua organização se você ainda não tiver um dispositivo ingressado no domínio.


**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** - Essa configuração bloqueia toda a organização, o que certamente não é uma boa ideia.



## <a name="policy-migration"></a>Migração de política

Você deve considerar migrar as políticas que você não tiver criado no portal do Azure porque:

- Agora você pode abordar cenários que você não podia manipular antes.

- Você pode consolidar as políticas e, dessa forma, reduzir o número de políticas a serem gerenciadas.   

- Você pode gerenciar todas as políticas de acesso condicional em um local central.

- O portal clássico do Azure será desativado.   


Para obter mais informações, consulte [Migrar políticas clássicas no portal do Azure](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Próximas etapas

Se você quiser saber como configurar uma política de acesso condicional, veja [Introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).
