---
title: Criar um modelo de base do Terraform no Azure usando o Yeoman
description: Saiba como criar um modelo de base do Terraform no Azure usando o Yeoman.
services: terraform
ms.service: terraform
keywords: terraform, devops, máquina virtual, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 9ef27166e84192dec81fd8f8da508785342ffefc
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288009"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Criar um modelo de base do Terraform no Azure usando o Yeoman

O [Terraform](https://docs.microsoft.com/azure/terraform/
) fornece uma maneira de criar a infraestrutura no Azure facilmente. O [Yeoman](http://yeoman.io/) facilita consideravelmente o trabalho do desenvolvedor do módulo na criação de módulos do Terraform, oferecendo uma ótima estrutura de *melhores práticas*.

Neste artigo, você aprenderá a usar o gerador de módulo do Yeoman para criar um modelo base do Terraform. Depois, você aprenderá a testar seu novo modelo do Terraform usando dois métodos diferentes:

- Executar seu módulo do Terraform usando um arquivo do Docker que você cria neste artigo.
- Executar seu módulo do Terraform nativamente no Azure Cloud Shell.

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: caso não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- **Visual Studio Code**: usaremos o [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) para examinar os arquivos criados pelo gerador do Yeoman. No entanto, você poderá usar qualquer editor de código que desejar.
- **Terraform**: você precisará de uma instalação do [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) para executar o módulo criado pelo Yeoman.
- **Docker**: usaremos o [Docker](https://www.docker.com/get-started) para executar o módulo criado pelo gerador do Yeoman. (Se preferir, você poderá usar o Ruby no lugar do Docker para executar o exemplo de módulo.)
- **Linguagem de programação Go**: você precisará de uma instalação do [Go](https://golang.org/) porque os casos de teste gerados pelo Yeoman são escritos em linguagem Go.

>[!NOTE]
>A maioria dos procedimentos neste tutorial envolve entradas de linha de comando. As etapas descritas aqui se aplicam a todos os sistemas operacionais e as ferramentas de linha de comando. Em nossos exemplos, optamos por usar o PowerShell para o ambiente loca e Git Bash para o ambiente do cloud shell.

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

### <a name="install-nodejs"></a>Instalar o Node. js

Para usar o Terraform no Cloud Shell, você precisará [instalar o Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e insira `node --version`.

### <a name="install-yeoman"></a>Instalar o Yeoman

Em um prompt de comando, insira `npm install -g yo`

![Instalar o Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Instalar o modelo do Yeoman para o módulo do Terraform

Em um prompt de comando, insira `npm install -g generator-az-terra-module`.

![Install generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Para verificar se o Yeoman está instalado, digite `yo --version` em uma janela de terminal.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Criar uma pasta vazia para manter o módulo gerado pelo Yeoman

O modelo do Yeoman gera arquivos no **diretório atual**. Por esse motivo, você precisará criar um diretório.

>[!Note]
>Esse diretório vazio precisa ser colocado em $GOPATH/src. Você encontrará instruções [aqui](https://github.com/golang/go/wiki/SettingGOPATH) para fazer isso.

Em um prompt de comando:

1. Navegue até o diretório pai que conterá o diretório novo e vazio que estamos prestes a criar.
1. Digite `mkdir <new-directory-name>`.

    >[!NOTE]
    >Substitua <new-directory-name> pelo nome do novo diretório. Neste exemplo, nomeamos o novo diretório `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Vá para o novo diretório digitando `cd <new directory's name>`e pressionando **Enter**.

    ![Navegue até o novo diretório](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Para verificar se o diretório está vazio, digite `ls`. Não deve haver nenhum arquivo listado no resultado desse comando.

## <a name="create-a-base-module-template"></a>Criar um modelo de base do módulo

Em um prompt de comando:

1. Digite `yo az-terra-module`.

1. Siga as instruções na tela para fornecer as seguintes informações:

    - *Nome do projeto de módulo do Terraform*

        ![Nome do projeto](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >Neste exemplo, inserimos `doc-sample-module`.

    - *Você gostaria de incluir o arquivo de imagem do Docker?*

        ![Incluir arquivo de imagem do Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Digite `y`. Se você selecionar **n**, o código do módulo gerado dará suporte à execução apenas no modo nativo.

3. Insira `ls` para exibir os arquivos resultantes que forem criados.

    ![Listar arquivos criados](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Rever o código do módulo gerado

1. Iniciar o Visual Studio Code

1. Na barra de menus, selecione **Arquivo > Abrir Pasta** e selecione a pasta criada.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Vamos dar uma olhada em alguns dos arquivos que foram criados pelo gerador de módulo do Yeoman.

>[!Note]
>Neste artigo, usaremos os arquivos main.tf, variables.tf e outputs.tf como criados pelo gerador de módulo do Yeoman. No entanto, ao criar seus próprios módulos, você deve editar esses arquivos para acomodar a funcionalidade do seu módulo do Terraform. Para ver uma discussão mais detalhada desses arquivos e seu uso, consulte [Terratest em módulos do Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Define um módulo chamado *random-shuffle*. A entrada é uma *string_list*. A saída é a contagem de permutações.

### <a name="variablestf"></a>variables.tf

Define as variáveis de entrada e saída usadas pelo módulo.

### <a name="outputstf"></a>outputs.tf

Define o que o módulo gera. Aqui, ele é o valor retornado por **random_shuffle**, que é um módulo interno do Terraform.

### <a name="rakefile"></a>Rakefile

Define as etapas de compilação. As etapas incluem:

- **build**: valida a formatação do arquivo main.tf.
- **unidade**: o esqueleto do módulo gerado não inclui o código para um teste de unidade. Se você quiser especificar um cenário de teste de unidade, adicione o código aqui.
- **e2e**: executa um teste de ponta a ponta no módulo.

### <a name="test"></a>test

- Os casos de teste são escritos em linguagem Go.
- Todos os códigos de teste são testes de ponta a ponta.
- Os testes de ponta a ponta tentam usar o Terraform para provisionar todos os itens definidos em **fixture** e comparam a saída no código **template_output.go** com os valores predefinidos esperados.
- **Gopkg.Lock** e **Gopkg.toml**: definem as dependências. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testar o novo módulo do Terraform usando um arquivo do Docker

>[!NOTE]
>Em nosso exemplo, estamos executando o módulo como um módulo local e não estamos lidando com o Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Confirmar se o Docker está instalado e em execução

Em um prompt de comando, insira `docker version`.

![Versão do Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

A saída resultante confirma que o Docker está instalado.

Para confirmar que o Docker está realmente em execução, digite `docker info`.

![Informações do Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Configurar um contêiner do Docker

1. Em um prompt de comando, insira

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    A mensagem **Criado com êxito** será exibida.

    ![Criado com êxito](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. No prompt de comando, insira `docker image ls`.

    Você verá o módulo recém-criado *terra-mod-example* listado.

    ![Resultados do repositório](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >O nome do módulo, *terra-mod-example*, foi especificado no comando que você inseriu na etapa 1 acima.

1. Digite `docker run -it terra-mod-example /bin/sh`.

    Agora, você está em execução no Docker e pode listar o arquivo digitando `ls`.

    ![Listar arquivo do Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Criar o módulo

1. Digite `bundle install`.

    Aguarde a mensagem **Pacote concluído** e continue na próxima etapa.

1. Digite `rake build`.

    ![Build do Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Executar o teste de ponta a ponta

1. Digite `rake e2e`.

1. Após alguns instantes, a mensagem **APROVADO** será exibida.

    ![APROVADO](media/terraform-vscode-module-generator/ymg-pass.png)

1. Insira `exit` para concluir o teste de ponta a ponta e sair do ambiente do Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Usar o gerador do Yeoman para criar e testar um módulo no Cloud Shell

Na seção anterior, você aprendeu a testar um módulo do Terraform usando um arquivo do Docker. Nesta seção, você aprenderá a usar o gerador do Yeoman para criar e testar um módulo no Cloud Shell.

Usar o Cloud Shell em vez de usar um arquivo do Docker simplifica bastante o processo. Usar o Cloud Shell:

- Não é necessário instalar o Node.js
- Não é necessário instalar o Yeoman
- Não é necessário instalar o Terraform

Todos esses itens estão pré-instalados no Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Iniciar uma sessão do Cloud Shell

1. Inicie uma sessão do Azure Cloud Shell por meio do [portal do Azure](https:/portal.azure.com/), de [shell.azure.com](https://shell.azure.com) ou do [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **A página Boas-vindas ao Azure Cloud Shell** é aberta. Selecione **Bash (Linux)**. (Não há suporte para PowerShell.)

    ![Bem-vindo ao Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Neste exemplo, Bash (Linux) foi selecionado.

1. Se você ainda não configurou uma conta de armazenamento do Azure, a tela a seguir será exibida. Selecione **Criar armazenamento**.

    ![Você não tem nenhum armazenamento montado](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que você selecionou anteriormente e exibe informações para a unidade de nuvem que acabou de ser criada.

    ![Sua unidade de nuvem foi criada](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Preparar uma pasta para armazenar seu módulo do Terraform

1. Neste ponto, o Cloud Shell já terá configurado o GOPATH em suas variáveis de ambiente. Para ver o caminho, insira `go env`.

1. Crie a pasta $GOPATH, se ainda não existir uma: insira `mkdir ~/go`.

1. Crie uma pasta dentro da pasta $GOPATH: insira `mkdir ~/go/src`. Essa pasta será usada para armazenar e organizar pastas de projetos diferentes que você pode criar, como a pasta <nome-do-seu-módulo> que criaremos na próxima etapa.

1. Criar uma pasta para armazenar seu módulo do Terraform: insira `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >Neste exemplo, escolhemos `my-module-name` para o nome da pasta.

1. Navegue até a pasta do seu módulo: insira `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Criar e testar seu módulo do Terraform

1. Insira `yo az-terra-module` e siga as instruções no Assistente.

    >[!NOTE]
    >Quando perguntado se deseja criar os arquivos do Docker, insira `N`.

1. Insira `bundle install` para instalar as dependências.

    Aguarde a mensagem **Pacote concluído** e continue na próxima etapa.

1. Insira `rake build` para criar o módulo.

    ![Build do Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Insira `rake e2e` para executar o teste de ponta a ponta.

1. Após alguns instantes, a mensagem **APROVADO** será exibida.

    ![APROVADO](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Instale e use a extensão Visual Studio Code do Azure Terraform.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
