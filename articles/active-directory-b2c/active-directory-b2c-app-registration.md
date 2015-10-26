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
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: como registrar seu aplicativo

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Pré-requisito

Para criar um aplicativo que aceita a inscrição e a conexão do consumidor, primeiro será necessário registrá-lo em um locatário do Active Directory B2C do Azure. Obtenha seu próprio locatário usando as etapas descritas [aqui](active-directory-b2c-get-started.md). Se tiver seguido todas as etapas nesse artigo, você deverá ter a folha de recursos do B2C fixada em seu Quadro Inicial.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Navegue até a folha de recursos do B2C

Você pode navegar para a folha de recursos do B2C de uma das duas maneiras a seguir.

### 1\. Diretamente no Portal de Visualização do Azure

Se você tiver a folha de recursos B2C fixada em seu quadro inicial, você a verá assim que entrar no [Portal de visualização do Azure](https://portal.azure.com/) como o Administrador Global do locatário B2C.

Você também pode acessar a folha de recursos B2C diretamente, navegando para [https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) em que **{tenant}** deve ser substituído pelo nome usado no momento da criação do locatário (por exemplo, contosob2c) e fazer logon como o Administrador Global do locatário B2C. Você pode marcar esse link para referência futura (veja a observação abaixo).

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a User from any tenant cannot access it.

### 2\. Acesso por meio do Portal do Azure

Entre no [portal do Azure](https://manage.windowsazure.com/) como o administrador de assinatura (esse é o mesmo trabalho ou conta da escola ou a mesma Conta da Microsoft que você usou para inscrever-se no Azure). Navegue até a extensão do Active Directory à esquerda e clique no locatário B2C. Na guia **Início rápido** (a primeira guia que abre), clique em **Gerenciar configurações B2C** em **Administrar**. Isso abrirá a folha de recursos do B2C em uma nova janela do navegador ou guia.

Você também pode encontrar o link **Gerenciar configurações B2C** (na seção **Administração de B2C**) na guia **Configurar**.

## Registrar um aplicativo

1. Na folha de recursos B2C no portal de visualização do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar** na parte superior da folha.
3. O **Nome** do aplicativo descreverá seu aplicativo para os consumidores. Por exemplo, digite "Aplicativo B2C da Contoso".
4. Se você estiver escrevendo um aplicativo baseado na web, alterne o botão **incluir o aplicativo web / API da web** para **Sim**. O **Reply URLs** são pontos de extremidade em que o AD B2C do Azure retornará quaisquer tokens de suas solicitações de aplicativo. Por exemplo, insira: `https://localhost:44321/`. Se seu aplicativo incluir um componente do lado do servidor (API) que precisa ser protegido, você desejará criar (e copiar) um **segredo do aplicativo** também clicando no botão **Gerar chave**.

    > [AZURE.NOTE]**Segredo do aplicativo** é uma credencial de segurança importante.

5. Se você estiver escrevendo um aplicativo móvel, alterne o **incluir cliente nativo** para **Sim**. Copie o padrão **URI de redirecionamento** criado automaticamente para você.
6. Clique em **Criar** para registrar seu aplicativo.
7. Clique no aplicativo que você acabou de criar e copie o identificador global exclusivo **ID do aplicativo** que você usará posteriormente em seu código.

## Criar um Aplicativo de Início Rápido

Agora que tem um aplicativo registrado com o B2C do AD do Azure, você pode concluir um de nossos tutoriais de início rápido para começar a trabalhar. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=Oct15_HO3-->