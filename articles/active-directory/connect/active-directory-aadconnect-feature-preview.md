---
title: "Azure AD Connect: recursos em visualização | Microsoft Docs"
description: "Este tópico descreve em mais detalhes os recursos que estão na visualização no Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b110b2e3041cf7839d1bf1c4d384b28b243efbd8
ms.openlocfilehash: c1653c769a6b42d18ffb0da71220ce06c6556587
ms.contentlocale: pt-br
ms.lasthandoff: 02/07/2017

---
# Mais detalhes sobre os recursos no modo de visualização
<a id="more-details-about-features-in-preview" class="xliff"></a>
Este tópico descreve como usar recursos presentes atualmente na visualização.

## Write-back de grupo
<a id="group-writeback" class="xliff"></a>
A opção para write-back do grupo nos recursos opcionais permite que você faça write-back de **Grupos do Office 365** para uma floresta com o Exchange instalado. Esse é um grupo que é sempre controlado na nuvem. Se tiver o Exchange local, você pode gravar novamente esses grupos localmente, assim os usuários com uma caixa de correio do Exchange local podem enviar e receber emails destes grupos.

Mais informações sobre Grupos do Office 365 e como usá-los podem ser encontradas [aqui](http://aka.ms/O365g).

Um grupo do Office 365 é representado como um grupo de distribuição no AD DS local. Seu servidor Exchange local deve estar no Exchange 2013 com atualização cumulativa 8 (lançado em março de 2015) ou no Exchange 2016 para reconhecer esse novo tipo de grupo.

**Observações no período de visualização**

* Atualmente, o atributo de catálogo de endereços não é populado na visualização. Sem esse atributo, o grupo não estará visível na GAL. A maneira mais fácil é preencher esse atributo para usar o cmdlet `update-recipient`do PowerShell do Exchange.
* Somente as florestas com o esquema do Exchange são alvos válidos para grupos. Se nenhum Exchange for detectado, não será possível habilitar o write-back de grupo.
* Somente implantações de floresta única de organização do Exchange terão suporte atualmente. Se você tiver mais de uma organização de Exchange local, será necessária uma solução de GALSync local para que esses grupos sejam exibidos em suas outras florestas.
* O recurso de write-back de grupo não lida com grupos de segurança ou grupos de distribuição.

> [!NOTE]
> Uma assinatura do Azure AD Premium é necessária para write-back do grupo.
> 
>

## Write-back de usuário
<a id="user-writeback" class="xliff"></a>
> [!IMPORTANT]
> O recurso de visualização de write-back do usuário foi removido na atualização de agosto de 2015 do Azure AD Connect. Se você tiver habilitado, você deve desabilitar esse recurso.
>
>

## Próximas etapas
<a id="next-steps" class="xliff"></a>
Continuar a [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

