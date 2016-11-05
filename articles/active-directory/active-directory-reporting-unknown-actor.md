---
title: 'Active Directory do Azure relatando '
;ator: ''
desconhecido";: ''
'|': ''
microsoft: ''
azure": ''
description: 'Descrição do evento '
;actor: ''
nos: ''
relatórios: ''
do: ''
active: ''
directory: ''
services: active-directory
documentationcenter: ''
author: SSalahAhmed
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: saah

---
# Active Directory do Azure relatando eventos de "Ator desconhecido"
*Esta documentação é parte do [Guia de Relatórios do Active Directory do Azure](active-directory-reporting-guide.md).*

Em raras ocasiões, você poderá ver valores incomuns nos campos "Ator" ou "Usuário" nos relatórios do AD do Azure. Esse comportamento é esperado e é causado por um dos dois eventos:

## Uma entidade de serviço está funcionando no diretório, sem um contexto de usuário
Nesse caso, uma entidade de serviço (aplicativo) está executando as atualizações de diretório sem realmente fazer logon como um usuário. Isso faz com que a ID da entidade de serviço apareça como o ator, em vez de um UPN. Aqui está um exemplo:

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Isso é um bug conhecido e estamos trabalhando para resolvê-lo.

## Um usuário foi excluído do diretório antes do evento ser processado
Nesse caso, um usuário foi excluído do diretório antes de processarmos o evento e associarmos a um nome de usuário a ele. Aqui está um exemplo:

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Isso é um bug conhecido e estamos trabalhando para resolvê-lo.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=AcomDC_0921_2016-->