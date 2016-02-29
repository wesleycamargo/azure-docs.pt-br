
<properties
	pageTitle="Referência técnica: acesso condicional a aplicativos do AD do Azure | Microsoft Azure"
	description="Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/11/2016"
	ms.author="femila"/>

# Referência técnica: acesso condicional a aplicativos do AD do Azure

## Serviços habilitados com acesso condicional
Regras de acesso condicional têm suporte em vários tipos de aplicativos do AD do Azure. Essa lista inclui:

- Aplicativos federados da galeria de aplicativos do Azure AD
- Aplicativos de SSO de senha da galeria de aplicativos do Azure AD
- Aplicativos registrados com o Proxy de Aplicativo do Azure
- Aplicativos de linha de negócios e de multilocação desenvolvidos registrados no AD do Azure
- Visual Studio Online
- Aplicativo Remoto do Azure

## Habilitar regras de acesso

Cada regra pode ser habilitada ou desabilitada com base no aplicativo. Quando as regras estão “ATIVADAS”, elas são habilitadas e impostas aos usuários que acessam o aplicativo. Quando estão “DESATIVADAS”, elas não são usadas e não afetam a experiência de entrada dos usuários.

## Aplicar regras a usuários específicos
As regras podem ser aplicadas a conjuntos específicos de usuários baseadas no grupo de segurança ao definir “Aplicar a”. “Aplicar a” pode ser definido como “Todos os Usuários” ou “Grupos”. Quando definido como “Todos os Usuários”, as regras serão aplicadas a qualquer usuário com acesso ao aplicativo. A opção “Grupos” permite que grupos de segurança e de distribuição específicos sejam selecionados e as regra sejam impostas somente para esses grupos. Ao implantar uma regra pela primeira vez, é comum aplicar primeiro um conjunto limitado de usuários, que são membros de um grupo piloto. Depois de concluída, a regra pode ser aplicada a "Todos os Usuários". Isso fará com que a regra seja imposta a todos os usuários na organização. Alguns grupos também podem ser isentos da política usando a opção “Exceto”. Todos os membros desses grupos estarão isentos mesmo que apareçam em um grupo incluído.

## Redes "No trabalho"

As regras de Acesso Condicional que usam uma rede "No trabalho", dependem de intervalos de IP confiáveis que foram configurados no AD do Azure. Essas regras incluem:

- Exigir autenticação multifator quando não está no trabalho
- Bloquear acesso quando não estiver no trabalho

Os intervalos IP confiáveis podem ser configurados na [página de configurações de autenticação multifator](../multi-factor-authentication/multi-factor-authentication-whats-next.md) A política de acesso condicional usará os intervalos configurados em cada solicitação de autenticação e emissão de token para avaliar as regras. A declaração corpnet interior não é usada porque ela não está disponível para sessões mais longas, como tokens de atualização em aplicativos móveis.

## Regras por aplicativo
As regras são configuradas por aplicativo, permitindo que os serviços de alto valor sejam protegidos sem afetar o acesso a outros serviços. As regras de acesso condicional podem ser configuradas em uma guia “Configurar” do aplicativo.

As regras oferecidas atualmente são:

- Exigir autenticação multifator
 - Todos os usuários aos quais a política se aplica terão que ter realizado a autenticação multifator pelo menos uma vez.
- Exigir autenticação multifator quando não está no trabalho
 - Todos os usuários aos quais a política se aplica terão que ter realizado a autenticação multifator pelo menos uma vez se acessarem o serviço de um local remoto. Se os usuários saírem de um local de trabalho para um local remoto, precisarão realizar a autenticação multifator ao acessar o serviço.
- Bloquear acesso quando não estiver no trabalho 
 - Todos os usuários aos quais a política se aplica serão bloqueados ao acessar o serviço de um local remoto. Se eles saírem de um local de trabalho para um local remoto. Eles terão acesso em um local de trabalho.

<!---HONumber=AcomDC_0218_2016-->