---
title: "Azure AD Connect: Quando você já tiver o Azure AD | Microsoft Docs"
description: "Este tópico descreve como usar o Connect quando você tem um locatário existente do Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: f59028a2f909914222236f3b3575afd0949b4277
ms.openlocfilehash: c89e206462856d25a81729e7028065ac1cd13ef3
ms.contentlocale: pt-br
ms.lasthandoff: 02/23/2017

---

# Azure AD Connect: quando você tem um locatário existente
<a id="azure-ad-connect-when-you-have-an-existent-tenant" class="xliff"></a>
A maioria dos tópicos sobre como usar o Azure AD Connect pressupõe que você inicie com um novo locatário do Azure AD e que não exista nenhum usuário nem outros objetos. Mas, se você tiver começado com um locatário do Azure AD, o preencheu com usuários e outros objetos, e agora deseja usar o Connect, este tópico é para você.

## Noções básicas
<a id="the-basics" class="xliff"></a>
Um objeto no Azure AD é controlado na nuvem (Azure AD) ou no local. Em um único objeto, você não pode gerenciar alguns atributos locais e outros atributos no Azure AD. Cada objeto tem um sinalizador que indica onde o objeto é gerenciado.

Você pode gerenciar alguns usuários locais e outros na nuvem. Um cenário comum para essa configuração é uma organização com uma combinação de funcionários de contabilidade e vendedores. Os funcionários de contabilidade têm uma conta local do AD, mas os vendedores não, eles têm uma conta no Azure AD. Você deve gerenciar alguns usuários locais e outros no Azure AD.

Se você começar a gerenciar usuários no Azure AD que também estão no AD local e, mais tarde, desejar usar o Connect, existem outros problemas que devem ser considerados.

## Sincronia com usuários existentes no Azure AD
<a id="sync-with-existing-users-in-azure-ad" class="xliff"></a>
Quando você instala o Azure AD Connect e inicia a sincronização, o serviço de sincronização do Azure AD (no Azure AD) faz uma verificação em cada objeto novo e tenta localizar um objeto existente para corresponder. Existem três atributos usados para esse processo: **userPrincipalName**, **proxyAddresses** e **sourceAnchor**/**immutableID**. Uma correspondência entre **userPrincipalName** e **proxyAddresses** é conhecida como uma **correspondência flexível**. Uma correspondência com **sourceAnchor** é conhecida como **correspondência rígida**. Para o atributo **proxyAddresses**, apenas o valor com **SMTP:**, que é o email principal, é usado para a avaliação.

A correspondência é avaliada apenas para objetos novos provenientes do Connect. Se você alterar um objeto existente para que ele corresponda a um desses atributos, ocorrerá um erro.

Se o Azure AD encontrar um objeto cujos valores de atributo são os mesmos para um objeto proveniente do Connect e que já esteja presente no Azure AD, o objeto no Azure AD será controlado pelo Connect. O objeto anteriormente gerenciado por nuvem é sinalizado como gerenciado no local. Todos os atributos no Azure AD com um valor no AD local são substituídos pelo valor local. A exceção é quando um atributo tem um valor local **NULL**. Nesse caso, o valor no Azure AD permanece, mas você pode alterar apenas o local para outro valor.

> [!WARNING]
> Como todos os atributos no Azure AD serão substituídos pelo valor local, verifique se os dados no local estão corretos. Por exemplo, se você só gerenciou emails no Office 365 e não manteve atualizado no AD DS local, você perderá os valores no Azure AD/Office 365 não presentes no AD DS.

> [!IMPORTANT]
> Caso use a sincronização de senha, que é sempre usada pelas configurações expressas, a senha no Azure AD será substituída pela a senha do AD local. Se os usuários são usados para gerenciar senhas diferentes, informe-os de que eles devem usar a senha local de quando você instalou o Connect.

A seção anterior e o aviso devem ser considerados no planejamento. Se existem muitas alterações no Azure AD que não refletem no AD DS local, planeje como popular o AD DS com os valores atualizados antes de sincronizar seus objetos com o Azure AD Connect.

Se você correspondeu os objetos com uma correspondência flexível, o **sourceAnchor** será adicionado ao objeto no Azure AD para que uma correspondência rígida possa ser usada posteriormente.

### Correspondência rígida x correspondência flexível
<a id="hard-match-vs-soft-match" class="xliff"></a>
Para uma nova instalação do Connect, não há nenhuma diferença prática entre uma correspondência rígida e uma correspondência flexível. A diferença está em uma situação de recuperação de desastre. Se você tiver perdido o seu servidor com o Azure AD Connect, reinstale uma nova instância para não perder os dados. Um objeto com um sourceAnchor é enviado para o Connect durante a instalação inicial. Então a correspondência poderá ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que fazer o mesmo no Azure AD. Uma correspondência rígida é avaliada pelo Connect e pelo Azure AD. Uma correspondência flexível é avaliada apenas pelo Azure AD.

### Outros objetos que não são usuários
<a id="other-objects-than-users" class="xliff"></a>
Os usuários geralmente têm userPrincipalName e proxyAddresses, facilitando a correspondência. Mas outros objetos, como grupos de segurança, não. Nesse caso, você só pode corresponder com uma correspondência rígida usando o sourceAnchor. O sourceAnchor é sempre o **objectGUID** local convertido de Base64, portanto você deve atualizar o valor no Azure AD quando precisar de dois objetos para corresponder. O sourceAnchor/immutableID só pode ser atualizado com o PowerShell e não pelos portais.

## Criação de um Active Directory local novo a partir dos dados no Azure AD
<a id="create-a-new-on-premises-active-directory-from-data-in-azure-ad" class="xliff"></a>
Alguns clientes começam com uma solução somente em nuvem com o Azure AD e não têm um AD local. Mais tarde, eles desejam consumir recursos locais e criar um AD local com base nos dados do Azure AD. O Azure AD Connect não pode ajudá-lo nesse cenário. Ele não cria os usuários locais e não tem capacidade de definir a senha local igual à do Azure AD.

Se oferecer suporte a LOBs (aplicativos de linha de negócios) é o único motivo para adicionar o AD local, considere usar o [Azure AD Domain Services](../../active-directory-domain-services/index.md).

## Próximas etapas
<a id="next-steps" class="xliff"></a>
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

