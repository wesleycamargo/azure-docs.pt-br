---
title: Extensão do Terraform para VS Code do Azure | Microsoft Docs
description: Neste artigo, saiba como instalar e usar a extensão do Terraform no Visual Studio Code.
keywords: terraform, devops, máquina virtual, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190643"
---
# <a name="azure-terraform-vs-code-extension"></a>Extensão do Terraform para VS Code do Azure

A extensão do Terraform para Microsoft Azure Visual Studio Code (VS Code) foi projetada para aumentar a produtividade do desenvolvedor ao criar, testar e usar o Terraform com o Azure. A extensão fornece o suporte de comando do Terraform, visualização gráfica de recurso e integração do CloudShell com o VS Code.

## <a name="what-you-do"></a>O que fazer

- Instale o executável do software livre HashiCorp Terraform em seu computador.
- Instale a extensão do Terraform para VS Code do Azure em sua instalação local do VS Code.

## <a name="what-you-learn"></a>O que você aprenderá

Neste tutorial, você aprenderá:

- Como o Terraform pode automatizar e simplificar o provisionamento dos serviços do Azure.
- Como instalar e usar a extensão do Terraform do Microsoft VS Code para serviços do Azure.
- Como usar o VS Code para gravar, planejar e executar planos do Terraform.

## <a name="what-you-need"></a>O que você precisa

- Um computador executando o Windows 10, Linux ou macOS 10.10+.
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azure.microsoft.com/free/).
- Uma instalação da ferramenta de software livre [Terraform](https://www.terraform.io/) em seu computador local.
  
## <a name="prepare-your-dev-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="install-git"></a>Instalar o Git

Para concluir os exercícios neste artigo, você precisará [instalar o Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Instalar o HashiCorp Terraform

Siga as instruções na página da Web [Instalar o Terraform](https://www.terraform.io/intro/getting-started/install.html) da HashiCorp, que abrange:

- Baixando o Terraform
- Instalando o Terraform
- Verificar se o Terraform é instalado corretamente

>[!Tip]
>Certifique-se de seguir as instruções sobre como configurar sua variável do sistema PATH.

### <a name="install-nodejs"></a>Instalar o Node. js

Para usar o Terraform no Cloud Shell, você precisará [instalar o Node.js](https://nodejs.org/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e digite: `node -v`

### <a name="install-graphviz"></a>Instalar GraphViz

Para usar a função visualizar do Terraform, será necessário [instalar o GraphViz](http://graphviz.org/).

>[!NOTE]
>Para verificar se o GraphViz está instalado, abra uma janela do terminal e digite: `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Instalar a extensão do Terraform para VS Code do Azure:

1. Inicie o VS Code.
2. Clique no ícone **Extensões**.

    ![Botão Extensões](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Use a caixa de texto **Pesquisar extensões no Marketplace** para procurar a extensão do Terraform do Azure:

    ![Pesquisar extensões do VS Code no Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

4. Clique em **Instalar**.

    >[!NOTE]
    >Quando você clica em **Instalar** para a extensão do Terraform do Azure, o VS Code automaticamente instalará a extensão de Conta do Azure. Conta do Azure é um arquivo de dependência para a extensão Terraform do Azure, usado para executar autenticações de assinatura do Azure e extensões de código relacionadas ao Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Verificar se que a extensão do Terraform está instalada no Visual Studio Code

1. Clique no ícone Extensões.
2. Digite `@installed` na caixa de texto de pesquisa.

    ![Extensões instaladas](media/terraform-vscode-extension/tf-installed-extensions.png)

A extensão do Terraform do Azure será exibida na lista de extensões instaladas.

![Extensões do Terraform instaladas](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Agora é possível executar todos os comandos com suporte do Terraform em seu ambiente do Cloud Shell de dentro do VS Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Exercício 1: Passo a passo dos comandos básicos do Terraform

Neste exercício, você cria e executa um arquivo de configuração básico do Terraform que provisiona um novo grupo de recursos do Azure.

### <a name="prepare-a-test-plan-file"></a>Preparar um arquivo de plano de teste

1. No VS Code, selecione **Arquivo > Novo Arquivo** na Barra de Menus.
2. Navegue até [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) e copie o código no bloco de código **Exemplo de Uso**:
3. Cole o código copiado no novo arquivo criado no VS Code.

    ![Colar o código Exemplo de Uso](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Você pode alterar o valor **nome** do grupo de recursos, mas ele deve ser exclusivo para sua assinatura do Azure.

4. Na Barra de Menus, selecione **Arquivo > Salvar Como**.
5. Na caixa de diálogo **Salvar Como**, navegue até um local de sua escolha e, em seguida, clique em **Nova pasta**. (Altere o nome da nova pasta para algo mais descritivo do que *Nova pasta*.)

    >[!NOTE]
    >Neste exemplo, a pasta é nomeada TERRAFORM-TEST-PLAN.

6. Certifique-se de que a nova pasta está realçada (selecionada) e, em seguida, clique em **Abrir**.
7. Na caixa de diálogo **Salvar Como**, altere o nome padrão do arquivo para *main.tf*.

    ![Salvar como main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Clique em **Salvar**.
- Na Barra de Menus, selecione **Arquivo > Abrir Pasta**. Navegue e selecione a nova pasta que você criou.

### <a name="run-terraform-init-command"></a>Executar o comando *init* do Terraform

1. Inicie o Visual Studio Code.
2. Na barra de menus do VS Code, selecione **Arquivo > Abrir Pasta...**  e localize e selecione o arquivo *main.tf*.

    ![arquivo main.TF](media/terraform-vscode-extension/tf-main-tf.png)

3. Na barra de menus, selecione **Exibir > Paleta de Comandos... > Azure Terraform: Init**.
4. Após alguns instantes, quando for perguntado *Você deseja abrir o Cloud Shell?* Clique em **OK**.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. Na primeira vez que iniciar o Cloud Shell em uma nova pasta, você será solicitado a configurar o aplicativo Web. Clique em **Abrir**.

    ![Primeira inicialização do Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. A página Bem-vindo ao Azure Cloud Shell será aberta. Selecione Bash ou PowerShell.

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Neste exemplo, Bash (Linux) foi selecionado.

7. Se você ainda não configurou uma conta de armazenamento do Azure, a tela a seguir será exibida. Clique em **Criar armazenamento**.

    ![Você não tem nenhum armazenamento montado](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que você selecionou anteriormente e exibe informações para a unidade de nuvem que acabou de ser criada.

    ![Sua unidade de nuvem foi criada](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Agora você pode sair do Cloud Shell
10. Na Barra de Menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: init**.

    ![O Terraform foi inicializado com êxito](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualizar o plano

Anteriormente neste tutorial, você instalou o GraphViz. O Terraform pode usar o GraphViz para gerar uma representação visual de uma configuração ou plano de execução. A extensão do Terraform do VS Code do Azure implementa esse recurso por meio do comando *visualizar*.

- Na **Barra de Menus**, selecione **Exibir > Paleta de Comandos > Azure Terraform: Visualize**.

    ![Visualizar o plano](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Executar o comando *plan* do Terraform

O comando *plan* do Terraform é usado para verificar se o plano de execução para um conjunto de alterações fará o que você pretendia.

>[!NOTE]
>O *plano* do Terraform não faz quaisquer alterações nos recursos reais do Azure. Para efetivamente fazer as alterações contidas em seu plano, nós usamos o comando *apply* do Terraform.

- Na Barra de Menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: plan**.

    ![Plano do Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Executar o comando *apply* do Terraform

Depois de estar satisfeito com os resultados do *plano* do Terraform, você pode executar o comando *apply*.

1. Na Barra de Menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: apply**.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Digite *yes*.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Verifique se o plano do Terraform foi executado

Para ver se o novo grupo de recursos do Azure foi criado com êxito:

1. Abra o portal do Azure.
2. Selecione **Grupos de recursos** no painel de navegação esquerdo.

    ![Verificar o novo recurso](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Seu novo grupo de recursos deve estar listado na coluna **NAME**.

>[!NOTE]
>Você pode deixar a janela do portal do Azure aberta por enquanto; nós vamos usá-lo na próxima etapa.

### <a name="run-terraform-destroy-command"></a>Executar o comando *destroy* do Terraform

1. Na Barra de Menus, selecione **Exibir** > **Paleta de Comandos** > **Azure Terraform: destroy**.

    ![Terraform destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Digite *yes*.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Verifique se o grupo de recursos foi destruído

Para confirmar se o Terraform destruiu com êxito o novo grupo de recursos:

1. Clique em **Atualizar** na página *Grupos de recursos* do portal do Azure.
2. O grupo de recursos não será mais listado.

    ![Verificar se o grupo de recursos foi destruído](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Exercício 2: Módulo *compute* do Terraform

Neste exercício, você aprenderá a carregar o módulo *compute* do Terraform no ambiente do VS Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Clonar o módulo terraform-azurerm-compute

1. Use [este link](https://github.com/Azure/terraform-azurerm-compute) para acessar o módulo de Computação do Rm do Azure do Terraform no GitHub.
2. Clique em **Clonar ou baixar**.

    ![Clonar ou baixar](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Neste exemplo, nossa pasta foi nomeada *terraform-azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Abrir a pasta no VS Code

1. Inicie o Visual Studio Code.
2. Na **Barra de Menus**, selecione **Arquivo > Abrir Pasta**, navegue e selecione a pasta que você criou na etapa anterior.

    ![pasta terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Inicializar Terraform

Antes de começar a usar os comandos do Terraform de dentro do VS Code, baixe os plug-ins para dois provedores do Azure: random e azurerm.

1. No painel do Terminal do IDE do VS Code, digite: `terraform init`

    ![comando terraform init](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Digite `az login` e siga as instruções na tela.

### <a name="module-test-lint"></a>Teste de módulo: *lint*

1. Na **Barra de Menus**, selecione **Exibir > Paleta de Comandos > Azure Terraform: Execute Test**.
2. Na lista de opções de tipo de teste, selecione **lint**.

    ![Selecionar o tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. Quando for perguntado *Você deseja abrir o CloudShell?* clique em **OK** e siga as instruções na tela.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando você executa qualquer o teste **lint** ou **ponta a ponta**, o Azure usa um serviço de contêiner para provisionar uma máquina de teste para executar o teste real. Por esse motivo, os resultados do teste normalmente podem levar vários minutos para serem retornados.

Após alguns instantes, você deve ver uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados do teste Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Teste de módulo: *ponta a ponta*

1. Na **Barra de Menus**, selecione **Exibir > Paleta de Comandos > Azure Terraform: Execute Test**.
2. Na lista de opções de tipo de teste, selecione **ponta a ponta**.

    ![Selecionar o tipo de teste](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Se for perguntado *Você deseja abrir o CloudShell?* clique em **OK** e siga as instruções na tela.

    ![Você deseja abrir o Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Quando você executa qualquer o teste **lint** ou **ponta a ponta**, o Azure usa um serviço de contêiner para provisionar uma máquina de teste para executar o teste real. Por esse motivo, os resultados do teste normalmente podem levar vários minutos para serem retornados.

Após alguns instantes, você deve ver uma listagem no painel do Terminal semelhante a este exemplo:

![Resultados do teste de ponta a ponta](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Próximas etapas

Você viu algumas das maneiras em que o Terraform pode simplificar o provisionamento de serviços do Azure de dentro do Visual Studio Code. Agora, você talvez queira examinar alguns desses recursos:
- O [Registro do Módulo do Terraform](https://registry.terraform.io/) lista todos os módulos do Terraform disponíveis para o Azure e outros provedores com suporte.

Para cada um desses módulos, as informações a seguir são fornecidas:

- Uma descrição das funcionalidades gerais do módulo e suas características
- Um exemplo de uso
- As configurações, que mostram como compilar, executar e testar cada módulo no computador de desenvolvimento local
- Um Dockerfile para que você possa criar e executar um ambiente de desenvolvimento do módulo localmente.
