<properties
	pageTitle="Azure Active Directory B2C: registro de aplicativos | Microsoft Azure"
	description="Como registrar seu aplicativo com o B2C do Active Directory do Azure"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/30/2016"
	ms.author="swkrish"/>


# Azure Active Directory B2C: registrar seu aplicativo

## Pré-requisito

Para compilar um aplicativo que aceite a inscrição e a entrada do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Azure Active Directory B2C. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todas as etapas indicadas nesse artigo, você terá a folha de recursos B2C fixada em seu Quadro Inicial.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegue até a folha de recursos do B2C

Se a folha de recursos B2C estiver fixada no seu Quadro Inicial, você a verá assim que entrar no [Portal do Azure](https://portal.azure.com/) como o Administrador Global do locatário B2C.

Você também poderá acessar a folha clicando em **Procurar** e em **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Você precisa ser um Administrador Global do locatário do B2C para ser capaz de acessar a folha de recursos do B2C. Um Administrador Global de qualquer outro locatário ou um Usuário de qualquer outro locatário não pode acessá-la. Você pode alternar para o locatário B2C usando o seletor de locatário no canto superior direito do Portal do Azure.

## Registrar um aplicativo

1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar**, na parte superior da folha.
3. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
4. Se você estiver escrevendo um aplicativo baseado na web, alterne o botão **incluir o aplicativo web / API da web** para **Sim**. As **URLs de Resposta** são pontos de extremidade para onde o Azure AD B2C retornará quaisquer tokens que seus aplicativo solicitarem. Por exemplo, insira: `https://localhost:44321/`. Se o aplicativo Web também chamará alguma API Web protegida pelo Azure AD B2C, convém criar também um **Segredo de Aplicativo** clicando no botão **Gerar Chave**.

    > [AZURE.NOTE]
    Um **Segredo de Aplicativo** é uma credencial de segurança importante e deve ser protegido adequadamente.

5. Se você estiver escrevendo um aplicativo móvel, alterne o **incluir cliente nativo** para **Sim**. Copie o **URI de Redirecionamento** padrão criado automaticamente para você.
6. Clique em **Criar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.

> [AZURE.IMPORTANT] Os aplicativos criados na folha de recursos de B2C precisam ser gerenciados no mesmo local. Se você editar aplicativos B2C usando o PowerShell ou outro portal, eles deixarão de ter suporte e não funcionarão com o Azure AD B2C.

## Criar um Aplicativo de Início Rápido

Agora que já tem um aplicativo registrado com o Azure AD B2C, você pode concluir um de nossos tutoriais de início rápido para começar a trabalhar. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0831_2016-->