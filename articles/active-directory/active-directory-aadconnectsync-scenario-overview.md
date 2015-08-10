<properties
	pageTitle="Azure AD Connect Sync: visão geral de cenário com várias florestas"
    description="O objetivo deste tópico é abordar alguns cenários comuns e como eles são representados no serviço de sincronização do Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync: visão geral de cenário com várias florestas

Muitas organizações têm ambientes que incluem várias florestas do Active Directory local. Há vários motivos para ter mais de uma floresta do Active Directory local implantada. Os exemplos típicos são designs com florestas de conta-recurso, florestas relacionadas a fusões e aquisições ou florestas usadas para terceirizar dados.

A Microsoft fornece a você o DirSync (uma solução para cenários de floresta única) e o FIM (uma solução para cenários de várias florestas). No entanto, configurar o FIM pode ser um desafio e pode levar uma quantidade considerável de tempo.

Com o Azure AD Connect Sync, você pode simplificar significativamente a configuração e torná-la mais previsível.

Sobre a configuração padrão entregue pelo Azure AD Connect Sync, faz-se as seguintes suposições:

1. Os usuários têm apenas uma conta habilitada e a floresta em que essa conta se encontra é usada para federar o usuário.
2. Os usuários têm apenas uma caixa de correio.
3. A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (Lista de Endereços Global) do Exchange. Se não houver nenhuma caixa de correio no usuário, qualquer floresta pode ser usada para contribuir com esses valores de atributos.


O objetivo deste tópico é abordar alguns cenários comuns e como eles são representados no serviço de sincronização do Azure AD Connect Sync:

1. Tecnologias distintas 
2. Malha completa com GALSync opcional 
3. Floresta de conta-recurso 


## Tecnologias distintas

Nesse ambiente, todas as florestas locais são tratados como entidades separadas e nenhum usuário estaria presente em nenhuma outra floresta.<br> Cada floresta tem sua própria organização do Exchange e não há nenhum GALSync entre as florestas. Essa pode ser a situação após uma fusão/aquisição ou em uma organização em que cada unidade de negócios está operando isolada das outras.

![Tecnologias distintas][1]

Nesta figura, cada objeto em cada floresta será representado uma vez no metaverso e agregado no diretório de destino do AD do Azure. Isso teria o mesmo resultado final que conectar um servidor DirSync a cada floresta do AD de origem.
 




## Malha completa com GALSync opcional

Uma topologia de malha completa permite que usuários e recursos sejam localizados em qualquer floresta; normalmente, haveriam seria relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta pode haver, opcionalmente, uma solução GALSync que represente um usuário em uma floresta como um contato em outra floresta.

Nesse cenário, os objetos de identidade normalmente são unidos usando o atributo de email. Como resultado, um usuário com uma caixa de correio em uma floresta é unido aos contatos em outras florestas. Grupos de distribuição e de segurança podem ser encontradas em cada floresta e podem conter uma mescla de usuários, contatos e FSPs (entidades de segurança externas).

A figura a seguir demonstra esse cenário.

![Malha completa com GALSync opcional][2]


## Floresta de conta-recurso
Em uma topologia de floresta de conta-recurso, você tem uma ou mais florestas de conta com contas de usuário ativas.<br> Este cenário inclui uma floresta que confia em todas as florestas de conta. Essa floresta normalmente tem um esquema do AD estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, bem como outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nessa floresta e a caixa de correio está vinculada à floresta de conta.

A figura a seguir demonstra esse cenário com apenas uma conta.

![Floresta de conta-recurso][3]


## Recursos adicionais

* [Azure AD Connect Sync: personalizando opções de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=July15_HO5-->