---
title: Criar soluções de colocação em escala de nuvem com o Azure | Microsoft Docs
description: Aprenda a criar soluções de colocação em escala de nuvem com o Azure.
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 57624133b249a8ec2ece90eac4a64729e4d15151
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968196"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Tutorial: Criar soluções de colocação em escala de nuvem com o Azure

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como criar uma solução de nuvem para fornecer um processo disparado manualmente para alternar de uma pilha do Azure hospedado o aplicativo web, do Azure hospedados no aplicativo web com o dimensionamento automático por meio do Gerenciador de tráfego, garantindo o utilitário de nuvem flexível e escalonável sob carga.

Com esse padrão, seu locatário não pode ser pronto para executar seu aplicativo na nuvem pública. No entanto, pode não ser economicamente viável para os negócios manter a capacidade necessária em seu ambiente local para lidar com picos de demanda para o aplicativo. Seu locatário pode levar usar a elasticidade da nuvem pública com sua solução local.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie um aplicativo web com vários nós.
> - Configurar e gerenciar o processo de implantação contínua (CD).
> - Publica o aplicativo web para o Azure Stack.
> - Crie uma versão.
> - Saiba como monitorar e acompanhar as implantações.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar aplicativos híbridos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

-   Assinatura do Azure. Se necessário, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um sistema integrado do Azure Stack ou implantação do Kit de desenvolvimento do Azure Stack.
    - Você encontrar instruções para instalar o Azure Stack no [instalar o Kit de desenvolvimento do Azure Stack](../asdk/asdk-install.md).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Essa instalação pode exigir algumas horas para ser concluído.

-   Implante [serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) os serviços de PaaS para o Azure Stack.

-   [Criar plano/ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro do ambiente do Azure Stack.

-   [Criar assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente do Azure Stack.

-   Crie um aplicativo Web na assinatura do locatário. Anote a nova URL do aplicativo Web para usar mais tarde.

-   Implante a máquina Virtual do Azure Pipelines dentro da assinatura de locatário.

-   VM Windows Server 2016 com o .NET 3.5 necessário. Essa VM será criada na assinatura do locatário no Azure Stack como o agente de compilação particular.

-   [Windows Server 2016 com imagem de VM do SQL 2017](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) está disponível no Azure Stack Marketplace. Se essa imagem não estiver disponível, trabalhe com um operador de pilha do Azure para garantir que ele é adicionado ao ambiente.

## <a name="issues-and-considerations"></a>Problemas e considerações

### <a name="scalability-considerations"></a>Considerações sobre escalabilidade

O componente de chave de ajuste de escala de nuvem é a capacidade de oferecer dimensionamento imediato sob demanda entre público locais e na infraestrutura de nuvem, provando serviço consistente e confiável conforme prescrito por demanda.

### <a name="availability-considerations"></a>Considerações sobre disponibilidade

Certifique-se localmente de aplicativos implantados são configurados para alta disponibilidade por meio da implantação de software e configuração de hardware no local.

### <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento

A solução de nuvem garante que o gerenciamento contínuo e uma interface familiar entre ambientes. PowerShell é recomendado para gerenciamento de plataforma cruzada.

## <a name="cross-cloud-scaling"></a>Escala de nuvem

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Obter um domínio personalizado e configurar o DNS

Atualize o arquivo de zona DNS para o domínio. Azure AD verificará a propriedade de nome de domínio personalizado. Use [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) para os registros DNS do Azure/Office 365/DNS externo dentro do Azure, ou adicionar a entrada DNS em [um registrador DNS diferente](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Registre um domínio personalizado com um registrador de público.

2.  Entre no registrador de nome de domínio para o domínio. Um administrador aprovado talvez precise fazer atualizações DNS. 

3.  Atualize o arquivo de zona DNS para o domínio adicionando a entrada DNS fornecida pelo Azure AD. (A entrada DNS não afetará o roteamento de email ou comportamentos de hospedagem na web.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Criar um aplicativo web com vários nós padrão no Azure Stack

Configurar a integração contínua híbrida e implantação contínua (CI/CD) para implantar aplicativo Web no Azure e o Azure Stack e automático de alterações por push para ambas as nuvens.

> [!Note]  
> O Azure Stack com imagens adequados seja distribuído para execução (Windows Server e SQL) e a implantação do serviço de aplicativo são necessárias. Leia a documentação do serviço de aplicativo "[antes de começar com o serviço de aplicativo no Azure Stack](../azure-stack-app-service-before-you-get-started.md)" seção para o operador de pilha do Azure.

### <a name="add-code-to-azure-repos"></a>Adicione código para repositórios do Azure

Azure Repos

1. Entrar para repositórios do Azure com uma conta que tenha direitos de criação do projeto em repositórios do Azure.

    CI/CD híbrido pode aplicar ao código do aplicativo e o código de infraestrutura. Use [modelos do Azure Resource Manager](https://azure.microsoft.com/resources/templates/) para ambos os desenvolvimento em nuvem privada e hospedado.

    ![Alt text](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Clone o repositório** criando e abrindo o aplicativo da web padrão.

    ![Alt text](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar a implantação de aplicativo web independente para serviços de aplicativos em ambas as nuvens

1.  Editar o **WebApplication.csproj** arquivo. Selecione **Runtimeidentifier** e adicione **win10-x64**. (Consulte [contained implantação](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.) 

    ![Alt text](media/azure-stack-solution-cloud-burst/image3.png)

2.  Verifique o código para repositórios do Azure usando o Team Explorer.

3.  Confirme que o código do aplicativo foi verificado em repositórios do Azure.

## <a name="create-the-build-definition"></a>Criar a definição de compilação

1. Log em Pipelines do Azure a fim de confirmar a capacidade de criar definições de compilação.

2. Adicione **- r win10-x64** código. Isso é necessário para disparar uma implantação autocontida com.Net Core.

    ![Alt text](media/azure-stack-solution-cloud-burst/image4.png)

3. Execute a compilação. O [compilação de implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará os artefatos que podem ser executados no Azure e Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Agente hospedado do uso do Azure

Use um agente hospedado em Pipelines do Azure é uma opção conveniente para criar e implantar aplicativos web. Manutenção e atualizações são executadas automaticamente pelo Microsoft Azure, permitindo a implantação, teste e desenvolvimento contínuo e sem interrupções.

### <a name="manage-and-configure-the-cd-process"></a>Gerenciar e configurar o processo de CD

O servidor de DevOps do Azure e Pipelines do Azure fornecem um pipeline totalmente configurável e gerenciável para as versões em vários ambientes, como desenvolvimento, preparo, QA e ambientes de produção; incluindo a necessidade de aprovações em estágios específicos.

## <a name="create-release-definition"></a>Criar definição de versão

![Alt text](media/azure-stack-solution-cloud-burst/image5.png)

1.  Selecione o **plus** botão para adicionar uma nova versão sob o **guia Releases** na página de Build e versão do VSO.

    ![Alt text](media/azure-stack-solution-cloud-burst/image6.png)

2. Aplique o modelo de implantação de serviço de aplicativo do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image7.png)

3. Em Adicionar um artefato, adicione o artefato para o aplicativo de compilação na nuvem do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image8.png)

4. Na guia Pipeline, selecione a **fase, a tarefa** vincular do ambiente e definir valores de ambiente de nuvem do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image9.png)

5. Defina as **nome do ambiente** e selecione Azure **assinatura** para o ponto de extremidade de nuvem do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image10.png)

6. Em nome do ambiente, defina exigida **nome do serviço de aplicativo do Azure**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image11.png)

7. Insira **VS2017 hospedado** em fila do agente para o ambiente hospedado na nuvem do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image12.png)

8. No menu de implantar o serviço de aplicativo do Azure, selecione válidos **pacote ou pasta** para o ambiente. Selecione **Okey** à **local da pasta**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image13.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image14.png)

9. Salve todas as alterações e voltar para **pipeline de lançamento**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image15.png)

10. Adicione um novo artefato selecionando o build para o aplicativo do Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image16.png)

11. Adicione um ambiente mais aplicando a implantação do serviço de aplicativo do Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image17.png)

12. Nomeie o novo ambiente do Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image18.png)

13. Encontre o ambiente do Azure Stack sob **tarefa** guia.

    ![Alt text](media/azure-stack-solution-cloud-burst/image19.png)

14. Selecione a assinatura para o ponto de extremidade do Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image20.png)

15. Defina o nome do aplicativo web do Azure Stack como o nome do serviço de aplicativo.

    ![Alt text](media/azure-stack-solution-cloud-burst/image21.png)

16. Selecione o agente do Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image22.png)

17. Sob o serviço de aplicativo do Azure implantar seção Selecionar válidos **pacote ou pasta** para o ambiente. Selecione **Okey** para o local da pasta.

    ![Alt text](media/azure-stack-solution-cloud-burst/image23.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image24.png)

18. Na guia variável, adicione uma variável denominada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, defina seu valor como **verdadeiro**e o escopo para o Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image25.png)

19. Selecione o **contínuo** ícone de gatilho de implantação em artefatos e habilite a **continua** gatilho de implantação.

    ![Alt text](media/azure-stack-solution-cloud-burst/image26.png)

20. Selecione o **pré-implantação** ícone de condições no ambiente do Azure Stack e defina o gatilho como **após o lançamento.**

21. Salve todas as alterações.

> [!Note]  
> Algumas configurações para as tarefas podem ter sido automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) durante a criação de uma definição de versão de um modelo. Essas configurações não podem ser modificadas as configurações da tarefa; em vez disso, o item de ambiente pai deve ser selecionado para editar essas configurações

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publicar no Azure Stack por meio do Visual Studio

Criando pontos de extremidade, uma compilação do Visual Studio Online (VSTO) pode implantar aplicativos de serviço do Azure para o Azure Stack. Pipelines do Azure se conecta ao agente de compilação, que se conecta ao Azure Stack.

1.  Entre no VSTO e navegue até a página de configurações do aplicativo.

2.  Em **Configurações**, selecione **Segurança**.

3.  Na **grupos do VSTS**, selecione **criadores de ponto de extremidade**.

4.  Sobre o **membros** guia, selecione **Add**.

5.  Na **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.

6.  Selecione **Salvar alterações**.

7.  No **grupos do VSTS** lista, selecione **administradores de ponto de extremidade**.

8.  Sobre o **membros** guia, selecione **Add**.

9.  Na **adicionar usuários e grupos**, insira um nome de usuário e selecione o usuário na lista de usuários.

10. Selecione **Salvar alterações**.

Agora que as informações de ponto de extremidade existem, os Pipelines do Azure para conexão do Azure Stack está pronto para uso. O agente de compilação no Azure Stack obtém as instruções de Pipelines do Azure e, em seguida, o agente transmite informações de ponto de extremidade para comunicação com o Azure Stack.

## <a name="develop-the-application-build"></a>Desenvolver a compilação do aplicativo

> [!Note]  
> O Azure Stack com imagens adequados seja distribuído para execução (Windows Server e SQL) e a implantação do serviço de aplicativo são necessárias. Leia a documentação do serviço de aplicativo "[antes de começar com o serviço de aplicativo no Azure Stack](../azure-stack-app-service-before-you-get-started.md)" seção para o operador de pilha do Azure.

Use [modelos do Azure Resource Manager, como web](https://azure.microsoft.com/resources/templates/) código de aplicativo de repositórios do Azure para implantar em ambas as nuvens.

### <a name="add-code-to-a-azure-repos-project"></a>Adicionar código a um projeto de repositórios do Azure

1.  Entrar para repositórios do Azure com uma conta que tenha direitos de criação do projeto no Azure Stack. A próxima captura de tela mostra como conectar-se ao projeto HybridCICD.

2.  **Clone o repositório** criando e abrindo o aplicativo da web padrão.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Criar a implantação de aplicativo web independente para serviços de aplicativos em ambas as nuvens

1.  Editar o **WebApplication.csproj** arquivo: selecione **Runtimeidentifier** e, em seguida, adicione win10-x64. Para obter mais informações, consulte [implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentação.

2.  Use o Team Explorer para verificar o código em repositórios do Azure.

3.  Confirme se o código do aplicativo foi marcado em repositórios do Azure.

### <a name="create-the-build-definition"></a>Criar a definição de compilação

1.  Entrar Pipelines do Azure com uma conta que pode criar uma definição de compilação.

2.  Navegue até a **compilar o aplicativo de Web** página para o projeto.

3.  Na **argumentos**, adicione **- r win10-x64** código. Isso é necessário para disparar uma implantação autocontida com.Net Core.

4.  Execute a compilação. O [compilação de implantação autocontida](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo publicará os artefatos que podem ser executados no Azure e Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Uso do Azure hospedados agente de compilação

Usando um agente de compilação hospedado em Pipelines do Azure é uma opção conveniente para a criação e implantação de aplicativos web. Atualizações e manutenção do agente são executadas automaticamente pelo Microsoft Azure, que permite que um ciclo de desenvolvimento contínuo e sem interrupções.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurar o processo de implantação contínua (CD)

O servidor de DevOps do Azure e Pipelines do Azure fornecem um pipeline totalmente configurável e gerenciável para as versões em vários ambientes, como desenvolvimento, preparo, garantia de qualidade (QA) e produção. Esse processo pode incluir a necessidade de aprovações em estágios específicos do ciclo de vida do aplicativo.

#### <a name="create-release-definition"></a>Criar definição de versão

Criar uma definição de versão é o processo de compilação a etapa final no aplicativo. Essa definição de versão é usada para criar uma versão e implantar uma compilação.

1.  Entrar em Pipelines do Azure e navegue até **Build e versão** para o projeto.

2.  Sobre o **versões** guia, selecione **[+]** e, em seguida, escolher **criar definição de versão**.

3.  Na **selecione um modelo**, escolha **implantação de serviço de aplicativo do Azure**e, em seguida, selecione **aplicar**.

4.  Na **adicionar artefato**, da **fonte (definição de compilação)** selecione o aplicativo de build de nuvem do Azure.

5.  Sobre o **Pipeline** guia, selecione o **1 fase**, **1 tarefa** vincular ao **exibir tarefas do ambiente**.

6.  No **tarefas** , insira Azure como o **nome do ambiente** e selecione AzureCloud Traders Web EP do **assinatura do Azure** lista.

7.  Insira o **nome do serviço de aplicativo do Azure**, que é `northwindtraders` na próxima captura de tela.

8.  Para a fase de agente, selecione **VS2017 hospedado** da **fila de agentes** lista.

9.  Na **implantar o serviço de aplicativo do Azure**, selecione válidos **pacote ou pasta** para o ambiente.

10. Na **selecione o arquivo ou pasta**, selecione **Okey** para **local**.

11. Salve todas as alterações e voltar para **Pipeline**.

12. No **Pipeline** guia, selecione **adicionar um artefato**e escolha o **NorthwindCloud Traders-Vessel** do **fonte (definição de compilação)** lista.

13. Na **selecione um modelo**, adicione outro ambiente. Escolher **implantação de serviço de aplicativo do Azure** e, em seguida, selecione **aplicar**.

14. Insira `Azure Stack` como o **nome do ambiente**.

15. Sobre o **tarefas** guia, localize e selecione o Azure Stack.

16. Dos **assinatura do Azure** lista, selecione **AzureStack Vessel Traders EP** para o ponto de extremidade do Azure Stack.

17. Insira o nome do aplicativo web do Azure Stack como o **nome do serviço de aplicativo**.

18. Sob **seleção de agente**, escolha **AzureStack -b Fir Douglas** do **fila do agente** lista.

19. Para **implantar o serviço de aplicativo do Azure**, selecione válidos **pacote ou pasta** para o ambiente. Na **Selecionar arquivo ou pasta**, selecione **Okey** para a pasta **local**.

20. Sobre o **variável** guia, localize a variável chamada `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Defina o valor da variável como **verdadeira**e defina seu escopo para **do Azure Stack**.

21. No **Pipeline** guia, selecione o **gatilho de implantação contínua** ícone para o artefato NorthwindCloud Traders-Web e defina o **gatilho de implantação contínua** para **Habilitado**. Fazer a mesma coisa o **NorthwindCloud Traders-Vessel** artefato.

22. Para o ambiente do Azure Stack, selecione a **condições de pré-implantação** ícone definido o gatilho para **após o lançamento**.

23. Salve todas as alterações.

> [!Note]  
> Algumas configurações para tarefas de liberação são automaticamente definidas como [variáveis de ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) durante a criação de uma definição de versão de um modelo. Essas configurações não podem ser modificadas as configurações da tarefa, mas podem ser modificadas nos itens de ambiente do pai.

## <a name="create-a-release"></a>Criar uma versão

1.  Sobre o **Pipeline** guia, abra o **Release** lista e escolha **criar versão**.

2.  Insira uma descrição para a versão, verifique se os artefatos corretos estão selecionados e, em seguida, escolha **criar**. Após alguns instantes, uma faixa será exibida indicando que a nova versão foi criada e o nome de versão é exibido como um link. Escolha o link para ver a página de resumo de versão.

3.  A página de resumo de versão para mostra detalhes sobre a versão. Na captura de tela para "Release-2", o **ambientes** seção mostra as **status da implantação** para o Azure como "Em andamento" e o status para o Azure Stack é "êxito". Quando o status da implantação para o ambiente do Azure é alterado para "Êxito", uma faixa será exibida, indicando que a versão está pronta para aprovação. Quando uma implantação está pendente ou tiver falhado, um azul **(i)** ícone de informações é mostrada. Passe o mouse sobre o ícone para ver um pop-up que contém a razão para atraso ou a falha.

4.  Outros modos de exibição, como a lista de versões, também exibir um ícone que indica a aprovação está pendente. O pop-up para esse ícone mostra o nome do ambiente e obter mais detalhes relacionados à implantação. É fácil para um administrador, consulte o progresso geral de versões e veja quais versões estão aguardando aprovação.

## <a name="monitor-and-track-deployments"></a>Monitorar e controlar implantações

1.  Sobre o **Release 2** página de resumida, selecione **Logs**. Durante a implantação, essa página mostra o log em tempo real do agente. O painel esquerdo mostra o status de cada operação na implantação para cada ambiente.

2.  Escolha um ícone de pessoa na **ação** coluna de uma aprovação de pré-implantação ou pós-implantação para consultar quem aprovada (ou rejeitados) a implantação e a mensagem fornecidas por elas.

3.  Depois que a implantação for concluída, todo o arquivo de log é exibido no painel direito. Selecione qualquer **etapa** no painel esquerdo para ver o arquivo de log para uma única etapa, como **inicializar trabalho**. A capacidade de ver logs individuais torna mais fácil rastrear e depurar partes da implantação geral. **Salve** o arquivo de log para uma etapa, ou **baixar todos os logs como zip**.

4.  Abra o **resumo** guia para obter informações gerais sobre a versão. Essa exibição mostra detalhes sobre a compilação, os ambientes em que ele foi implantado, status da implantação e outras informações sobre a versão.

5.  Selecione um link de ambiente (**Azure** ou **do Azure Stack**) para ver informações sobre existente e implantações em um ambiente específico pendentes. Use esses modos de exibição como uma maneira rápida de verificar se a mesma compilação foi implantada para os dois ambientes.

6.  Abra o **implantou o aplicativo de produção** no navegador. Por exemplo, para o site de serviços de aplicativo do Azure, abra a URL [http://[your-app-name\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net).

**A integração do Azure e o Azure Stack oferece uma solução de nuvem escalonável**

Um serviço de nuvem com vários robusto e flexível fornece segurança de dados volta up e redundância, rápida e consistente de disponibilidade, armazenamento dimensionável e distribuição e roteamento em conformidade geográfica. Esse processo disparado manualmente garante carga confiável e eficiente, alternando entre os aplicativos Web hospedados, garantindo a disponibilidade imediata dos dados cruciais. 

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).
