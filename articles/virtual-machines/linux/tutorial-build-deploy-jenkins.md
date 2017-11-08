---
title: CI/CD do Jenkins para VMs do Azure com o VSTS | Microsoft Docs
description: "Configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo Node.js usando Jenkins para VMs do Azure do Release Management no VSTS (Visual Studio Team Services) ou Microsoft TFS (Team Foundation Server)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Implantar seu aplicativo em VMs Linux usando Jenkins e VSTS

CI (integração contínua) e CD (implantação contínua) é um pipeline por meio do qual você pode compilar, lançar e implantar seu código. O VSTS (Visual Studio Team Services) fornece um conjunto completo de ferramentas de automação de CI/CD para implantação no Azure. Jenkins é uma ferramenta de terceiros popular baseada em servidor de CI/CD que também fornece a automação de CI/CD. Você pode usar os dois juntos para personalizar o fornecimento de seu aplicativo ou serviço de nuvem.

Neste tutorial, você usará o Jenkins para compilar um **aplicativo Web Node.js** e o VSTS ou TFS (Team Foundation Server) para implantá-lo em um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contém máquinas virtuais Linux.

Você vai:

> [!div class="checklist"]
> * Obter o aplicativo de exemplo
> * Configurar os plug-ins do Jenkins
> * Configurar um projeto Freestyle Jenkins para Node.js
> * Configurar o Jenkins para integração com o VSTS
> * Criar um ponto de extremidade de serviço do Jenkins
> * Criar um grupo de implantação para máquinas virtuais do Azure
> * Criar uma definição da versão do VSTS
> * Executar implantações disparadas por CI e manuais

## <a name="before-you-begin"></a>Antes de começar

* Você precisa ter acesso a um servidor Jenkins. Se você ainda não tiver criado um servidor Jenkins, consulte [Criar um mestre Jenkins na Máquina Virtual do Azure](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). 

* Entre na sua conta do VSTS (`https://{youraccount}.visualstudio.com`). 
  Você pode obter uma [conta do VSTS gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para saber mais, confira [Conectar ao VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  É necessária uma máquina virtual Linux para um destino de implantação.  Para mais informações, consulte [Criar e gerenciar VMs do Linux com a CLI do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para sua máquina virtual.  Para obter mais informações, consulte [Criar grupos de segurança de rede usando o Portal do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obter o aplicativo de exemplo

Você precisa de um aplicativo para implantação em um repositório Git.
Para este tutorial, recomendamos o uso [deste aplicativo de exemplo disponível no GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Este tutorial contém um exemplo de script usado para instalar um aplicativo e o Node.js.  Se quiser trabalhar com seu próprio repositório, você deverá configurar um exemplo semelhante.

1. Crie uma bifurcação deste aplicativo e anote o local (URL) para usar em etapas posteriores deste tutorial.  Para obter mais informações, consulte [Criar fork para um repositório](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> O aplicativo foi compilado usando [Yeoman](http://yeoman.io/learning/index.html); ele usa **Express**, **bower** e **grunt**; e tem alguns pacotes **npm** como dependências.
> O exemplo também contém um script que configura o Nginx e implanta o aplicativo. Ele é executado nas máquinas virtuais. Especificamente, o script instala o Node, o Nginx e o PM2; configura Nginx e PM2; depois, inicia o aplicativo Node.

## <a name="configure-jenkins-plugins"></a>Configurar os plug-ins do Jenkins

Primeiramente, você deve configurar dois plug-ins Jenkins para **NodeJS** e **Implantação Contínua do VS Team Services**.

1. Abra sua conta do Jenkins e escolha **Gerenciar Jenkins**.
2. Na página **Gerenciar Jenkins**, escolha **Gerenciar Plug-ins**.
3. Filtre a lista para localizar o plug-in **NodeJS** e escolha a opção **Instalar sem reinicialização**.
    ![Como adicionar o plug-in do NodeJS ao Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtre a lista para localizar o plug-in **Implantação Contínua do VS Team Services** e escolha a opção **Instalar sem reinicialização**.
5. Navegue de volta para o painel do Jenkins e escolha **Gerenciar Jenkins**.
6. Escolha **Configuração da Ferramenta Global**.  Localize **NodeJS** e clique em **Instalações do NodeJS**.
7. Habilite a opção **Instalar automaticamente** e, em seguida, insira um valor de **Nome**.
8. Clique em **Salvar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto Freestyle Jenkins para Node.js

1. Clique em **Novo Item**.  Insira um **nome do item**.
2. Escolha **Projeto Freestyle**.  Clique em **OK**.
3. Na guia **Gerenciamento de Código-Fonte**, selecione **Git** e insira os detalhes do repositório e do branch que contém o código do aplicativo.    
    ![Adicionar um repositório ao seu build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na guia **Compilar Gatilhos**, selecione **Sondar SCM** e insira o cronograma `H/03 * * * *` para sondar o repositório do Git em busca de alterações a cada três minutos. 
5. Na guia **Ambiente de Build**, selecione **Fornecer Nó &amp; CAMINHO do compartimento/pasta de npm** e selecione o valor da **Instalação do NodeJS**. Deixe o **arquivo npmrc** definido como "usar padrão do sistema".
6. Na guia **Build**, escolha **Executar shell** e insira o comando `npm install` para garantir a atualização de todas as dependências.


## <a name="configure-jenkins-for-vsts-integration"></a>Configurar o Jenkins para integração com o VSTS

  > [!NOTE]
  Certifique-se de que o PAT (token de acesso pessoal) usado nas etapas a seguir contenha a **permissão de versão (ler, gravar, executar e gerenciar) no VSTS**.
 
1.  Crie um PAT na sua conta do VSTS caso ainda não tenha um. O Jenkins exige essas informações para acessar sua conta do VSTS.  Certifique-se de **armazenar** as informações de token para futuras etapas nesta seção.
  Leia [Como criar um token de acesso pessoal para VSTS e TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para aprender a gerar um.
2. Na guia **Ações de pós-build**, clique em **Adicionar ação de pós-build**. Escolha **Arquivar os artefatos**.
3. Em **Arquivos para arquivar**, insira `**/*` para incluir todos os arquivos.
4. Para criar outra ação, clique em **Adicionar ação de pós-compilação**.
5. Escolha **Disparar versão no TFS/Team Services**, insira o URI para sua conta do VSTS, como:   `https://{your-account-name}.visualstudio.com`.
6. Insira o nome do **Projeto de equipe**.
7. Escolha um nome para a **definição da versão** (crie esta definição da versão posteriormente no VSTS).
8. Escolha as credenciais para se conectar ao seu ambiente do VSTS ou TFS.  Deixe o **Nome de usuário** em branco se você estiver usando o VSTS.
   Insira um **Nome de usuário e senha** se você estiver usando uma versão local do TFS.    
    ![Configuração de ações de pós-build do Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Salve** o projeto Jenkins.

## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto de extremidade de serviço do Jenkins

Um ponto de extremidade de serviço permite que o VSTS se conecte ao Jenkins.

1. Abra a página **Serviços** no VSTS, abra a lista **Novo Ponto de Extremidade de Serviço** e escolha **Jenkins**.
    ![Adicionar um ponto de extremidade do Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Insira um nome para a conexão.
3. Insira a URL de seu servidor Jenkins e marque a opção **Aceitar certificados SSL não confiáveis**.  Um exemplo de URL é: `http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Insira o **nome de usuário e a senha** para sua conta do Jenkins.
5. Escolha **Verificar conexão** para verificar se as informações estão corretas.
6. Escolha **OK** para criar o ponto de extremidade de serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implantação para Máquinas Virtuais do Azure

É necessário um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registrar o agente do VSTS para a definição da versão poder fazer a implantação em sua máquina virtual.  Os grupos de implantação facilitam a definição de grupos lógicos de computadores de destino para implantação e instalam o agente necessário em cada computador.

   > [!NOTE]
   > Nas etapas a seguir, certifique-se de instalar os pré-requisitos e **não execute o script com privilégios sudo.**

1. Abra a guia **Versões** do hub **Build &amp; Versão**, abra **Grupos de implantação** e escolha **+ Novo**.
2. Insira um nome para o grupo de implantação e uma descrição opcional.
   Depois, escolha **Criar**.
3. Escolha o sistema operacional para sua máquina virtual de destino de implantação.  Por exemplo, escolha **Ubuntu 16.04+**.
4. Selecione **Usar um token de acesso pessoal no script para autenticação**.
5. Selecione o link **Pré-requisitos do sistema**.  Instale os pré-requisitos para seu sistema operacional.
6. Selecione **Copiar script para área de transferência** para copiar o script.
7. **Faça logon** na máquina virtual de destino de implantação e **execute** o script.  **Não** execute o script com privilégios sudo.
8. Após a instalação, serão solicitadas marcações de grupo de implantação.  Aceite os padrões.
9. No VSTS, verifique sua máquina virtual recém-registrada em **Destinos** em **Grupos de implantação**.

## <a name="create-a-vsts-release-definition"></a>Criar uma definição da versão do VSTS

Uma definição de versão especifica o processo que o VSTS executará para implantar o aplicativo.  Neste exemplo, você deve executar um script de shell.

Para criar uma definição de versão no VSTS:

1. Abra **Versões** no hub **Build &amp; Versão** e escolha **Criar definição da versão**. 
2. Selecione o modelo **Vazio** escolhendo para iniciar com um **Processo vazio**.
3. Na seção **Artefatos**, clique em **+ Adicionar artefato** e escolha **Jenkins** para **Tipo de fonte**. Selecione sua conexão de ponto de extremidade de serviço do Jenkins. Depois, selecione o trabalho de origem do Jenkins e escolha **Adicionar**.
4.  Clique nas reticências ao lado de **Ambiente 1**.  Clique em **Adicionar fase de grupo de implantação**.
5.  Escolha seu **Grupo de implantação**.
5. Clique em **+** para adicionar uma tarefa à **Fase do grupo de implantação**.
6. Escolha a tarefa **Script do Shell** e clique em **Adicionar**.    
    A tarefa **Script do Shell** é usada para fornecer a configuração para execução de um script em cada servidor a fim de instalar o Node.js e iniciar o aplicativo. Configure-o da seguinte maneira:
8. **Caminho do script**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Clique em **Avançado** e, em seguida, habilite **Especificar diretório de trabalho**.
10.  **Diretório de Trabalho**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Edite o nome da definição de versão com base no nome especificado na guia **Ações de Pós-build** do build no Jenkins. O Jenkins exige esse nome para poder disparar uma nova versão quando os artefatos de origem forem atualizados.
12. Clique em **Salvar** e clique em **OK** para salvar a definição da versão.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar implantações disparadas por CI e manuais

1. Escolha **+ Versão** e selecione **Criar Versão**.
2. Selecione o build concluído na lista suspensa realçada e escolha **Fila**.
3. Escolha o link de versão na mensagem pop-up. Por exemplo: "A versão **Release-1** foi criada".
4. Abra a guia **Logs** para observar a saída do console de versão.
5. No navegador, abra a URL de um dos servidores adicionados ao seu grupo de implantação. Por exemplo, insira: `http://{your-server-ip-address}`
6. Acesse o repositório do Git de origem e modifique o conteúdo do título **h1** no arquivo app/views/index.jade com um texto alterado.
7. **Confirme** a alteração.
8. Depois de alguns minutos, você verá uma nova versão criada na página **Versões** do VSTS ou TFS. Abra a versão para ver a implantação acontecendo. Parabéns!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a implantação de um aplicativo no Azure usando o build do Jenkins e o VSTS para versão. Você aprendeu como:

> [!div class="checklist"]
> * Compilar seu aplicativo no Jenkins
> * Configurar o Jenkins para integração com o VSTS
> * Criar um grupo de implantação para máquinas virtuais do Azure
> * Criar uma definição de versão que configura as VMs e implanta o aplicativo

Avance para o próximo tutorial para saber mais sobre como implantar uma pilha LAMP (Linux, Apache, MySQL e PHP).

> [!div class="nextstepaction"]
> [Implantar a pilha LAMP](tutorial-lamp-stack.md)