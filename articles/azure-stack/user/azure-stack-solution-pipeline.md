---
title: Implantar seu aplicativo no Azure e o Azure Stack | Microsoft Docs
description: Saiba como implantar aplicativos no Azure e o Azure Stack com um pipeline de CI/CD híbrido.
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
ms.date: 11/07/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 63d22aaa402c77b55591a15745b258cdd6f0ef0a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244344"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Tutorial: Implantar aplicativos no Azure Stack e no Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como implantar um aplicativo do Azure e Azure Stack usando um pipeline de entrega (CI/CD) híbrida/integração contínua.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> * Inicie uma nova compilação com base nas confirmações de código ao seu repositório de serviços de DevOps do Azure.
> * Implante automaticamente seu aplicativo no Azure global para o teste de aceitação do usuário.
> * Quando seu código passa no teste, implante automaticamente o aplicativo no Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Benefícios da entrega híbrida criar pipe

Continuidade, segurança e confiabilidade são elementos importantes da implantação do aplicativo. Esses elementos são essenciais para sua organização e é fundamental para sua equipe de desenvolvimento. Um pipeline de CI/CD híbrido permite que você consolide seu build pipes em seu ambiente local e a nuvem pública. Um modelo de entrega híbrida também permite alterar os locais de implantação sem alterar seu aplicativo.

Outros benefícios de usar a abordagem híbrida são:

* Você pode manter um conjunto consistente de ferramentas de desenvolvimento em seu ambiente do Azure Stack local e nuvem pública do Azure.  Um conjunto de ferramentas comuns torna mais fácil de implementar práticas e padrões de CI/CD.
* Aplicativos e serviços implantados no Azure ou Azure Stack são intercambiáveis e o mesmo código pode ser executado em qualquer local. Você pode tirar proveito de locais e recursos de nuvem pública e recursos.

Para saber mais sobre CI e CD:

* [O que é integração contínua?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [O que é a entrega contínua?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar híbrido aplicativos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter componentes para criar um pipeline de CI/CD híbrido. Os seguintes componentes levará tempo para se preparar:

* Um parceiro OEM/hardware do Azure pode implantar uma pilha do Azure de produção. Todos os usuários podem implantar o Azure Stack desenvolvimento ASDK (Kit).
* Também deve ter um operador do Azure Stack: implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016.

>[!NOTE]
>Se você já tiver alguns desses componentes implantados, certifique-se de atender a todos os requisitos antes de iniciar este tutorial.

Este tutorial pressupõe que você tenha algum conhecimento básico do Azure e o Azure Stack. Para saber mais antes de iniciar o tutorial, leia os artigos a seguir:

* [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Principais conceitos do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Requisitos do Azure

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Criar uma [aplicativo Web](https://docs.microsoft.com/azure/app-service/overview) no Azure. Observe da URL do aplicativo Web, você precisará usá-lo no tutorial.

### <a name="azure-stack-requirements"></a>Requisitos de pilha do Azure

* Usar um sistema integrado do Azure Stack ou implantar o Azure Stack desenvolvimento ASDK (Kit). Para implantar o ASDK:
    * O [Tutorial: implantar o ASDK usando o instalador](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) fornece instruções detalhadas de implantação.
    * Use o [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) script do PowerShell para automatizar as etapas de pós-implantação ASDK.

    > [!Note]
    > A instalação ASDK leva cerca de sete horas para concluir, portanto, planeje adequadamente.

 * Implante [serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) os serviços de PaaS para o Azure Stack.
 * Crie [plano/ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) no Azure Stack.
 * Criar uma [assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) no Azure Stack.
 * Crie um aplicativo Web na assinatura do locatário. Anote a nova URL do aplicativo Web para usar mais tarde.
 * Implante uma máquina de Virtual do Windows Server 2012 na assinatura do locatário. Você usará esse servidor como seu servidor de compilação e para executar os serviços de DevOps do Azure.
* Forneça uma imagem do Windows Server 2016 com o .NET 3.5 para uma máquina virtual (VM). Essa VM será criada no Azure Stack como um agente de compilação particular.

### <a name="developer-tool-requirements"></a>Requisitos da ferramenta de desenvolvedor

* Criar uma [espaço de trabalho de serviços do Azure DevOps](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). O processo de inscrição cria um projeto chamado **MyFirstProject**.
* [Instalar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [entrar nos serviços do Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Conectar-se ao seu projeto e [cloná-lo localmente](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Seu ambiente do Azure Stack precisa as imagens corretas seja distribuídas para executar o Windows Server e SQL Server. Ela também deve ter implantado o serviço de aplicativo.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Preparar o agente de Pipelines do Azure privado para a integração de serviços de DevOps do Azure

### <a name="prerequisites"></a>Pré-requisitos

Serviços de DevOps do Azure autentica no Azure Resource Manager usando uma entidade de serviço. Serviços de DevOps do Azure deve ter o **Colaborador** função Provisione recursos em uma assinatura do Azure Stack.

As etapas a seguir descrevem o que é necessário para configurar a autenticação:

1. Criar uma entidade de serviço, ou usar uma entidade de serviço existente.
2. Crie chaves de autenticação para a entidade de serviço.
3. Valide a assinatura do Azure Stack por meio do controle de acesso baseado em função para permitir que o nome Principal de serviço (SPN) para fazer parte da função do Colaborador.
4. Crie uma nova definição de serviço nos serviços de DevOps do Azure usando as informações de SPN e pontos de extremidade do Azure Stack.

### <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Consulte a [criação da entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruções para criar uma entidade de serviço. Escolher **aplicativo Web/API** para o tipo de aplicativo ou [use o script do PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) conforme explicado no artigo [criar uma conexão de serviço do Azure Resource Manager com um serviço existente entidade ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Se você usar o script para criar um ponto de extremidade de pilha do Azure Resource Manager, você precisará passar o **- azureStackManagementURL** parâmetro e **- environmentName** parâmetro. Por exemplo:   
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Criar uma chave de acesso

Uma entidade de serviço requer uma chave para autenticação. Use as etapas a seguir para gerar uma chave.

1. Em **Registros do Aplicativo** no Azure Active Directory, selecione seu aplicativo.

    ![Selecione o aplicativo](media/azure-stack-solution-hybrid-pipeline/000_01.png)

2. Anote o valor de **ID do aplicativo**. Ao configurar o ponto de extremidade de serviço nos serviços de DevOps do Azure, você usará esse valor.

    ![ID do aplicativo](media/azure-stack-solution-hybrid-pipeline/000_02.png)

3. Para gerar uma chave de autenticação, selecione **Configurações**.

    ![Editar configurações do aplicativo](media/azure-stack-solution-hybrid-pipeline/000_03.png)

4. Para gerar uma chave de autenticação, selecione **Chaves**.

    ![Definir configurações chave](media/azure-stack-solution-hybrid-pipeline/000_04.png)

5. Forneça uma descrição para a chave e definir a duração da chave. Ao terminar, escolha **Salvar**.

    ![Duração e a descrição da chave](media/azure-stack-solution-hybrid-pipeline/000_05.png)

    Depois de salvar a chave, a chave **valor** é exibida. Copie esse valor, porque você não pode obter esse valor posteriormente. Você fornece o **valor de chave** com a ID do aplicativo para entrar como o aplicativo. Armazene o valor da chave onde seu aplicativo possa recuperá-lo.

    ![VALOR de chave](media/azure-stack-solution-hybrid-pipeline/000_06.png)

### <a name="get-the-tenant-id"></a>Obter a ID do locatário

Como parte da configuração do ponto de extremidade de serviço, serviços de DevOps do Azure requer o **ID do locatário** que corresponde ao diretório do AAD que seu carimbo de data / Azure Stack é implantado. Use as etapas a seguir para obter a ID de locatário.

1. Selecione **Azure Active Directory**.

    ![Azure Active Directory para o locatário](media/azure-stack-solution-hybrid-pipeline/000_07.png)

2. Para obter a ID de locatário, selecione **Propriedades** do seu locatário do Azure AD.

    ![Exibir propriedades do inquilino](media/azure-stack-solution-hybrid-pipeline/000_08.png)

3. Copie a **ID de diretório**. Esse valor é a ID do locatário.

    ![ID do Diretório](media/azure-stack-solution-hybrid-pipeline/000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Conceder os direitos de entidade de serviço para implantar recursos na assinatura do Azure Stack

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função representa as permissões recomendadas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: Funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e qualquer um de seus recursos.

1. Navegue até o nível do escopo ao qual quer atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

    ![Selecione assinaturas](media/azure-stack-solution-hybrid-pipeline/000_10.png)

2. Na **assinatura**, selecione o Visual Studio Enterprise.

    ![Visual Studio Enterprise](media/azure-stack-solution-hybrid-pipeline/000_11.png)

3. No Visual Studio Enterprise, selecione **controle de acesso (IAM)**.

4. Selecione **Adicionar atribuição de função**.

    ![Adicionar](media/azure-stack-solution-hybrid-pipeline/000_13.png)

5. Na **adicionar permissões**, selecione a função que você deseja atribuir ao aplicativo. Neste exemplo, o **proprietário** função.

    ![Função de proprietário](media/azure-stack-solution-hybrid-pipeline/000_14.png)

6. Por padrão, os aplicativos do Azure Active Directory não são exibidos nas opções disponíveis. Para localizar seu aplicativo, você deve fornecer seu nome na **selecionar** campo para procurá-lo. Selecione o aplicativo.

    ![Resultado de pesquisa do aplicativo](media/azure-stack-solution-hybrid-pipeline/000_16.png)

7. Selecione **Salvar** para finalizar a atribuição da função. Agora você vê o aplicativo na lista de usuários atribuídos a uma função para esse escopo.

### <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

Controle de acesso do Azure baseado em função (RBAC) fornece gerenciamento de acesso refinado para o Azure. Ao usar o RBAC, você pode controlar o nível de acesso que os usuários precisam para realizar seus trabalhos. Para obter mais informações sobre o controle de acesso baseado em função, consulte [gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Pools de agente dos serviços de DevOps do Azure

Em vez de gerenciar separadamente cada agente, você pode organizar os agentes em pools de agente. Um pool de agentes define o limite de compartilhamento para todos os agentes nesse pool. Nos serviços de DevOps do Azure, pools de agente estão no escopo para a organização de serviços de DevOps do Azure, que significa que você pode compartilhar um pool de agentes entre projetos. Para saber mais sobre os pools de agente, consulte [criar Pools de agente e filas](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adicionar um Token de acesso pessoal (PAT) para o Azure Stack

Crie um Token de acesso pessoal para acessar os serviços de DevOps do Azure.

1. Entrar em sua organização de serviços de DevOps do Azure e selecione o nome de perfil da sua organização.

2. Selecione **gerenciar segurança** a página de criação de token de acesso.

    ![Entrada do usuário](media/azure-stack-solution-hybrid-pipeline/000_17.png)

    ![Selecione um projeto](media/azure-stack-solution-hybrid-pipeline/000_18.png)

    ![Adicione o token de acesso pessoal](media/azure-stack-solution-hybrid-pipeline/000_18a.png)

    ![Criar token](media/azure-stack-solution-hybrid-pipeline/000_18b.png)

3. Copie o token.

    > [!Note]
    > Salve as informações de token. Essas informações não são armazenadas e não serão mostradas novamente quando você sair da página de web.

    ![Token de acesso pessoal](media/azure-stack-solution-hybrid-pipeline/000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalação do que agente de compilação de serviços de DevOps do Azure no Azure Stack hospedou o servidor de compilação

1. Conecte-se ao seu servidor de compilação que você implantou no host do Azure Stack.
2. Baixar e implantar o agente de compilação como um serviço usando sua conta pessoal (PAT) do token de acesso e executados como a conta de administrador de VM.

    ![Baixe o agente de compilação](media/azure-stack-solution-hybrid-pipeline/010_downloadagent.png)

3. Navegue até a pasta para o agente de compilação extraídos. Execute o **cmd** arquivo em um prompt de comando elevado.

    ![Agente de compilação extraídos](media/azure-stack-solution-hybrid-pipeline/000_20.png)

    ![Registrar o agente de compilação](media/azure-stack-solution-hybrid-pipeline/000_21.png)

4. Quando o cmd for concluída, a pasta do agente de compilação é atualizada com arquivos adicionais. A pasta com o conteúdo extraído deve ser semelhante ao seguinte:

    ![Atualização de pasta do agente de compilação](media/azure-stack-solution-hybrid-pipeline/009_token_file.png)

    Você pode ver o agente na pasta de serviços de DevOps do Azure.

## <a name="endpoint-creation-permissions"></a>Permissões de criação de ponto de extremidade

Criando pontos de extremidade, uma compilação do Visual Studio Online (VSTO) pode implantar aplicativos de serviço do Azure para o Azure Stack. Serviços de DevOps do Azure se conecta ao agente de compilação, que se conecta ao Azure Stack.

![Aplicativo de exemplo NorthwindCloud no VSTO](media/azure-stack-solution-hybrid-pipeline/012_securityendpoints.png)

1. Entre no VSTO e navegue até a página de configurações do aplicativo.
2. Em **Configurações**, selecione **Segurança**.
3. Na **grupos de serviços do Azure DevOps**, selecione **criadores de ponto de extremidade**.

    ![Criadores de ponto de extremidade NorthwindCloud](media/azure-stack-solution-hybrid-pipeline/013_endpoint_creators.png)

4. Sobre o **membros** guia, selecione **Add**.

    ![Adicionar um membro](media/azure-stack-solution-hybrid-pipeline/014_members_tab.png)

5. Na **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.
6. Selecione **Salvar alterações**.
7. No **grupos de serviços do Azure DevOps** lista, selecione **administradores de ponto de extremidade**.

    ![Administradores do ponto de extremidade NorthwindCloud](media/azure-stack-solution-hybrid-pipeline/015_save_endpoint.png)

8. Sobre o **membros** guia, selecione **Add**.
9. Na **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.
10. Selecione **Salvar alterações**.

Agora que as informações de ponto de extremidade existem, os serviços de DevOps do Azure para conexão do Azure Stack está pronto para uso. O agente de compilação no Azure Stack obtém as instruções dos serviços de DevOps do Azure e, em seguida, o agente transmite informações de ponto de extremidade para comunicação com o Azure Stack.

## <a name="create-an-azure-stack-endpoint"></a>Criar um ponto de extremidade do Azure Stack

### <a name="create-an-endpoint-for-azure-ad-deployments"></a>Criar um ponto de extremidade para implantações do AD do Azure

Você pode seguir as instruções em [criar entidade de uma conexão de serviço do Azure Resource Manager com um serviço existente ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) artigo para criar uma conexão de serviço com um serviço existente principal e use o seguinte mapeamento:

Você pode criar uma conexão de serviço usando o seguinte mapeamento:

| NOME | Exemplo | DESCRIÇÃO |
| --- | --- | --- |
| Nome da conexão | Pilha do Azure AD do Azure | O nome da conexão. |
| Ambiente | AzureStack | O nome do seu ambiente. |
| URL de ambiente | `https://management.local.azurestack.external` | O ponto de extremidade de gerenciamento. |
| Nível de escopo | Assinatura | O escopo do que a conexão. |
| ID da assinatura | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID de assinatura de usuário do Azure Stack |
| Nome da assinatura | name@contoso.com | Nome da assinatura de usuário do Azure Stack. |
| ID do cliente de entidade de serviço | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | A ID da entidade de [isso](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) seção neste artigo. |
| Chave da entidade de serviço | THESCRETGOESHERE= | A chave do mesmo artigo (ou a senha, se você usou o script). |
| ID do locatário | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | A ID do locatário recuperar seguindo as instruções em [obter a ID do locatário](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).  |
| Conexão: | Não verificado | Valide suas configurações de conexão para a entidade de serviço. |

Agora que o ponto de extremidade é criado, o DevOps para conexão do Azure Stack está pronto para uso. O agente de compilação no Azure Stack obtém as instruções do DevOps e, em seguida, o agente transmite informações de ponto de extremidade para comunicação com o Azure Stack.

![Agente do Azure AD de compilação](media/azure-stack-solution-hybrid-pipeline/016_save_changes.png)

### <a name="create-an-endpoint-for-ad-fs"></a>Criar um ponto de extremidade para o AD FS

A atualização mais recente para DevOps do Azure permite criar uma conexão de serviço usando uma entidade de serviço com um certificado para autenticação. Isso é necessário quando o Azure Stack é implantado com o AD FS como provedor de identidade. 

![O AD FS do agente de compilação](media/azure-stack-solution-hybrid-pipeline/image06.png)

Você pode criar uma conexão de serviço usando o seguinte mapeamento:

| NOME | Exemplo | DESCRIÇÃO |
| --- | --- | --- |
| Nome da conexão | Azure Stack ADFS | O nome da conexão. |
| Ambiente | AzureStack | O nome do seu ambiente. |
| URL de ambiente | `https://management.local.azurestack.external` | O ponto de extremidade de gerenciamento. |
| Nível de escopo | Assinatura | O escopo do que a conexão. |
| ID da assinatura | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID de assinatura de usuário do Azure Stack |
| Nome da assinatura | name@contoso.com | Nome da assinatura de usuário do Azure Stack. |
| ID do cliente de entidade de serviço | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | A ID do cliente da entidade de serviço que você criou para o AD FS. |
| Certificado | `<certificate>` |  Converta o arquivo de certificado de PFX para PEM. Cole o conteúdo do arquivo do certificado PEM neste campo. <br> Convertendo de PFX para PEM:<br>`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` |
| ID do locatário | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | A ID do locatário recuperar seguindo as instruções em [obter a ID do locatário](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id). |
| Conexão: | Não verificado | Valide suas configurações de conexão para a entidade de serviço. |

Agora que o ponto de extremidade é criado, o DevOps do Azure para conexão do Azure Stack está pronto para uso. O agente de compilação no Azure Stack obtém as instruções do DevOps do Azure e, em seguida, o agente transmite informações de ponto de extremidade para comunicação com o Azure Stack.

> [!Note]
> Se o ponto de extremidade ARM de usuário do Azure Stack não é exposto à Internet, a validação de conexão falhará. Isso é esperado e você pode validar sua conexão com a criação de um pipeline de lançamento com uma tarefa simples. 

## <a name="develop-your-application-build"></a>Desenvolver sua compilação do aplicativo

Nesta parte do tutorial, você vai:

* Adicione o código a um projeto de serviços de DevOps do Azure.
* Crie implantação de aplicativo web independente.
* Configurar o processo de implantação contínua

> [!Note]
 > Seu ambiente do Azure Stack precisa as imagens corretas seja distribuídas para executar o Windows Server e SQL Server. Ela também deve ter implantado o serviço de aplicativo. Leia a documentação do serviço de aplicativo seção "Pré-requisitos" para obter os requisitos de operador do Azure Stack.

CI/CD híbrido pode aplicar ao código do aplicativo e o código de infraestrutura. Use [modelos do Azure Resource Manager, como web ](https://azure.microsoft.com/resources/templates/) código de aplicativo dos serviços de DevOps do Azure para implantar em ambas as nuvens.

### <a name="add-code-to-an-azure-devops-services-project"></a>Adicione o código a um projeto de serviços de DevOps do Azure

1. Entrar nos serviços de DevOps do Azure com uma organização que tem direitos de criação do projeto no Azure Stack. A próxima captura de tela mostra como conectar-se ao projeto HybridCICD.

    ![Conectar a um projeto](media/azure-stack-solution-hybrid-pipeline/017_connect_to_project.png)

2. **Clone o repositório** criando e abrindo o aplicativo da web padrão.

    ![Clone o repositório](media/azure-stack-solution-hybrid-pipeline/018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar a implantação de aplicativo web independente para serviços de aplicativos em ambas as nuvens

1. Editar o **WebApplication.csproj** arquivo: Selecione **Runtimeidentifier** e, em seguida, adicione `win10-x64.` para obter mais informações, consulte [implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.

    ![Configurar Runtimeidentifier](media/azure-stack-solution-hybrid-pipeline/019_runtimeidentifer.png)

2. Use o Team Explorer para verificar o código nos serviços de DevOps do Azure.

3. Confirme se o código do aplicativo foi marcado nos serviços de DevOps do Azure.

### <a name="create-the-build-pipeline"></a>Criar o pipeline de build

1. Entrar nos serviços de DevOps do Azure com uma organização que pode criar um pipeline de compilação.

2. Navegue até a **compilar o aplicativo de Web** página para o projeto.

3. Na **argumentos**, adicione **- r win10-x64** código. Isso é necessário para disparar uma implantação autocontida com.Net Core.

    ![Adicionar o pipeline de build do argumento](media/azure-stack-solution-hybrid-pipeline/020_publish_additions.png)

4. Execute a compilação. O [compilação de implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará os artefatos que podem ser executados no Azure e Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Uso do Azure hospedados agente de compilação

Usando um agente de compilação hospedado nos serviços de DevOps do Azure é uma opção conveniente para a criação e implantação de aplicativos web. Atualizações e manutenção do agente são executadas automaticamente pelo Microsoft Azure, que permite que um ciclo de desenvolvimento contínuo e sem interrupções.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurar o processo de implantação contínua (CD)

Team Foundation Server (TFS) e serviços de DevOps do Azure fornecem um pipeline totalmente configurável e gerenciável para as versões em vários ambientes, como desenvolvimento, preparo, garantia de qualidade (QA) e produção. Esse processo pode incluir a necessidade de aprovações em estágios específicos do ciclo de vida do aplicativo.

### <a name="create-release-pipeline"></a>Criar pipeline de lançamento

Criar um pipeline de lançamento é o processo de compilação a etapa final em seu aplicativo. Esse pipeline de lançamento é usado para criar uma versão e implantar uma compilação.

1. Entrar nos serviços de DevOps do Azure e navegue até **Pipelines do Azure** para seu projeto.
2. Sobre o **versões** guia, selecione  **\[ +]** e, em seguida, escolha **criar definição de versão**.

   ![Criar pipeline de lançamento](media/azure-stack-solution-hybrid-pipeline/021a_releasedef.png)

3. Na **selecione um modelo**, escolha **implantação de serviço de aplicativo do Azure**e, em seguida, selecione **aplicar**.

    ![Aplicar modelo](media/azure-stack-solution-hybrid-pipeline/102.png)

4. Na **adicionar artefato**, da **fonte (definição de compilação)** menu suspenso, selecione o aplicativo de build de nuvem do Azure.

    ![Adicionar artefato](media/azure-stack-solution-hybrid-pipeline/103.png)

5. Sobre o **Pipeline** guia, selecione o **1 fase**, **1 tarefa** vincular ao **exibir tarefas do ambiente**.

    ![Tarefas da exibição de pipeline](media/azure-stack-solution-hybrid-pipeline/104.png)

6. No **tarefas** , insira Azure como o **nome do ambiente** e selecione AzureCloud Traders Web EP do **assinatura do Azure** lista suspensa.

    ![Configurar variáveis de ambiente](media/azure-stack-solution-hybrid-pipeline/105.png)

7. Insira o **nome do serviço de aplicativo do Azure**, que é "northwindtraders" na próxima captura de tela.

    ![Nome do serviço de aplicativo](media/azure-stack-solution-hybrid-pipeline/106.png)

8. Para a fase de agente, selecione **VS2017 hospedado** da **fila de agentes** lista suspensa.

    ![Agente hospedado](media/azure-stack-solution-hybrid-pipeline/107.png)

9. Na **implantar o serviço de aplicativo do Azure**, selecione válidos **pacote ou pasta** para o ambiente.

    ![Selecione o pacote ou pasta](media/azure-stack-solution-hybrid-pipeline/108.png)

10. Na **selecione o arquivo ou pasta**, selecione **Okey** para **local**.

    ![Texto ALT](media/azure-stack-solution-hybrid-pipeline/109.png)

11. Salve todas as alterações e voltar para **Pipeline**.

    ![Texto ALT](media/azure-stack-solution-hybrid-pipeline/110.png)

12. No **Pipeline** guia, selecione **adicionar um artefato**e escolha o **NorthwindCloud Traders-Vessel** do **fonte (definição de compilação)** lista suspensa.

    ![Adicionar novo artefato](media/azure-stack-solution-hybrid-pipeline/111.png)

13. Na **selecione um modelo**, adicione outro ambiente. Escolher **implantação de serviço de aplicativo do Azure** e, em seguida, selecione **aplicar**.

    ![Selecionar modelo](media/azure-stack-solution-hybrid-pipeline/112.png)

14. Insira "Azure Stack" como o **nome do ambiente**.

    ![Nome do ambiente](media/azure-stack-solution-hybrid-pipeline/113.png)

15. Sobre o **tarefas** guia, localize e selecione o Azure Stack.

    ![Ambiente de pilha do Azure](media/azure-stack-solution-hybrid-pipeline/114.png)

16. Dos **assinatura do Azure** lista suspensa, selecione "AzureStack Vessel Traders EP" para o ponto de extremidade do Azure Stack.

    ![Texto ALT](media/azure-stack-solution-hybrid-pipeline/115.png)

17. Insira o nome do aplicativo web do Azure Stack como o **nome do serviço de aplicativo**.

    ![Nome do serviço de aplicativo](media/azure-stack-solution-hybrid-pipeline/116.png)

18. Sob **seleção de agente**, escolha "AzureStack - bDouglas Fir" entre a **fila do agente** lista suspensa.

    ![Escolher agente](media/azure-stack-solution-hybrid-pipeline/117.png)

19. Para **implantar o serviço de aplicativo do Azure**, selecione válidos **pacote ou pasta** para o ambiente. Na **Selecionar arquivo ou pasta**, selecione **Okey** para a pasta **local**.

    ![Escolha o pacote ou pasta](media/azure-stack-solution-hybrid-pipeline/118.png)

    ![Aprovar local](media/azure-stack-solution-hybrid-pipeline/119.png)

20. Sobre o **variável** guia, localize a variável nomeada **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Defina o valor da variável como **verdadeira**e defina seu escopo para **do Azure Stack**.

    ![Definir variável](media/azure-stack-solution-hybrid-pipeline/120.png)

21. No **Pipeline** guia, selecione o **gatilho de implantação contínua** ícone para o artefato NorthwindCloud Traders-Web e defina o **gatilho de implantação contínua** para **Habilitado**.  Fazer a mesma coisa para o artefato "NorthwindCloud Traders-Vessel".

    ![Gatilho de implantação contínua de conjunto](media/azure-stack-solution-hybrid-pipeline/121.png)

22. Para o ambiente do Azure Stack, selecione a **condições de pré-implantação** ícone definido o gatilho para **após o lançamento**.

    ![Gatilho do conjunto de condições de pré-implantação](media/azure-stack-solution-hybrid-pipeline/122.png)

23. Salve todas as alterações.

> [!Note]
> Algumas configurações para tarefas de liberação podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) quando você criou um pipeline de lançamento de um modelo. Essas configurações não podem ser modificadas as configurações da tarefa. No entanto, você pode editar essas configurações nos itens de ambiente do pai.

## <a name="create-a-release"></a>Criar uma versão

Agora que você tiver concluído as modificações no pipeline de lançamento, é hora de começar a implantação. Para fazer isso, você deve criar uma versão do pipeline de lançamento. Uma versão pode ser criada automaticamente. Por exemplo, o gatilho de implantação contínua é definido no pipeline de lançamento. Isso significa que modificar o código-fonte iniciará uma nova compilação e, de que, uma nova versão. No entanto, nesta seção você criará uma nova versão manualmente.

1. No **Pipeline** guia, abra o **Release** lista suspensa lista e escolha **criar versão**.

    ![Criar uma versão](media/azure-stack-solution-hybrid-pipeline/200.png)

2. Insira uma descrição para a versão, verifique se os artefatos corretos estão selecionados e, em seguida, escolha **criar**. Após alguns instantes, uma faixa será exibida indicando que a nova versão foi criada e o nome de versão é exibido como um link. Escolha o link para ver a página de resumo de versão.

    ![Faixa de criação de versão](media/azure-stack-solution-hybrid-pipeline/201.png)

3. A página de resumo de versão para mostra detalhes sobre a versão. Na captura de tela para "Release-2", o **ambientes** seção mostra as **status da implantação** para o Azure como "Em andamento" e o status para o Azure Stack é "êxito". Quando o status da implantação para o ambiente do Azure é alterado para "Êxito", uma faixa será exibida, indicando que a versão está pronta para aprovação. Quando uma implantação está pendente ou tiver falhado, um azul **(i)** ícone de informações é mostrada. Passe o mouse sobre o ícone para ver um pop-up que contém a razão para atraso ou a falha.

    ![Página de resumo da versão](media/azure-stack-solution-hybrid-pipeline/202.png)

Outros modos de exibição, como a lista de versões, também será exibido um ícone que indica a aprovação está pendente. O pop-up para esse ícone mostra o nome do ambiente e obter mais detalhes relacionados à implantação. É fácil para um administrador, consulte o progresso geral de versões e veja quais versões estão aguardando aprovação.

### <a name="monitor-and-track-deployments"></a>Monitorar e controlar implantações

Esta seção mostra como você pode monitorar e acompanhar todas as suas implantações. A versão para implantar os dois sites de serviços de aplicativo do Azure fornece um bom exemplo.

1. Na página de resumo "Release-2", selecione **Logs**. Durante a implantação, essa página mostra o log em tempo real do agente. O painel esquerdo mostra o status de cada operação na implantação para cada ambiente.

    Você pode escolher um ícone de pessoa na **ação** coluna de uma aprovação de pré-implantação ou pós-implantação para consultar quem aprovada (ou rejeitados) a implantação e a mensagem fornecidas por elas.

2. Depois que a implantação for concluída, todo o arquivo de log é exibido no painel direito. Você pode selecionar qualquer **etapa** no painel esquerdo para ver o arquivo de log para uma única etapa, como "Inicializar o trabalho". A capacidade de ver logs individuais torna mais fácil rastrear e depurar partes da implantação geral. Você também pode **salve** o arquivo de log para uma etapa, ou **baixar todos os logs como zip**.

    ![Registros de liberação](media/azure-stack-solution-hybrid-pipeline/203.png)

3. Abra o **resumo** guia para obter informações gerais sobre a versão. Essa exibição mostra detalhes sobre a compilação, os ambientes em que ele foi implantado, status da implantação e outras informações sobre a versão.

4. Selecione um link de ambiente (**Azure** ou **do Azure Stack**) para ver informações sobre existente e implantações em um ambiente específico pendentes. Você pode usar esses modos de exibição como uma maneira rápida de verificar se a mesma compilação foi implantada para os dois ambientes.

5. Abra o **implantou o aplicativo de produção** no seu navegador. Por exemplo, para o site de serviços de aplicativo do Azure, abra a URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
