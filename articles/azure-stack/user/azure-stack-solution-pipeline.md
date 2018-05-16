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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 2a51169fa74384778d3351d48055d05e387d0ea6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Tutorial: Implantar aplicativos do Azure e o Azure pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Um pipeline de entrega (CI/CD) integração contínua/contínua híbrido permite que você criar, testar e implantar seu aplicativo em várias nuvens.  Neste tutorial, você criará um ambiente de exemplo para:
 
> [!div class="checklist"]
> * Inicie uma nova compilação com base em confirmações de código ao seu repositório do Visual Studio Team Services (VSTS).
> * Implante automaticamente o seu código recém-criado para o Azure global para testes de aceitação do usuário.
> * Quando o seu código de teste, implante automaticamente com a pilha do Azure.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Sobre a entrega híbrida criar pipe

Continuidade de implantação de aplicativo, segurança e confiabilidade é essencial para sua organização e é fundamental para sua equipe de desenvolvimento. Com um pipeline de CI/CD híbrida, você pode consolidar seus pipelines em seu ambiente local e nuvem pública. Você pode alterar o local sem alternar a seu aplicativo.

Essa abordagem também permite que você mantenha um conjunto de ferramentas de desenvolvimento consistente. Ferramentas consistentes em nuvem pública do Azure e seu ambiente do Azure pilha local significa que é muito mais fácil para implementar as práticas de desenvolvimento de CI/CD. Aplicativos e serviços implantados no Azure ou Azure pilha são intercambiáveis, e o mesmo código pode ser executado em qualquer local, aproveitando os recursos e recursos de nuvem pública e local.

Saiba mais sobre:
 - [O que é integração contínua?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [O que é o fornecimento contínuo?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Pré-requisitos

Você precisará ter alguns componentes para criar um pipeline de CI/CD híbrida. Eles podem levar algum tempo para se preparar.
 
 - Um parceiro OEM/hardware do Azure pode implantar uma pilha do Azure de produção e todos os usuários podem implantar um Kit de desenvolvimento de pilha do Azure (ASDK). 
 - Um operador de pilha do Azure também deve implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016.

Se você já tiver alguns desses componentes, verifique se que eles atenderem aos requisitos antes de começar.

Este tópico também pressupõe que você tenha algum conhecimento do Azure e a pilha do Azure. Se você quiser saber antes de prosseguir, certifique-se de que comece por estes tópicos:


Este tutorial também pressupõe que você tenha algum conhecimento do Azure e a pilha do Azure. 

Se você quiser saber antes de prosseguir, você pode iniciar com estes tópicos:
 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Principais conceitos de pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

 - Criar um [aplicativo da Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) no Azure. Anote o novo URL do aplicativo Web, como ele é usado mais tarde.

Azure Stack
 - Usar um sistema de pilha do Azure integradas ou implantar o Kit de desenvolvimento da pilha (ASDK) da Azure vinculadas abaixo:
    - Você pode encontrar instruções detalhadas sobre como implantar o ASDK em "[Tutorial: implantar o ASDK usando o instalador](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - Você pode automatizar muitas das etapas de pós-implantação seu ASDK com o seguinte script do PowerShell, [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > A instalação ASDK leva um sete horas para ser concluída, por isso, planeje adequadamente.

 - Implantar [do serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) serviços de PaaS a pilha do Azure. 
 - Criar [ofertas plano](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro do ambiente de pilha do Azure. 
 - Criar [assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente de pilha do Azure. 
 - Crie um aplicativo da Web dentro da assinatura de locatário. Anote o novo URL do aplicativo Web para uso posterior.
 - Implante a máquina Virtual do VSTS, ainda dentro da assinatura de locatário.
 - Windows Server 2016 VM com o .NET 3.5 necessário. Essa VM será criada na pilha do Azure que o agente de compilação particular. 

### <a name="developer-tools"></a>Ferramentas de desenvolvedor

 - Criar um [espaço de trabalho do VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). O processo de inscrição cria um projeto chamado **MyFirstProject**.
 - [Instalar o Visual Studio de 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [entrar no VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Conecte-se ao projeto e [clonar localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > Você precisará do Azure pilha com imagens adequadas agregadas para ser executado (Windows Server e SQL) e ter o serviço de aplicativo implantado.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Preparar a compilação particular e o agente de liberação para integração com o Visual Studio Team Services

### <a name="prerequisites"></a>Pré-requisitos

Visual Studio Team Services (VSTS) autentica com o Azure Resource Manager usando uma entidade de serviço. Para o VSTS poder provisionar recursos em uma assinatura de pilha do Azure, ele requer o status do Colaborador.

A seguir está as etapas de alto nível que precisam ser configurados para permitir essa autenticação:

1. Entidade de serviço deve ser criada ou um existente pode ser usado.
2. Chaves de autenticação precisam ser criado para a entidade de serviço.
3. Assinatura de pilha do Azure precisa ser validada por meio do controle de acesso baseado em função para permitir que o SPN fazer parte da função de Colaborador.
4. Uma nova definição de serviço no VSTS devem ser criada usando os pontos de extremidade de pilha do Azure, bem como informações de SPN.

### <a name="service-principal-creation"></a>Criação de entidade de serviço

Consulte o [criação da entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruções para criar uma entidade de serviço e escolha o aplicativo/API da Web para o tipo de aplicativo.

### <a name="access-key-creation"></a>Criação de chaves de acesso

Uma entidade de serviço requer uma chave de autenticação, siga as etapas nesta seção para gerar uma chave.


1. Em **Registros do Aplicativo** no Azure Active Directory, selecione seu aplicativo.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Anote o valor de **ID do aplicativo**. Ao configurar o ponto de extremidade de serviço no VSTS, você usará esse valor.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Para gerar uma chave de autenticação, selecione **Configurações**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Para gerar uma chave de autenticação, selecione **Chaves**.
 
    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.
 
    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Após salvar a chave, o valor da chave é exibido. Copie este valor, pois não é possível recuperar a chave posteriormente. Você fornece o **valor de chave** com a ID do aplicativo para fazer logon como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Obter a ID do locatário

Como parte da configuração de ponto de extremidade do serviço, VSTS requer o **ID de locatário** que corresponde ao diretório AAD implantado seu carimbo de pilha do Azure. Siga as etapas nesta seção para obter a ID de locatário.

1. Selecione **Azure Active Directory**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Para obter a ID de locatário, selecione **Propriedades** do seu locatário do Azure AD.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Copie a **ID de diretório**. Esse valor é a ID do locatário.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Conceder os direitos de serviço principal para implantar recursos na assinatura do Azure pilha 

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**. Em vez disso, você pode selecionar um grupo de recursos ou um recurso.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Selecione o **assinatura** (grupo de recursos ou recurso) para atribuir o aplicativo.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Selecione **Controle de Acesso (IAM)**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Selecione **Adicionar**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Selecione a função que deseja atribuir ao aplicativo. A imagem a seguir mostra o **proprietário** função.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Por padrão, os aplicativos do Azure Active Directory não são exibidos nas opções disponíveis. Para localizar seu aplicativo, você deve **forneça o nome** no campo de pesquisa. Selecione-o.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

### <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

Controle de acesso do Azure baseado em função (RBAC) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode conceder apenas a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Para obter mais informações sobre o controle de acesso baseado em função, consulte [gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pools de agente do VSTS

Em vez de gerenciar cada agente individualmente, você pode organizar agentes em pools de agente. Um pool de agente define o limite de compartilhamento para todos os agentes nesse pool. No VSTS, pools de agente têm o escopo para a conta do VSTS; Portanto, você pode compartilhar um pool de agente em projetos de equipe. Para obter mais informações e um tutorial sobre como criar pools de agente do VSTS, consulte [criar Pools de agente e filas](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adicionar um token de acesso pessoal (PAT) para a pilha do Azure

1. Entre sua conta do VSTS e selecione o nome do perfil de conta.
2. Selecione **gerenciar segurança** a página de criação de token de acesso.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copie o token.
    
    > [!Note]  
    > Obter as informações do token. Ele não será exibido novamente após sair desta tela. 
    
    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalação do que agente de compilação do VSTS na pilha do Azure hospedado servidor de compilação

1.  Conecte-se ao seu servidor de compilação implantado no host de pilha do Azure.

2.  Baixar e implantar o agente de compilação como um serviço usando sua conta pessoal do token (PAT) e executar como a conta de administrador da VM.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Vá para a pasta do agente de compilação extraídos. Execute o **run.cmd** arquivo em um prompt de comando com privilégios elevados. 

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Após o run.cmd a pasta com o conteúdo extraído deve parecer com o seguinte:

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Agora você pode ver o agente na pasta do VSTS.

## <a name="endpoint-creation-permissions"></a>Permissões de criação de ponto de extremidade

Os usuários podem criar pontos de extremidade para que compilações do VSTO podem implantar aplicativos de serviço do Azure para a pilha. VSTS conecta-se para o agente de compilação, conecta-se com a pilha do Azure. 

![Texto ALT](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Sobre o **configurações** menu, selecione **segurança**.
2. No **VSTS grupos** na lista à esquerda, selecione **criadores de ponto de extremidade**. 

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Sobre o **membros** guia, selecione **+ adicionar**. 

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Digite um nome de usuário e selecione o usuário na lista.
5. Clique em **Salvar alterações**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. No **VSTS grupos** na lista à esquerda, selecione **administradores de ponto de extremidade**.
7. Sobre o **membros** guia, selecione **+ adicionar**.
8. Digite um nome de usuário e selecione o usuário na lista.
9. Clique em **Salvar Alterações**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    O agente de compilação na pilha do Azure recebe instruções do VSTS, que, em seguida, transmite informações de ponto de extremidade para comunicação com a pilha do Azure. 
    
    VSTS para conexão de pilha do Azure agora está pronto.

## <a name="develop-your-application"></a>Desenvolver seu aplicativo

Configure híbrido CI/CD para implantar o aplicativo Web para o Azure e a pilha do Azure e automática por push as alterações para as nuvens.

> [!Note]  
> Você precisará do Azure pilha com imagens adequadas agregadas para ser executado (Windows Server e SQL) e ter o serviço de aplicativo implantado. Leia a documentação do serviço de aplicativo seção "Pré-requisitos" para obter os requisitos de operador de pilha do Azure.

### <a name="add-code-to-vsts-project"></a>Adicionar código ao projeto VSTS

1. Entrar no Visual Studio com uma conta que tenha direitos de criação de projeto na pilha do Azure.

    Híbrido CI/CD pode aplicar o código do aplicativo e o código de infraestrutura. Use [modelos do Gerenciador de recursos do Azure como web ](https://azure.microsoft.com/resources/templates/) código de aplicativo do VSTS para as nuvens.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clone o repositório** criando e abrindo o aplicativo web padrão.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar a implantação do aplicativo da web autocontidos para serviços de aplicativos em ambas as nuvens

1. Editar o **WebApplication.csproj** arquivo: selecione **Runtimeidentifier** e adicione `win10-x64.` para obter mais informações, consulte [implantação autossuficiente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação .

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Verifique o código no VSTS usando o Team Explorer.

3. Confirme que o código do aplicativo foi verificado no Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Entrar no VSTS para confirmar a capacidade de criar definições de compilação.

2. Adicionar **win10-x64 - r** código. Isso é necessário para disparar uma implantação independente com o .net Core. 

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Execute a compilação. O [implantação autossuficiente compilação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará artefatos que podem ser executados no Azure e a pilha do Azure.

### <a name="using-an-azure-hosted-agent"></a>Usando um agente hospedado do Azure

Usando um agente hospedado no VSTS é uma opção conveniente para criar e implantar aplicativos web. Atualizações e manutenção são executadas automaticamente pelo Microsoft Azure, permitindo contínuo e ininterrupto de desenvolvimento, teste e implantação.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Gerenciar e configurar o processo de implantação contínua (CD)

O Visual Studio Team Services (VSTS) e o Team Foundation Server (TFS) fornecem um pipeline altamente configurável e gerenciável para versões em vários ambientes, como desenvolvimento, teste, QA e ambientes de produção; incluindo que requerem aprovações em estágios específicos.

### <a name="create-release-definition"></a>Criar definição de versão

![Texto ALT](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Selecione o  **\[ +]** para adicionar uma nova versão sob o **guia versões** na página de Build e versão do VSO.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Aplicar o **implantação de serviço de aplicativo do Azure** modelo.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\103.png)

3. Em Adicionar menu suspenso artefato **adicionar o artefato** para o aplicativo de compilação de nuvem do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Na guia de Pipeline, selecione o **fase**, **tarefa** link do ambiente e definir valores de ambiente de nuvem do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Definir o **nome do ambiente** e selecione Azure **assinatura** para o ponto de extremidade de nuvem do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\106.png)

6. Em nome do ambiente, defina necessários **nome do serviço de aplicativo do Azure**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Digite **VS2017 hospedado** em fila do agente para o ambiente de nuvem do Azure hospedado.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\108.png)

8. No menu de implantar o serviço de aplicativo do Azure, selecione o válido **pacote ou pasta** para o ambiente. Selecione **Okey** para **local da pasta**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Salvar todas as alterações e voltar a **pipeline da versão**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Adicionar um **novo artefato** selecionando a compilação para o aplicativo de pilha do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Adicionar um ambiente de mais aplicando o **implantação de serviço de aplicativo do Azure**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Nomeie o novo ambiente **Azure pilha**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Localizar o ambiente de pilha do Azure sob **tarefa** guia.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Selecione o **assinatura** para o ponto de extremidade de pilha do Azure.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Defina o nome do aplicativo web do Azure pilha como o **nome do serviço de aplicativo**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Selecione o **agente Azure pilha**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\118.png)

17. Sob o serviço de aplicativo do Azure implantar seção Selecione válidos **pacote ou pasta** para o ambiente. Selecione Okey para **local da pasta**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Na guia variável, adicione uma variável chamada **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, defina seu valor como **true**e o escopo para **Azure pilha**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Selecione o **contínuo** implantação disparar ícone em ambos os artefatos e habilitar o gatilho de implantação continua.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Selecione o **pré-implantação** ícone condições no azure ambiente de pilha e defina o gatilho para **após o lançamento**.

21. Salve todas as alterações.

> [!Note]  
> Algumas configurações para as tarefas podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) quando criou a definição de uma versão de um modelo. Essas configurações não não possível modificar as configurações da tarefa; em vez disso, você deve selecionar o item de ambiente pai para editar essas configurações.

## <a name="create-a-release"></a>Criar uma versão

Agora que você concluiu as modificações na definição de versão, é hora de começar a implantação. Para fazer isso, você deve criar uma versão da definição de versão. Uma versão pode ser criada automaticamente. Por exemplo, o gatilho de implantação contínua é definido na definição de versão. Isso significa que modificar o código-fonte iniciará uma nova compilação e de que uma nova versão. No entanto, nesta seção você criará uma nova versão manualmente.

1. Abra o **versão** suspensa lista e escolha **criar versão**.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Insira uma descrição para a versão, verifique se os artefatos corretos estão selecionados e, em seguida, escolha **criar**. Após alguns momentos, uma faixa é exibida indicando que a nova versão foi criada. Escolha o link (o nome da versão).

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. A página de resumo da versão abre mostrando os detalhes da versão. No **ambientes** seção, você verá o status da implantação para o ambiente de "Qualidade" alterar de "Em andamento" para "Êxito" e, nesse ponto, uma faixa é exibida indicando que a versão está aguardando aprovação. Quando uma implantação em um ambiente está pendente ou falhou, um azul (i) o ícone de informações é mostrado. Aponte para ver um pop-up que contém o motivo.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\202.png)

Outros modos de exibição, como a lista de versões, também exibir um ícone que indica a aprovação está pendente. O ícone mostra um pop-up que contém o nome do ambiente e obter mais detalhes quando você aponta para ela. Isso facilita que um administrador ver quais versões estão aguardando aprovação, bem como o progresso geral de todas as versões.

### <a name="monitor-and-track-deployments"></a>Monitorar e acompanhar implantações

Nesta seção, você verá como você pode monitorar e controlar as implantações - neste exemplo, dois sites de serviços de aplicativo do Azure - da versão que você criou na seção anterior.

1. Na página de resumo de lançamento, escolha o **Logs** link. Durante a implantação, essa página mostra o log em tempo real do agente e, no painel esquerdo, uma indicação do status de cada operação no processo de implantação para cada ambiente.

    Escolha o ícone no **ação** coluna para uma aprovação de pré-implantação ou pós-implantação ver os detalhes de quem aprovada (ou rejeitados) a implantação e a mensagem que fornecida pelo usuário.

2. Depois que a implantação for concluída, o arquivo de log inteiro é exibido no painel direito. Selecione qualquer uma da **processar etapas** no painel esquerdo para mostrar apenas o log de conteúdo do arquivo para essa etapa. Isso torna mais fácil rastrear e depurar partes individuais da implantação geral. Como alternativa, baixe os arquivos de log individuais ou um zip de todos os arquivos de log dos ícones e os links na página.

    ![Texto ALT](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Abra o **resumo** guia para ver os detalhes gerais da versão. Mostra detalhes sobre a compilação e os ambientes que ele foi implantado para - junto com o status da implantação e outras informações sobre a versão.

4. Selecione cada uma da **links de ambiente** para ver mais detalhes sobre existente e pendentes implantações em um ambiente específico. Você pode usar essas páginas para verificar se a mesma compilação foi implantada para ambos os ambientes.

5. Abra o **implantado o aplicativo de produção** no seu navegador. Por exemplo, para um site de serviços de aplicativo do Azure, da URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
