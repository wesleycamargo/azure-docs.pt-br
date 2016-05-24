<properties
	pageTitle="Visualização do B2C do Active Directory do Azure: registro de aplicativos | Microsoft Azure"
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
	ms.date="05/16/2016"
	ms.author="swkrish"/>


# Visualização do Azure Active Directory B2C: registrar seu aplicativo

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Pré-requisito

Para compilar um aplicativo que aceite a inscrição e a entrada do consumidor, primeiro será necessário registrar o aplicativo em um locatário do Azure Active Directory B2C. Obtenha seu próprio locatário usando as etapas descritas em [Criar um locatário do Azure AD B2C](active-directory-b2c-get-started.md). Depois de seguir todas as etapas indicadas nesse artigo, você terá a folha de recursos B2C fixada em seu Quadro Inicial.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegue até a folha de recursos do B2C

Você pode navegar até a folha de recursos B2C do Portal do Azure ou do Portal Clássico do Azure.

### 1\. Acesso ao Portal do Azure

Se a folha de recursos B2C estiver fixada no seu Quadro Inicial, você a verá assim que entrar no [Portal do Azure](https://portal.azure.com/) como o Administrador Global do locatário B2C.

Você também poderá acessar a folha clicando em **Procurar** e em **Azure AD B2C** no painel de navegação à esquerda no [Portal do Azure](https://portal.azure.com/).

Você também pode acessar a folha diretamente navegando para [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/) em que **{tenant}** deve ser substituído pelo nome usado no momento da criação do locatário (por exemplo, contosob2c). Você pode marcar como favorito esse link para referência futura.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Acesso pelo Portal Clássico do Azure

Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/) como o Administrador da Assinatura. (Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para se inscrever no Azure.) Navegue até a extensão do Active Directory à esquerda e clique no locatário B2C. Na guia **Início Rápido** (a primeira guia que se abre), clique em **Gerenciar configurações de B2C** em **Administrar**. Isso abrirá a folha de recursos do B2C em uma nova janela ou guia do navegador.

Você também pode encontrar o link **Gerenciar configurações de B2C** na seção **Administração de B2C** na guia **Configurar**.

## Registrar um aplicativo

1. Na folha de recursos do B2C no Portal do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar**, na parte superior da folha.
3. Insira um **Nome** para o aplicativo que descreva seu aplicativo para os consumidores. Por exemplo, você poderia digitar "Aplicativo B2C da Contoso".
4. Se você estiver escrevendo um aplicativo baseado na web, alterne o botão **incluir o aplicativo web / API da web** para **Sim**. As **URLs de Resposta** são pontos de extremidade para onde o Azure AD B2C retornará quaisquer tokens que seus aplicativo solicitarem. Por exemplo, insira: `https://localhost:44321/`. Se seu aplicativo incluir um componente do lado do servidor (API) que precisa ser protegido, você desejará criar (e copiar) um **segredo do aplicativo** também clicando no botão **Gerar chave**.

    > [AZURE.NOTE]
    **Segredo do aplicativo** é uma credencial de segurança importante.

5. Se você estiver escrevendo um aplicativo móvel, alterne o **incluir cliente nativo** para **Sim**. Copie o **URI de Redirecionamento** padrão criado automaticamente para você.
6. Clique em **Criar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID de Aplicativo Cliente** globalmente exclusiva que você usará posteriormente no código.

## Criar um Aplicativo de Início Rápido

Agora que já tem um aplicativo registrado com o Azure AD B2C, você pode concluir um de nossos tutoriais de início rápido para começar a trabalhar. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0518_2016-->