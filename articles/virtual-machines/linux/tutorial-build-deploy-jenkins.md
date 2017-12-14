---
title: CI/CD do Jenkins para VMs do Azure com o Team Services | Microsoft Docs
description: "Configurar a CI (integração contínua) e a CD (implantação contínua) de um aplicativo Node.js usando o Jenkins para VMs do Azure por meio do Release Management no Visual Studio Team Services ou Microsoft Team Foundation Server"
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
ms.openlocfilehash: bfda0475b58556db1236c8b051c59393384720f7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-your-app-to-linux-vms-by-using-jenkins-and-team-services"></a>Implantar seu aplicativo em VMs Linux usando o Jenkins e o Team Services

A CI (integração contínua) e a CD (implantação contínua) forma um pipeline por meio do qual você pode compilar, liberar e implantar seu código. O Visual Studio Team Services fornece um conjunto completo de ferramentas de automação de CI/CD para implantação no Azure. O Jenkins é uma ferramenta de terceiros popular baseada em servidor de CI/CD que também fornece a automação de CI/CD. Use o Team Services e o Jenkins juntos para personalizar a maneira como você fornece seu aplicativo ou serviço de nuvem.

Neste tutorial, você usa o Jenkins para criar um aplicativo Web Node.js. Em seguida, você usa o Team Services ou o Team Foundation Server para implantá-lo em um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contém VMs (máquinas virtuais) Linux.

Você vai:

> [!div class="checklist"]
> * Obter o aplicativo de exemplo.
> * Configure os plug-ins do Jenkins.
> * Configure um projeto Freestyle do Jenkins para Node.js.
> * Configure o Jenkins para integração com o Team Services.
> * Crie um ponto de extremidade de serviço do Jenkins.
> * Crie um grupo de implantação para as máquinas virtuais do Azure.
> * Crie uma definição da versão do Team Services.
> * Execute implantações manuais e disparadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Você precisa ter acesso a um servidor Jenkins. Se você ainda não tiver criado um servidor Jenkins, consulte [Criar um mestre Jenkins em uma máquina virtual do Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Entre em sua conta do Team Services (**https://{youraccount}.visualstudio.com**). 
  Você pode obter uma [conta gratuita do Team Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para saber mais, confira [Conectar-se ao Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  É necessária uma máquina virtual Linux para um destino de implantação.  Para obter mais informações, consulte [Criar e gerenciar VMs Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para sua máquina virtual. Para obter mais informações, consulte [Criar grupos de segurança de rede usando o Portal do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obter o aplicativo de exemplo

Você precisa de um aplicativo para implantação, armazenado em um repositório Git.
Para este tutorial, recomendamos o uso [deste aplicativo de exemplo disponível no GitHub](https://github.com/azooinmyluggage/fabrikam-node). Este tutorial contém um script de exemplo usado para instalar o Node.js e um aplicativo. Se quiser trabalhar com seu próprio repositório, você deverá configurar um exemplo semelhante.

Crie uma bifurcação deste aplicativo e anote o local (URL) para usar em etapas posteriores deste tutorial. Para obter mais informações, consulte [Criar fork para um repositório](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> O aplicativo foi criado por meio do [Yeoman](http://yeoman.io/learning/index.html). Ele usa o Express, Bower e o Grunt. Além disso, ele tem alguns pacotes npm como dependências.
> O exemplo também contém um script que configura o Nginx e implanta o aplicativo. Ele é executado nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, Nginx e PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia o aplicativo Node.

## <a name="configure-jenkins-plug-ins"></a>Configurar os plug-ins do Jenkins

Primeiro, você deve configurar dois plug-ins do Jenkins: **NodeJS** e **Implantação Contínua do VS Team Services**.

1. Abra sua conta do Jenkins e selecione **Gerenciar o Jenkins**.
2. Na página **Gerenciar o Jenkins**, selecione **Gerenciar Plug-ins**.
3. Filtre a lista para localizar o plug-in **NodeJS** e marque a opção **Instalar sem reinicialização**.
    ![Como adicionar o plug-in do NodeJS ao Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtre a lista para localizar o plug-in **Implantação Contínua do VS Team Services** e marque a opção **Instalar sem reinicialização**.
5. Volte para o painel do Jenkins e selecione **Gerenciar o Jenkins**.
6. Selecione **Configuração da Ferramenta Global**. Localize **NodeJS** e selecione **Instalações do NodeJS**.
7. Marque a opção **Instalar automaticamente** e, em seguida, insira um valor de **Nome**.
8. Selecione **Salvar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto Freestyle Jenkins para Node.js

1. Selecione **Novo Item**. Insira um nome de item.
2. Selecione **Projeto Freestyle**. Selecione **OK**.
3. Na guia **Gerenciamento de Código-Fonte**, selecione **Git** e insira os detalhes do repositório e do branch que contém o código do aplicativo.    
    ![Adicionar um repositório ao seu build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na guia **Criar Gatilhos**, selecione **Sondar SCM** e insira o agendamento `H/03 * * * *` para sondar o repositório Git em busca de alterações a cada três minutos. 
5. Na guia **Ambiente de Build**, selecione **Fornecer Nó &amp; CAMINHO do compartimento/pasta de npm** e selecione o valor da **Instalação do NodeJS**. Deixe o **arquivo npmrc** definido como **usar padrão do sistema**.
6. Na guia **Build**, selecione **Executar shell** e insira o comando `npm install` para garantir que todas as dependências são atualizadas.


## <a name="configure-jenkins-for-team-services-integration"></a>Configurar o Jenkins para integração com o Team Services

> [!NOTE]
> Garanta que o PAT (token de acesso pessoal) usado nas etapas a seguir contém a permissão *Versão* (ler, gravar, executar e gerenciar) no Team Services.
 
1.  Crie um PAT em sua conta do Team Services caso ainda não tenha um. O Jenkins exige essas informações para acessar sua conta do Team Services. Armazene as informações de token para as próximas etapas desta seção.
  
    Para saber como gerar um token, leia [Como fazer para criar um token de acesso pessoal para o VSTS e TFS?](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. Na guia **Ações de Pós-build**, selecione **Adicionar ação de pós-build**. Selecione **Arquivar os artefatos**.
3. Em **Arquivos para arquivar**, insira `**/*` para incluir todos os arquivos.
4. Para criar outra ação, selecione **Adicionar ação de pós-build**.
5. Selecione **Disparar versão no TFS/Team Services**. Insira o URI de sua conta do Team Services, como **https://{your-account-name}.visualstudio.com**.
6. Insira o nome do **Projeto de equipe**.
7. Escolha um nome para a definição da versão. (Você cria essa definição da versão posteriormente no Team Services.)
8. Escolha as credenciais para se conectar ao ambiente do Team Services ou do Team Foundation Server:
   - Deixe **Nome de usuário** em branco se estiver usando o Team Services. 
   - Insira um nome de usuário e uma senha caso esteja usando uma versão local do Team Foundation Server.    
   ![Configurando as ações de pós-build do Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Salve o projeto do Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto de extremidade de serviço do Jenkins

Um ponto de extremidade de serviço permite que o Team Services se conecte ao Jenkins.

1. Abra a página **Serviços** no Team Services, abra a lista **Novo Ponto de Extremidade de Serviço** e selecione **Jenkins**.
   ![Adicionar um ponto de extremidade do Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Insira um nome para a conexão.
3. Insira a URL de seu servidor Jenkins e marque a opção **Aceitar certificados SSL não confiáveis**. Uma URL de exemplo é **http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com**.
4. Insira o nome de usuário e a senha de sua conta do Jenkins.
5. Selecione **Verificar conexão** para verificar se as informações estão corretas.
6. Selecione **OK** para criar o ponto de extremidade de serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implantação para máquinas virtuais do Azure

É necessário ter um [grupo de implantação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registrar o agente do Team Services, de modo que a definição da versão possa ser implantada na máquina virtual. Os grupos de implantação facilitam a definição de grupos lógicos de computadores de destino para implantação e a instalação do agente necessário em cada computador.

   > [!NOTE]
   > No procedimento a seguir, instale os pré-requisitos e *não execute o script com privilégios sudo.*

1. Abra a guia **Versões** do hub **Build &amp; Versão**, abra **Grupos de implantação** e selecione **+ Novo**.
2. Insira um nome para o grupo de implantação e uma descrição opcional. Em seguida, selecione **Criar**.
3. Escolha o sistema operacional para sua máquina virtual de destino de implantação. Por exemplo, selecione **Ubuntu 16.04+**.
4. Selecione **Usar um token de acesso pessoal no script para autenticação**.
5. Selecione o link **Pré-requisitos do sistema**. Instale os pré-requisitos para seu sistema operacional.
6. Selecione **Copiar script para área de transferência** para copiar o script.
7. Faça logon na máquina virtual de destino da implantação e execute o script. Não execute o script com privilégios sudo.
8. Após a instalação, serão solicitadas marcações de grupo de implantação. Aceite os padrões.
9. No Team Services, verifique sua máquina virtual recém-registrada em **Destinos** em **Grupos de implantação**.

## <a name="create-a-team-services-release-definition"></a>Criar uma definição da versão do Team Services

Uma definição da versão especifica o processo que o Team Services usa para implantar o aplicativo. Neste exemplo, você deve executar um script de shell.

Para criar uma definição de versão no Team Services:

1. Abra a guia **Versões** do hub **Build &amp; Versão** e selecione **Criar definição da versão**. 
2. Selecione o modelo **Vazio** escolhendo começar com um **Processo vazio**.
3. Na seção **Artefatos**, clique em **+ Adicionar Artefato** e escolha **Jenkins** em **Tipo de fonte**. Selecione sua conexão de ponto de extremidade de serviço do Jenkins. Depois, selecione o trabalho de origem do Jenkins e selecione **Adicionar**.
4. Selecione as reticências ao lado de **Ambiente 1**. Selecione **Adicionar fase do grupo de implantação**.
5. Escolha seu grupo de implantação.
5. Selecione **+** para adicionar uma tarefa à **Fase do grupo de implantação**.
6. Selecione a tarefa **Script do Shell** e **Adicionar**. A tarefa **Script do Shell** fornece a configuração para execução de um script em cada servidor, a fim de instalar o Node.js e iniciar o aplicativo.
8. Para **Caminho do Script**, insira **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selecione **Avançado** e, em seguida, habilite a opção **Especificar Diretório de Trabalho**.
10. Para **Diretório de Trabalho**, insira **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Edite o nome da definição da versão com base no nome especificado na guia **Ações de Pós-build** do build no Jenkins. O Jenkins exige esse nome para poder disparar uma nova versão quando os artefatos de origem forem atualizados.
12. Selecione **Salvar** e **OK** para salvar a definição da versão.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar implantações manuais e disparadas por CI

1. Selecione **+ Versão** e **Criar Versão**.
2. Selecione o build concluído na lista suspensa realçada e selecione **Fila**.
3. Escolha o link de versão na mensagem pop-up. Por exemplo: "A versão **Release-1** foi criada".
4. Abra a guia **Logs** para observar a saída do console de versão.
5. No navegador, abra a URL de um dos servidores adicionados ao grupo de implantação. Por exemplo, insira **http://{your-server-ip-address}**.
6. Acesse o repositório do Git de origem e modifique o conteúdo do título **h1** no arquivo app/views/index.jade com um texto alterado.
7. Confirme a alteração.
8. Depois de alguns minutos, você verá uma nova versão criada na página **Versões** do Team Services ou do Team Foundation Server. Abra a versão para ver a implantação acontecendo. Parabéns!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a implantação de um aplicativo no Azure usando o Jenkins para o build e o Team Services para a versão. Você aprendeu como:

> [!div class="checklist"]
> * Crie seu aplicativo no Jenkins.
> * Configure o Jenkins para integração com o Team Services.
> * Crie um grupo de implantação para as máquinas virtuais do Azure.
> * Crie uma definição da versão que configura as VMs e implanta o aplicativo.

Para saber mais sobre como implantar uma pilha LAMP (Linux, Apache, MySQL e PHP), avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implantar a pilha LAMP](tutorial-lamp-stack.md)
