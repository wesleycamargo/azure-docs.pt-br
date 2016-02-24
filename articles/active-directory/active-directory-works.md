<properties
	pageTitle="Como o Active Directory do Azure funciona? | Microsoft Azure"
	description="O Active Directory do Azure cria um cenário de identidade que é seu na nuvem. Ele pode ser conectado ao seu sistema de identidades local ou usado de modo independente."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="curtand"/>



# Como o Active Directory do Azure funciona?

###Outros artigos sobre este tópico
[O que é o AD do Azure?](active-directory-whatis.md)<br> [Como ele funciona?](active-directory-works.md)<br> [Introdução](active-directory-get-started.md)<br> [Próximas etapas](active-directory-next-steps.md)<br> [Saiba mais](active-directory-learn-map.md)


O Active Directory do Azure (AD do Azure) cria um cenário de identidade que é seu na nuvem. Ele pode ser conectado ao seu sistema de identidades local ou usado de modo independente.

Você pode pensar em uma conta no AD do Azure como sua carteira de motorista para a nuvem: é sua ID exclusiva para acessar os serviços online. Nesse sentido, o AD do Azure funciona como seu registrador particular na nuvem para essas carteiras de motorista. Ele permite que identidades sejam usadas em qualquer lugar na nuvem, além de melhorar a mobilidade para os usuários que acessam recursos locais.

> [AZURE.NOTE] Para usar o Active Directory do Azure, você precisa de uma conta do Azure. Se você ainda não tem uma conta, você pode [inscrever-se para uma conta gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## Como o AD do Azure dá suporte ao Office 365, Microsoft Intune e outros serviços do Azure?
O Portal do Azure, o Centro de Administração do Office 365, o portal de conta do Microsoft Intune e os cmdlets módulo PowerShell do AD do Azure, todos eles realizam leituras e gravações de uma única instância compartilhada do AD do Azure que está associada ao seu diretório. Os portais (ou cmdlets) agem como uma interface de front-end que recebe e/ou modifica suas informações de diretório. [Saiba mais sobre o suporte para outros serviços](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Como o AD do Azure dá suporte a aplicativos de terceiros?
O AD do Azure simplifica a autenticação para desenvolvedores, fornecendo identidade como um serviço, juntamente com as bibliotecas de software livre para plataformas diferentes para lhe ajudar a começar a codificar rapidamente. [Saiba mais sobre cenários de autenticação do AD do Azure](active-directory-authentication-scenarios.md).


## Como o AD do Azure estende meu diretório local?
O AD do Azure dá suporte a vários dos protocolos de autenticação e autorização mais usados. [Saiba mais sobre os protocolos de autenticação do Active Directory do Azure](active-directory-authentication-scenarios.md).

## Como Azure me ajuda a gerenciar identidades?
Quer saber mais sobre como gerenciar o AD do Azure? Como obter um diretório? Como excluir um diretório? Como gerenciar dados de diretório? Saiba mais sobre a administração de diretório do AD do Azure. [Saiba mais sobre como administrar o AD do Azure](active-directory-administer.md).

## Recursos adicionais

* [Inscrever-se no Azure como uma organização](sign-up-organization.md)
* [Identidade do Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0128_2016-->