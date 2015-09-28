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
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Visualização do Active Directory B2C do Azure: como registrar seu aplicativo

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Pré-requisito

Para criar um aplicativo que aceita a inscrição e a conexão do consumidor, primeiro será necessário registrá-lo em um diretório do Active Directory B2C do Azure. Obtenha seu próprio diretório usando as etapas descritas [aqui](active-directory-b2c-get-started.md). Se tiver seguido todas as etapas nesse artigo, você deverá ter a folha de recursos do B2C fixada em seu Quadro Inicial.

## Navegue até a folha de recursos do B2C

Você pode navegar para a folha de recursos do B2C de uma das duas maneiras a seguir.

### 1\. Diretamente no Portal de Visualização do Azure

Se tiver a folha de recursos do B2C fixada em seu Quadro Inicial, você a verá assim que entrar no [portal de visualização do Azure](https://portal.azure.com/) como o Administrador Global do diretório B2C.

Você também pode acessar a folha de recursos do B2C diretamente navegando para [https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com), em que **{directory}** deve ser substituído pelo nome usado no momento da criação do diretório (por exemplo, contosob2c) e entrando como o Administrador Global do diretório B2C. Você pode marcar esse link para referência futura.

### 2\. Acesso por meio do Portal do Azure

Entre no [portal do Azure](https://manage.windowsazure.com/) como o Administrador de Assinatura (essa é a mesma conta do trabalho, da escola ou da Microsoft que você usou para inscrever-se no Azure). Navegue até a extensão do Active Directory à esquerda e clique no diretório B2C. Na guia **Início Rápido** (a primeira guia que é aberta), clique em **Gerenciar Configurações B2C** em **Administrar**. Isso abrirá a folha de recursos do B2C em uma nova janela do navegador ou guia.

Você também pode encontrar o link **Gerenciar configurações B2C** (na seção **Administração B2C**) na guia **Configurar**.

## Registrar um aplicativo

1. Na folha de recursos do B2C no portal de visualização do Azure, clique em **Aplicativos**.
2. Clique em **+Adicionar**, na parte superior da folha.
3. O **Nome** do aplicativo descreverá seu aplicativo para os consumidores. Por exemplo, digite "Aplicativo B2C da Contoso".
4. Se você estiver escrevendo um aplicativo baseado na Web, alterne a opção **Incluir aplicativo Web/API Web** para **Sim**. As **URLs de resposta** são pontos de extremidade em que o B2C do AD do Azure retornará quaisquer tokens que seu aplicativo solicitar. Por exemplo, insira `https://localhost:44321/`. Se seu aplicativo incluir um componente do lado do servidor (API) que precise ser protegido, convém criar (e copiar) também um **Segredo de Aplicativo** clicando no botão **Gerar Chave**.

    > [AZURE.NOTE]O **Segredo do Aplicativo** é uma credencial de segurança importante.

5. Se você estiver escrevendo um aplicativo móvel, alterne a opção **Incluir cliente nativo** para **Sim**. Copie o **URI de redirecionamento** padrão criado automaticamente para você.
6. Clique em **Criar** para registrar o aplicativo.
7. Clique no aplicativo que você acabou de criar e copie a **ID do aplicativo** global exclusiva que você usará posteriormente no código.

## Criar um Aplicativo de Início Rápido

Agora que tem um aplicativo registrado com o B2C do AD do Azure, você pode concluir um de nossos tutoriais de início rápido para começar a trabalhar. Aqui estão algumas recomendações:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=Sept15_HO3-->