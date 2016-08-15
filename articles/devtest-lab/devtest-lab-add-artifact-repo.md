<properties
	pageTitle="Adicionar um repositório de artefatos do Git a um laboratório | Microsoft Azure"
	description="Adicione um repositório Git do GitHub ou do Visual Studio Team Services para seus artefatos personalizados para seus Laboratórios de Desenvolvimento/Teste"
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
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Adicionar um repositório de artefatos do Git a um laboratório

> [AZURE.NOTE] Assista ao vídeo que acompanha este artigo: [How to add your private artifacts repository to a lab in DevTest Labs](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab) (Como adicionar seu repositório de artefatos privado a um laboratório no DevTest Labs)

## Visão geral

Por padrão, um laboratório inclui artefatos do repositório de artefatos de Laboratórios de Desenvolvimento/Teste oficiais do Azure. Você pode adicionar um repositório de artefatos do Git ao laboratório para incluir os artefatos que a sua equipe cria. O repositório pode ser hospedado no [GitHub](https://github.com) ou no [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Para saber como criar um repositório no GitHub, confira o [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Para saber como criar um projeto do Team Services com um Repositório Git, veja [Conectar ao Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A captura de tela a seguir mostra um exemplo da aparência de um repositório contendo artefatos no GitHub: ![Repositório de artefatos de exemplo no GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Adicionar um repositório de artefatos do GitHub ao seu laboratório

Para adicionar um repositório de artefatos do GitHub ao seu laboratório, você primeiro deve obter a URL clone HTTPS e o Token de Acesso pessoal do repositório de artefatos e inserir essas informações no seu laboratório

### Obter a URL de clone do repositório GitHub e token de acesso pessoal

1. Na home page do repositório GitHub que contém os artefatos da equipe, salve a **URL clone HTTPS** para uso posterior.

1. Selecione a imagem de perfil no canto superior direito e, em seguida, **Configurações**.

1. No menu **Configurações pessoais** à esquerda, selecione **Tokens de acesso pessoal**.

1. Selecione **Gerar novo token**.

1. Na página **Novo token de acesso pessoal**, insira uma **Descrição do token**, aceite os itens padrão em **Selecionar escopos** e escolha **Gerar Token**.

1. Salve o token gerado, pois você precisará dele mais tarde.

1. Você pode fechar o GitHub agora.

###Conectar o seu laboratório ao repositório do GitHub

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar**, e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha do laboratório, selecione **Configurações**.

1. Na folha **Configurações** do laboratório, selecione **Repositório de Artefatos**.

1. Na folha **Repositório de Artefatos**:

    1. Insira um **Nome** para o repositório.
    1. Insira a **Url de Clone de Git**.
    2. Insira o **Caminho da Pasta** no repositório de artefatos que contém os artefatos.
    3. Insira o **Token de Acesso Pessoal** salvo no repositório de artefatos.
    4. Selecione **Salvar**.

Os artefatos no seu repositório agora estão listados na folha **Adicionar Artefatos**.

## Adicionar um repositório de artefatos Git do Visual Studio ao seu laboratório

Para adicionar um repositório de artefatos Git do Visual Studio no seu laboratório, você primeiro deve obter a URL clone HTTPS e o Token de Acesso pessoal do repositório de artefatos e inserir essas informações no seu laboratório.

### Na página da Web do Visual Studio do seu projeto de artefato

1. Abra a home page de sua coleção de equipe (por exemplo, `https://contoso-web-team.visualstudio.com`) e selecione o projeto de artefato.

2. Na home page do projeto, selecione **Código**.

1. Para exibir a URL de clone, na página **Código** do projeto, selecione **Clone**.

1. Salve a URL, você precisará dela mais tarde neste tutorial.

1. Para criar um Token de Acesso Pessoal, selecione **Meu perfil** no menu suspenso da conta de usuário.

1. Na página de informações de perfil, selecione **Segurança**.

1. Na guia **Segurança**, selecione **Adicionar**.

1. Na página **Criar um token de acesso pessoal**:

    1. Insira uma **Descrição** para o token.
    2. Selecione **180 dias** na lista **Expira em**.
    3. Escolha **Todas as contas acessíveis** na lista **Contas**.
    4. Escolha a opção **Todos os escopos**.
    5. Escolha **Criar Token**.

1. Quando terminar, o novo token será exibido na lista de **Tokens de Acesso Pessoal**. Selecione **Copiar Token** e salve o valor do token, pois ele será usado em breve.

### No laboratório

1. Na folha do laboratório, selecione **Configurações**.

    ![Escolha Configurações](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Na folha **Configurações**, selecione **Repositório de Artefatos**.

1. Na folha **Repositório de Artefatos**

    1. Insira um **Nome** de exibição para o repositório.
    1. Insira a **Url de Clone de Git**.
    2. Insira o **Caminho da Pasta** no repositório de artefatos que contém os artefatos.
    3. Insira o **Token de Acesso Pessoal** salvo no repositório de artefatos.
    4. Selecione **Salvar**.

## Postagens de blogs relacionadas
- [Como solucionar problemas de falha de Artefatos no AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Ingressar uma VM ao domínio de AD existente usando o modelo do ARM no Laboratório de Teste de Desenvolvimento do Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0803_2016-->