<properties
	pageTitle="Adicionar um repositório de artefatos do Git ao seu Laboratório Desenvolvimento/Teste | Microsoft Azure"
	description="Adicione um repositório Git do GitHub ou do Visual Studio Team Services para seus artefatos personalizados para o laboratório"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# Adicionar um repositório de artefatos do Git ao seu Laboratório de Desenvolvimento/Teste

> [AZURE.NOTE] Clique no link a seguir para exibir o vídeo que acompanha este artigo: [como adicionar seu Repositório de Artefatos privado a um Laboratório de Desenvolvimento/Teste](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab)

## Visão geral

Por padrão, um Laboratório de Desenvolvimento/Teste inclui artefatos do repositório de artefatos de Laboratório de Desenvolvimento/Teste oficial do Azure. Você pode adicionar um repositório de artefatos do Git ao laboratório para incluir os artefatos que a sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou no [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Para saber como criar um repositório no GitHub, confira o [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Para saber como criar um projeto do Team Services com um Repositório Git, veja [Conectar ao Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A captura de tela a seguir mostra um exemplo da aparência de um repositório contendo artefatos no GitHub: ![Repositório de artefatos de exemplo no GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Adicionar um repositório de artefatos do GitHub ao seu laboratório

Para adicionar um repositório de artefatos do GitHub ao seu laboratório, você primeiro deve obter a URL clone HTTPS e o Token de Acesso pessoal do repositório de artefatos e inserir essas informações no seu laboratório

### Obter a URL de clone do repositório GitHub e token de acesso pessoal

1. Na home page do repositório GitHub que contém os artefatos da equipe, salve a **URL clone HTTPS** para uso posterior.

1. Toque na imagem de perfil no canto superior direito e selecione **Configurações**.

1. No menu **Configurações pessoais** à esquerda, toque em **Tokens de acesso pessoal**.

1. Toque em **Gerar novo token**.

1. Na página **Novo token de acesso pessoal**, insira uma **Descrição do token**, aceite os itens padrão em **Selecionar escopos** e escolha **Gerar Token**.

1. Salve o token gerado, pois você precisará dele mais tarde.

1. Você pode fechar o GitHub agora.

###Conectar o seu laboratório ao repositório do GitHub

1. Entre no [portal de visualização do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Na folha de laboratório, toque em **Configurações**.

1. Na folha **Configurações** do laboratório, toque em **Repositório de Artefatos**.

1. Na folha **Repositório de Artefatos**:

    1. Insira um **Nome** para o repositório.
    1. Insira a **Url de Clone de Git**.
    2. Insira o **Caminho da Pasta** no repositório de artefatos que contém os artefatos.
    3. Insira o **Token de Acesso Pessoal** salvo no repositório de artefatos.
    4. Toque em **Salvar**.

Os artefatos no seu repositório agora estão listados na folha **Adicionar Artefatos**.

## Adicionar um repositório de artefatos Git do Visual Studio ao seu laboratório

Para adicionar um repositório de artefatos Git do Visual Studio no seu laboratório, você primeiro deve obter a URL clone HTTPS e o Token de Acesso pessoal do repositório de artefatos e inserir essas informações no seu laboratório.

### Na página da Web do Visual Studio do seu projeto de artefato

1. Abra a home page da sua coleção de equipe (por exemplo, `https://contoso-web-team.visualstudio.com`) e toque no projeto de artefato.

2. Na home page do projeto, toque em **Código**.

1. Para exibir a URL de clone, na página de **Código** do projeto, toque em **Clone**.

1. Salve a URL, você precisará dela mais tarde neste tutorial.

1. Para criar um Token de acesso pessoal, toque em **Meu perfil** no menu suspenso de conta de usuário.

1. Na página de informações de perfil, toque em **Segurança**.

1. Clique na guia **Segurança** e toque em **Adicionar**.

1. Na página **Criar um token de acesso pessoal**:

    1. Insira uma **Descrição** para o token.
    2. Selecione **180 dias** na lista **Expira em**.
    3. Escolha **Todas as contas acessíveis** na lista **Contas**.
    4. Escolha a opção **Todos os escopos**.
    5. Escolha **Criar Token**.

1. Quando terminar, o novo token será exibido na lista de **Tokens de Acesso Pessoal**. Toque em **Copiar Token** e salve o valor do token, pois ele será usado em breve.

### No Laboratório de Desenvolvimento/Teste

1. Na folha do seu laboratório, toque em **Configurações**.

    ![Escolha Configurações](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Na folha **Configurações**, toque em **Repositório de Artefatos**.

1. Na folha **Repositório de Artefatos**

    1. Insira um **Nome** de exibição para o repositório.
    1. Insira a **Url de Clone de Git**.
    2. Insira o **Caminho da Pasta** no repositório de artefatos que contém os artefatos.
    3. Insira o **Token de Acesso Pessoal** salvo no repositório de artefatos.
    4. Toque em **Salvar**.

<!---HONumber=AcomDC_0224_2016-->