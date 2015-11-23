<properties
   pageTitle="“Ator Desconhecido” dos relatórios do Active Directory do Azure | Microsoft Azure"
   description="Descrição do evento “Actor desconhecido” nos relatórios do Active Directory do Azure"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/09/2015"
   ms.author="kenhoff"/>

# Active Directory do Azure, relatar eventos de “tor desconhecido”

Em raras ocasiões, você poderá ver valores incomuns nos campos "Ator" ou "Usuário" nos relatórios do AD do Azure. Esse comportamento é esperado e é causado por um dos dois eventos:

## Uma entidade de serviço está funcionando no diretório, sem um contexto de usuário

Nesse caso, uma entidade de serviço (aplicativo) está executando as atualizações de diretório sem realmente fazer logon como um usuário. Isso faz com que a ID da entidade de serviço apareça como o ator, em vez de um UPN. Aqui está um exemplo:

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

Isso é um bug conhecido e estamos nos empenhando para resolvê-lo.

## Um usuário foi excluído do diretório antes do evento ser processado

Nesse caso, um usuário foi excluído do diretório antes de processarmos o evento e associado a um nome de usuário com ele. Aqui está um exemplo:

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

Isso é um bug conhecido e estamos nos empenhando para resolvê-lo.

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=Nov15_HO3-->