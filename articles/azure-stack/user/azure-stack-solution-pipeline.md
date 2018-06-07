---
title: Implantar seu aplicativo no Azure e o Azure pilha | Microsoft Docs
description: Saiba como implantar aplicativos do Azure e a pilha do Azure com um pipeline de CI/CD híbrida.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604345"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Tutorial: implantar aplicativos do Azure e a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Saiba como implantar um aplicativo do Azure e a pilha do Azure usando um pipeline de entrega (CI/CD) integração contínua/contínua híbrida.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> * Inicie uma nova compilação com base em confirmações de código ao seu repositório do Visual Studio Team Services (VSTS).
> * Implante automaticamente o seu aplicativo para o Azure global para testes de aceitação do usuário.
> * Quando seu código passa no teste, implante automaticamente o aplicativo com a pilha do Azure.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Benefícios da entrega híbrida criar pipe

Continuidade, segurança e confiabilidade são os principais elementos da implantação do aplicativo. Esses elementos são essenciais para sua organização e é fundamental para sua equipe de desenvolvimento. Um pipeline de CI/CD híbrido permite consolidar sua pipes de compilação em seu ambiente local e nuvem pública. Um modelo de entrega híbrido permite alterar os locais de implantação sem alterar seu aplicativo.

Outros benefícios de usar a abordagem híbrida são:

* Você pode manter um conjunto de ferramentas de desenvolvimento consistente em seu ambiente de pilha do Azure no local e nuvem pública do Azure.  Um conjunto comum de ferramentas torna mais fácil de implementar as práticas recomendadas e padrões de CI/CD.
* Aplicativos e serviços implantados no Azure ou Azure pilha são intercambiáveis, e o mesmo código pode ser executado em qualquer local. Você pode tirar proveito de recursos e recursos de nuvem pública e local.

Para saber mais sobre CI e CD:

* [O que é integração contínua?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [O que é o fornecimento contínuo?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter componentes para criar um pipeline de CI/CD híbrida. Os seguintes componentes levará tempo para preparar:

* Um parceiro OEM/hardware do Azure pode implantar uma pilha do Azure de produção. Todos os usuários podem implantar o Kit de desenvolvimento de pilha do Azure (ASDK).
* Também deve ter um operador de pilha do Azure: implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016.

>[!NOTE]
>Se você já tiver alguns desses componentes implantados, certifique-se de atender a todos os requisitos antes de iniciar este tutorial.

Este tutorial presume que você tenha conhecimentos básicos do Azure e a pilha do Azure. Para saber mais, antes de iniciar o tutorial, leia os seguintes artigos:

* [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Principais conceitos de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Requisitos do Azure

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Criar um [aplicativo da Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) no Azure. Observe da URL do aplicativo Web, você precisará usá-la no tutorial.

### <a name="azure-stack-requirements"></a>Requisitos de pilha do Azure

* Usar um sistema de pilha do Azure integradas ou implantar o Kit de desenvolvimento de pilha do Azure (ASDK). Para implantar o ASDK:
    * O [Tutorial: implantar o ASDK usando o instalador](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) fornece instruções detalhadas de implantação.
    * Use o [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) script do PowerShell para automatizar as etapas de pós-implantação ASDK.

    > [!Note]
    > A instalação ASDK leva aproximadamente sete horas para ser concluída, por isso, planeje adequadamente.

 * Implantar [do serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS a pilha do Azure.
 * Criar [ofertas plano](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) na pilha do Azure.
 * Criar um [assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) na pilha do Azure.
 * Crie um aplicativo Web na assinatura de locatário. Anote o novo URL do aplicativo Web para uso posterior.
 * Implante Máquina Virtual de VSTS na assinatura de locatário.
* Fornece uma imagem do Windows Server 2016 com o .NET 3.5 para uma máquina virtual (VM). Essa VM será criada na pilha do Azure como um agente de compilação particular.

### <a name="developer-tool-requirements"></a>Requisitos da ferramenta de desenvolvedor

* Criar um [espaço de trabalho do VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). O processo de inscrição cria um projeto chamado **MyFirstProject**.
* [Instalar o Visual Studio de 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [entrar no VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Conecte-se ao seu projeto e [clone-a localmente](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Seu ambiente de pilha do Azure precisa as imagens corretas agregadas para executar o Windows Server e SQL Server. Ela também deve ter o serviço de aplicativo implantado.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Preparar a compilação particular e o agente de liberação para integração com o Visual Studio Team Services

### <a name="prerequisites"></a>Pré-requisitos

Visual Studio Team Services (VSTS) autentica com o Azure Resource Manager usando uma entidade de serviço. VSTS deve ter o **Colaborador** função para provisionar recursos em uma assinatura de pilha do Azure.

As etapas a seguir descrevem o que é necessário para configurar a autenticação:

1. Crie uma entidade de serviço, ou use uma entidade de serviço existente.
2. Crie chaves de autenticação para a entidade de serviço.
3. Valide a assinatura de pilha do Azure por meio do controle de acesso baseado em função para permitir que o nome Principal de serviço (SPN) para fazer parte da função de Colaborador.
4. Crie uma nova definição de serviço no VSTS usando os pontos de extremidade de pilha do Azure e as informações de SPN.

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Consulte o [criação da entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruções para criar uma entidade de serviço e, em seguida, escolha **API da Web doaplicativo/** para o tipo de aplicativo.

### <a name="create-an-access-key"></a>Criar uma chave de acesso

Uma entidade de serviço requer uma chave de autenticação. Use as etapas a seguir para gerar uma chave.

1. Em **Registros do Aplicativo** no Azure Active Directory, selecione seu aplicativo.

    ![Selecione o aplicativo](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Anote o valor de **ID do aplicativo**. Ao configurar o ponto de extremidade de serviço no VSTS, você usará esse valor.

    ![ID do aplicativo](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Para gerar uma chave de autenticação, selecione **Configurações**.

    ![Editar configurações do aplicativo](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Para gerar uma chave de autenticação, selecione **Chaves**.

    ![Configurar as definições de chave](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Forneça uma descrição para a chave e definir a duração da chave. Ao terminar, escolha **Salvar**.

    ![Descrição da chave e duração](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Depois de salvar a chave, a chave **valor** é exibido. Copie esse valor, porque você não pode obter esse valor posteriormente. Você fornece o **valor de chave** com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

    ![VALOR de chave](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Obter a ID de locatário

Como parte da configuração de ponto de extremidade do serviço, VSTS requer o **ID de locatário** que corresponde ao diretório AAD implantado seu carimbo de pilha do Azure. Use as etapas a seguir para obter a ID de locatário.

1. Selecione **Azure Active Directory**.

    ![Active Directory do Azure para locatário](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Para obter a ID de locatário, selecione **Propriedades** do seu locatário do Azure AD.

    ![Exibir propriedades do inquilino](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Copie a **ID de diretório**. Esse valor é a ID do locatário.

    ![ID do Diretório](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Conceder os direitos de serviço principal para implantar recursos na assinatura do Azure pilha

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões recomendadas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adição de um aplicativo para a função de leitor de um grupo de recursos significa que ela pode ler o grupo de recursos e qualquer de seus recursos.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

    ![Selecione as assinaturas](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Em **assinatura**, selecione Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. No Visual Studio Enterprise, selecione **controle de acesso (IAM)**.

    ![Controle de acesso (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Selecione **Adicionar**.

    ![Adicionar](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Em **adicionar permissões**, selecione a função que você deseja atribuir ao aplicativo. Neste exemplo, o **proprietário** função.

    ![Função de proprietário](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Por padrão, os aplicativos do Azure Active Directory não são exibidos nas opções disponíveis. Para localizar seu aplicativo, você deve fornecer seu nome no **selecione** campo para procurá-lo. Selecione o aplicativo.

    ![Resultados de pesquisa do aplicativo](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

### <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

Controle de acesso do Azure baseado em função (RBAC) fornece gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode controlar o nível de acesso que os usuários precisam para realizar seus trabalhos. Para obter mais informações sobre o controle de acesso baseado em função, consulte [gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pools de agente do VSTS

Em vez de gerenciar cada agente separadamente, você pode organizar os agentes em pools de agente. Um pool de agente define o limite de compartilhamento para todos os agentes nesse pool. No VSTS, pools de agente limitam-se a conta do VSTS, o que significa que você pode compartilhar um pool de agente em projetos de equipe. Para saber mais sobre como criar pools de agente, consulte [criar Pools de agente e filas](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adicionar um Token de acesso pessoal (PAT) para a pilha do Azure

Crie um Token de acesso pessoal para acessar VSTS.

1. Entre sua conta do VSTS e selecione o nome do perfil de conta.
2. Selecione **gerenciar segurança** a página de criação de token de acesso.

    ![Entrada do usuário](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Selecione o projeto de equipe](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Adicionar o token de acesso pessoal](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Criar um token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copie o token.

    > [!Note]
    > Salve as informações do token. Essas informações não são armazenadas e não aparecerá novamente quando você deixar a página da web.

    ![Token de acesso pessoal](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalação do que agente de compilação do VSTS na pilha do Azure hospedado servidor de compilação

1. Conecte-se ao seu servidor de compilação implantado no host de pilha do Azure.
2. Baixar e implantar o agente de compilação como um serviço usando sua conta pessoal do token (PAT) e executar como a conta de administrador da VM.

    ![Baixar o agente de compilação](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Navegue até a pasta para o agente de compilação extraídos. Execute o **run.cmd** arquivo em um prompt de comando com privilégios elevados.

    ![Agente de compilação extraídos](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registrar o agente de compilação](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Quando o run.cmd for concluído, a pasta de agente de compilação é atualizada com arquivos adicionais. A pasta com o conteúdo extraído deve parecer com o seguinte:

    ![Atualização de pasta do agente de compilação](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Você pode ver o agente na pasta do VSTS.

## <a name="endpoint-creation-permissions"></a>Permissões de criação de ponto de extremidade

Criando pontos de extremidade, uma compilação do Visual Studio Online (VSTO) pode implantar aplicativos de serviço do Azure para a pilha do Azure. VSTS conecta-se para o agente de compilação, que se conecta a pilha do Azure.

![Aplicativo de exemplo NorthwindCloud no VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Entrar no VSTO e navegue até a página de configurações do aplicativo.
2. Em **configurações**, selecione **segurança**.
3. Em **VSTS grupos**, selecione **criadores de ponto de extremidade**.

    ![Criadores de ponto de extremidade NorthwindCloud](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Sobre o **membros** guia, selecione **adicionar**.

    ![Adicionar um membro](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. Em **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.
6. Selecione **Salvar alterações**.
7. No **VSTS grupos** lista, selecione **administradores de ponto de extremidade**.

    ![Administradores de ponto de extremidade NorthwindCloud](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Sobre o **membros** guia, selecione **adicionar**.
9. Em **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.
10. Selecione **Salvar alterações**.

Agora que as informações de ponto de extremidade existem, o VSTS para conexão de pilha do Azure está pronto para uso. O agente de compilação na pilha do Azure obtém as instruções do VSTS e, em seguida, o agente transmite informações de ponto de extremidade para comunicação com a pilha do Azure.

![Agente de compilação](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Desenvolver seu build do aplicativo

Nesta parte do tutorial, você vai:

* Adicione código a um projeto do VSTS.
* Crie a implantação do aplicativo da web independentes.
* Configurar o processo de implantação contínua

> [!Note]
 > Seu ambiente de pilha do Azure precisa as imagens corretas agregadas para executar o Windows Server e SQL Server. Ela também deve ter o serviço de aplicativo implantado. Leia a documentação do serviço de aplicativo seção "Pré-requisitos" para obter os requisitos de operador de pilha do Azure.

Híbrido CI/CD pode aplicar o código do aplicativo e o código de infraestrutura. Use [modelos do Gerenciador de recursos do Azure como web ](https://azure.microsoft.com/resources/templates/) VSTS para implantar ambas as nuvens de código do aplicativo.

### <a name="add-code-to-a-vsts-project"></a>Adicionar código a um projeto do VSTS

1. Entrar no VSTS com uma conta que tenha direitos de criação de projeto na pilha do Azure. A próxima captura de tela mostra como conectar-se ao projeto HybridCICD.

    ![Conectar a um projeto](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clone o repositório** criando e abrindo o aplicativo web padrão.

    ![Repositório de clone](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar a implantação do aplicativo da web autocontidos para serviços de aplicativos em ambas as nuvens

1. Editar o **WebApplication.csproj** arquivo: selecione **Runtimeidentifier** e, em seguida, adicione `win10-x64.` para obter mais informações, consulte [implantação autossuficiente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.

    ![Configurar Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Use o Team Explorer para incluir o código no VSTS.

3. Confirme se o código do aplicativo foi marcado no Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Entrar no VSTS com uma conta que pode criar uma definição de compilação.
2. Navegue até o **criar aplicativo de Web** página para o projeto.

3. Em **argumentos**, adicionar **win10-x64 - r** código. Isso é necessário para disparar uma implantação independente com o .net Core.

    ![Adicionar definição de compilação do argumento](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Execute a compilação. O [implantação autossuficiente compilação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará artefatos que podem ser executados no Azure e a pilha do Azure.

### <a name="use-an-azure-hosted-build-agent"></a>Uso do Azure hospedado agente de compilação

Usando um agente de compilação hospedado no VSTS é uma opção conveniente para criar e implantar aplicativos web. Atualizações e manutenção do agente são executadas automaticamente pelo Microsoft Azure, que permite que um ciclo de desenvolvimento contínuo e ininterrupta.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurar o processo de implantação contínua (CD)

O Visual Studio Team Services (VSTS) e o Team Foundation Server (TFS) fornecem um pipeline altamente configurável e gerenciável para versões em vários ambientes, como desenvolvimento, preparação, qualidade (QA) e produção. Esse processo pode incluir a necessidade de aprovações em estágios específicos do ciclo de vida do aplicativo.

### <a name="create-release-definition"></a>Criar definição de versão

Criando uma definição de versão é o processo de compilação a etapa final do seu aplicativo. Essa definição de versão é usada para criar uma versão e implantar uma compilação.

1. Entrar no VSTS e navegue até **Build e versão** para seu projeto.
2. Sobre o **versões** guia, selecione  **\[ +]** e, em seguida, selecione **criar definição de versão**.

   ![Criar definição de versão](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Em **selecionar um modelo de**, escolha **implantação de serviço de aplicativo do Azure**e, em seguida, selecione **aplicar**.

    ![Aplicar modelo](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Em **adicionar artefato**, do **fonte (definição de compilação)** menu suspenso, selecione o aplicativo de compilação de nuvem do Azure.

    ![Adicionar artefato](media\azure-stack-solution-hybrid-pipeline\103.png)

5. No **Pipeline** guia, selecione o **1 fase**, **1 tarefa** vincular a **exibir tarefas do ambiente**.

    ![Exibir tarefas de pipeline](media\azure-stack-solution-hybrid-pipeline\104.png)

6. No **tarefas** , insira o Azure como o **nome do ambiente** e selecione AzureCloud Traders Web EP do **assinatura do Azure** lista suspensa.

    ![Configurar variáveis de ambiente](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Insira o **nome do serviço de aplicativo do Azure**, que é "northwindtraders" na próxima captura de tela.

    ![Nome do serviço de aplicativo](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Para a fase de agente, selecione **hospedado VS2017** do **fila do agente** lista suspensa.

    ![Agente hospedado](media\azure-stack-solution-hybrid-pipeline\107.png)

9. Em **implantar o serviço de aplicativo do Azure**, selecione o válido **pacote ou pasta** para o ambiente.

    ![Selecione o pacote ou pasta](media\azure-stack-solution-hybrid-pipeline\108.png)

10. Em **Selecionar arquivo ou pasta**, selecione **Okey** para **local**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Salvar todas as alterações e voltar a **Pipeline**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\110.png)

12. No **Pipeline** guia, selecione **adicionar artefato**e escolha o **NorthwindCloud Traders-recipiente** do **fonte (definição de compilação)** lista suspensa.

    ![Adicionar novo artefato](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Em **selecionar um modelo de**, adicione outro ambiente. Escolher **implantação de serviço de aplicativo do Azure** e, em seguida, selecione **aplicar**.

    ![Selecionar modelo](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Digite "Azure pilha" como o **nome do ambiente**.

    ![Nome do ambiente](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Sobre o **tarefas** , localize e selecione a pilha do Azure.

    ![Ambiente de pilha do Azure](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Do **assinatura do Azure** lista suspensa, selecione "AzureStack Traders recipiente EP" para o ponto de extremidade de pilha do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Insira o nome do aplicativo web do Azure pilha como o **nome do serviço de aplicativo**.

    ![Nome do serviço de aplicativo](media\azure-stack-solution-hybrid-pipeline\116.png)

18. Em **seleção agente**, escolher "AzureStack - bDouglas Fir" o **fila do agente** lista suspensa.

    ![Selecione o agente](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Para **implantar o serviço de aplicativo do Azure**, selecione o válido **pacote ou pasta** para o ambiente. Em **Selecionar arquivo ou pasta**, selecione **Okey** para a pasta **local**.

    ![Selecione o pacote ou pasta](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Aprovar local](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Sobre o **variável** guia, localize a variável chamada **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Defina o valor da variável como **true**e definir o escopo para **Azure pilha**.

    ![Configurar a variável](media\azure-stack-solution-hybrid-pipeline\120.png)

21. No **Pipeline** guia, selecione o **gatilho de implantação contínua** ícone para o artefato NorthwindCloud Traders-Web e defina o **gatilho de implantação contínua** para **Habilitado**.  Fazer o mesmo para o artefato "NorthwindCloud Traders-recipiente".

    ![Gatilho de implantação contínua de conjunto](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Para o ambiente de pilha do Azure, selecione o **condições de pré-implantação** ícone definir o gatilho para **após o lançamento**.

    ![Conjunto de condições de pré-implantação gatilho](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Salve todas as alterações.

> [!Note]
> Algumas configurações para tarefas de versão podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) quando criou a definição de uma versão de um modelo. Essas configurações não podem ser modificadas nas configurações de tarefas. No entanto, você pode editar essas configurações nos itens do ambiente do pai.

## <a name="create-a-release"></a>Criar uma versão

Agora que você concluiu as modificações na definição de versão, é hora de começar a implantação. Para fazer isso, você deve criar uma versão da definição de versão. Uma versão pode ser criada automaticamente. Por exemplo, o gatilho de implantação contínua é definido na definição de versão. Isso significa que modificar o código-fonte iniciará uma nova compilação e de que uma nova versão. No entanto, nesta seção você criará uma nova versão manualmente.

1. No **Pipeline** guia, abra o **versão** suspensa lista e escolha **criar versão**.

    ![Criar uma versão](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Insira uma descrição para a versão, verifique se os artefatos corretos estão selecionados e, em seguida, escolha **criar**. Após alguns momentos, uma faixa é exibida indicando que a nova versão foi criada e o nome de versão é exibido como um link. Escolha o link para ver a página de resumo da versão.

    ![Faixa de criação de versão](media\azure-stack-solution-hybrid-pipeline\201.png)

3. A página de resumo versão mostra detalhes sobre a versão. Na seguinte captura de tela de "Versão 2", o **ambientes** seção mostra o **status da implantação** para o Azure como "Em andamento" e o status de pilha do Azure é "êxito". Quando o status da implantação para o ambiente do Azure é alterado para "Êxito", uma faixa será exibida, indicando que a versão está pronta para aprovação. Quando uma implantação está pendente ou falhou, um azul **(i)** ícone de informações é mostrado. Passe o mouse sobre o ícone para ver um pop-up que contém o motivo de atraso ou falha.

    ![Página de resumo de versão](media\azure-stack-solution-hybrid-pipeline\202.png)

Outros modos de exibição, como a lista de versões, também será exibido um ícone que indica a aprovação está pendente. O pop-up para esse ícone mostra o nome do ambiente e obter mais detalhes relacionados à implantação. É fácil para um administrador, consulte o progresso geral de versões e ver quais versões estão aguardando aprovação.

### <a name="monitor-and-track-deployments"></a>Monitorar e acompanhar implantações

Esta seção mostra como você pode monitorar e controlar todas as suas implantações. A versão para a implantação de dois sites de serviços de aplicativo do Azure fornece um bom exemplo.

1. Na página de resumo de "Versão 2", selecione **Logs**. Durante a implantação, essa página mostra o log em tempo real do agente. Painel da esquerda mostra o status de cada operação na implantação para cada ambiente.

    Você pode escolher um ícone de pessoa no **ação** coluna para uma aprovação de pré-implantação ou pós-implantação que aprovou (ou rejeitados) a implantação, e a mensagem fornecidas por elas.

2. Após a conclusão da implantação, todo o arquivo de log é exibido no painel direito. Você pode selecionar qualquer **etapa** no painel esquerdo para ver o arquivo de log para uma única etapa, como "Inicializar o trabalho". A capacidade de ver logs individuais torna mais fácil rastrear e depurar partes da implantação geral. Você também pode **salvar** o arquivo de log para uma etapa ou **baixar todos os logs como zip**.

    ![Logs de versão](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Abra o **resumo** guia para obter informações gerais sobre a versão. Essa exibição mostra detalhes sobre a compilação, os ambientes que ele foi implantado, status da implantação e outras informações sobre a versão.

4. Selecione um link de ambiente (**Azure** ou **Azure pilha**) para ver informações sobre existente e pendentes implantações em um ambiente específico. Você pode usar esses modos de exibição como uma maneira rápida de verificar se a mesma compilação foi implantada para ambos os ambientes.

5. Abra o **implantado o aplicativo de produção** em seu navegador. Por exemplo, para o site de serviços de aplicativo do Azure, abra a URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
