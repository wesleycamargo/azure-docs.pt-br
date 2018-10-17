---
title: Criar uma solução com o Azure e o Azure Stack de aprendizado de máquina de borda | Microsoft Docs
description: Saiba como criar uma solução de aprendizado de máquina do edge usando o Python com o Azure e o Azure Stack.
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
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 28ff8dbf073596e5f9565c56ae903af6af68f3e2
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353694"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Tutorial: Criar uma solução com o Azure e o Azure Stack de aprendizado de máquina de borda

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Saiba como criar uma solução com o Azure e o Azure Stack de aprendizado de máquina de borda.

Neste tutorial, você criará um ambiente de exemplo para:

> [!div class="checklist"]
> - Crie uma conta de armazenamento e um contêiner para limpar dados residam.
> - Crie uma máquina Virtual de ciência de dados (DSVM) do Ubuntu no portal do Azure.
> - Implante os serviços do Azure Machine Learning no Azure para criar e treinar modelos.
> - Crie contas de serviços do Azure Machine Learning.
> - Implantar e usar o registro de contêiner do Azure.
> - Implante um cluster Kubernetes no Azure Stack.
> - Crie uma conta de armazenamento do Azure Stack e a fila de armazenamento para os dados.
> - Crie uma função nova pilha do Azure para mover os dados limpos da pilha do Azure para o Azure.

**Quando usar essa solução**

 -  Sua organização estiver usando uma abordagem de DevOps ou tem uma planejada para um futuro próximo.
 -  Você deseja implementar práticas de CI/CD na sua implementação do Azure Stack e na nuvem pública.
 -  Você deseja consolidar o pipeline de CI/CD em ambientes de nuvem e locais.
 -  Você deseja que a capacidade de desenvolver aplicativos usando os serviços de nuvem ou local.
 -  Você deseja aproveitar as habilidades de desenvolvedor consistente entre aplicativos de nuvem e locais.

> [!Tip]  
> ![pillars.png híbrido](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> O Microsoft Azure Stack é uma extensão do Azure. O Azure Stack traz a agilidade e inovação da computação em nuvem ao seu ambiente local e habilitando a única nuvem híbrida que permite que você crie e implante aplicativos híbridos de qualquer lugar.  
> 
> O white paper [considerações sobre Design para aplicativos híbridos](https://aka.ms/hybrid-cloud-applications-pillars) analisa os pilares da qualidade de software (posicionamento, escalabilidade, disponibilidade, resiliência, capacidade de gerenciamento e segurança) para projetar, implantar e operar aplicativos híbridos. As considerações de design ajudar a otimizar o design do aplicativo híbrido, minimizando desafios em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

Alguns componentes são necessários para compilar esse caso de uso e podem levar algum tempo para se preparar:

 -  Um parceiro de OEM/Hardware do Azure podem implantar uma pilha do Azure de produção e todos os usuários podem implantar uma ASDK

 -  Um operador de pilha do Azure deve também implantar o serviço de aplicativo, criar planos e ofertas, criar uma assinatura de locatário e adicionar a imagem do Windows Server 2016

 -  Um serviço de aplicativo e de rede híbrida configuração é necessária (Saiba mais sobre [integração de aplicativos com redes virtuais.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Particular [versão do agente e Build](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) para integração do VSTS deve ser configurada antes da implantação (Verifique se todos os componentes existentes usados atender aos requisitos antes de começar.)

Conhecimento prévio do Azure e o Azure Stack é necessário. Para saber mais antes de prosseguir, comece com estes tópicos:

 -  [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Principais conceitos do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Guia de solução de CI/CD de híbrido do Azure Stack](/azure/azure-stack/user/azure-stack-solution-pipeline)

**As tabelas**

 -  Uma assinatura do Azure (criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  Uma nova URL do aplicativo Web criado por [aplicativo Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) no Azure

 -  Implantação de [serviços de contêiner do Azure (ACS) do Kubernetes no Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Implantação do serviço de Azure Machine Learning (versão prévia) [tutorial parte 4](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Experimentação do Azure Machine Learning [conta](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Um sistema integrado do Azure Stack ou implantação do Kit de desenvolvimento do Azure Stack.

    - Você encontrar instruções para instalar o Azure Stack no [instalar o Kit de desenvolvimento do Azure Stack](/articles/azure-stack/asdk/asdk-install).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Essa instalação pode exigir algumas horas para ser concluído.

 -  Implantação do [serviço de aplicativo](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) os serviços de PaaS para o Azure Stack

 -  Um [plano/ofertas](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dentro do ambiente do Azure Stack

 -  Um [assinatura de locatário](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dentro do ambiente do Azure Stack

 -  Uma imagem de VM do Ubuntu Server (disponível no [Marketplace do Azure Stack](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) esta VM será criado na assinatura do locatário no Azure Stack como o agente de compilação particular, bem como as VMs de Kubernetes. Se essa imagem não estiver disponível, o operador de pilha do Azure pode ajudar, para garantir que isso é adicionado ao ambiente. (Não use a compilação 18.04 do ubuntu, pois ele não é suportado atualmente.)

 -  Um aplicativo Web na assinatura do locatário (Observe a nova URL do aplicativo Web para uso posterior.)

 -  Implantação de baseado em Linux do VSTS privada Criar agente de máquina Virtual, dentro da assinatura de locatário

 -  Implantação de um [serviços de contêiner do Azure (ACS) do Kubernetes no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Ferramentas de desenvolvedor**

 -  [Espaço de trabalho do VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (o processo de inscrição criará um projeto chamado "MyFirstProject")

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) instalação e [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) logon

 -  [Clone local](https://www.visualstudio.com/docs/git/gitquickstart) do projeto

 -  [Subsistema do Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) instalação (para BASH e SSH)

 -  [Docker para Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) instalação

 -  [O Azure Machine Learning Workbench (visualização)](https://aka.ms/azureml-wb-msi) instalação

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) instalação de ambiente

**VSTS**

 -  **Conta do VSTS.** Configure rapidamente a integração contínua para compilação, teste e implantação. Para obter mais informações sobre contas do VSTS, consulte [criar a conta do VSTS](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **Repositório de código.** Usando repositórios de código existente no GitHub, BitBucket, DropBox, Onedrive e TFS, a plataforma do VSTS pode aproveitar vários repositórios de código para simplificar o pipeline de desenvolvimento. Para obter mais informações sobre o código consulte repositórios [Introdução ao Git e VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) tutorial.

 -  **Conexão de serviço.** Conecte-se aos serviços externos e remotos para executar tarefas para teste, compilação ou implantação. Para obter mais informações sobre o VSTS Consulte conexões de serviço [pontos de extremidade de serviço de compilação e versão](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Definições de compilação.** Definir processos de compilação automatizada e compor um conjunto de tarefas por meio do catálogo de tarefas. Para obter mais informações sobre compilação consulte definições [criar uma definição de compilação](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) documentação.

 -  **Definições de versão.** Defina o processo de implantação de uma coleção de ambientes em que os artefatos de aplicativo são implantados. Para obter mais informações sobre versão consulte definições [criar uma definição de versão](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) documentação.

 -  **Pool de agentes de Build do Linux hospedada do VSTS.** Criar, testar e implantar rapidamente aplicativos usar um Microsoft gerenciadas e mantidas agente hospedado. Para obter mais informações sobre a compilação do VSTS hospedado agentes veem [agentes hospedados](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) documentação.

## <a name="step-1-create-a-storage-account"></a>Etapa 1: criar uma conta de armazenamento

Crie uma conta de armazenamento e um contêiner para limpar dados residam.

1.  Entrar para o [ *portal do Azure*](https://portal.azure.com/).

2.  No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **todos os serviços**. Role para baixo até **armazenamento** e escolha **contas de armazenamento**. No * * as contas de armazenamento * * janela escolher **adicionar**.

3.  Insira um nome para a conta de armazenamento.

    > [!Note]  
    > Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. O nome da conta de armazenamento deve ser exclusivo dentro do Azure. O portal do Azure indicará se o nome da conta de armazenamento selecionado já está em uso.

4.  Especifique o modelo de implantação a ser usado: **Resource Manager**.

5.  Selecione o tipo de conta de armazenamento: **uso geral V1**, em seguida, especifique o nível de desempenho: **padrão**.

6.  Selecione a opção de replicação para a conta de armazenamento: **GRS**.

7.  Selecione a nova assinatura da conta de armazenamento.

8.  Especifique um novo grupo de recursos ou selecione um grupo de recursos existente.

9.  Selecione a localização geográfica da conta de armazenamento.

10. Selecione **Criar** para criar a conta de armazenamento.

    ![Alt text](\media\azure-stack-solution-machine-learning\image1.png)

11.  Escolha a conta de armazenamento criada recentemente.

12.  Selecione na **Blobs**.

    ![Alt text](media\azure-stack-solution-machine-learning\image2.png)

13.  Selecione na **+ contêiner** e selecione **contêiner**.

    ![Alt text](media\azure-stack-solution-machine-learning\image3.png)

14.  Forneça o nome para o contêiner **uploadeddata** e escolha o tipo de acesso **contêiner**.

15.  Selecione na **criar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Etapa 2: Criar uma máquina de Virtual de ciência de dados

Crie uma máquina Virtual de ciência de dados (DSVM) do Ubuntu no portal do Azure.

1.  Faça logon no portal do Azure de [https://portal.azure.com](https://portal.azure.com/)

2.  Selecione o **+ novo** link e pesquise por "dados de ciências da máquina Virtual para Linux Ubuntu CSP

    ![Alt text](media\azure-stack-solution-machine-learning\image5.png)

1.  Escolher **máquina de Virtual de ciência de dados para Linux (Ubuntu)** na lista e siga na tela instruções para criar a DSVM.

    ![Alt text](media\azure-stack-solution-machine-learning\image6.png)

> ! [Importante]  
> **Escolher** senha * * como o*tipo de autenticação*.

Coloque a DSVM de novo no mesmo grupo de recursos como a conta de armazenamento recém-criada. Todos os objetos de ML de borda são implantados no Azure por este grupo de recursos.

1.  Nos recursos opcionais de configurações

    a.  Selecione o **conta de armazenamento** criado anteriormente.

    b.  Criar um novo **rede Virtual**, **sub-rede**, e **IP público** ela deve por padrão criam um nome com base no nome do grupo de recursos.

    c.  Criar um novo **NSG** ele deve criar isso automaticamente com as regras corretas já aplicadas.

    d.  Para o **conta de armazenamento de diagnóstico**, selecione a conta de armazenamento criada anteriormente.

    e.  Observação: Com o AAD habilitado e configurado para a assinatura do Azure, identidades gerenciadas para recursos do Azure podem ser habilitadas também.

2.  Selecione **OK**.

### <a name="connect-to-the-dsvm"></a>Conectar-se à DSVM

Quando o DSVM tiver sido criado, conecte-se à VM do subsistema do Windows para Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Digite Sim quando solicitado a confirmar a conexão de VM.

2.  Insira a senha que criou para o DSVM.

### <a name="update-the-dsvm"></a>Atualizar o DSVM 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Etapa 3: Implantar serviços de aprendizado de máquina do Azure

Implante o Azure Machine Learning de serviços do Azure.

Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta. Eles ajudam os cientistas de dados profissionais a preparar dados, desenvolver experimentos e implantar modelos em escala de nuvem.

Esta seção explica:

> [!div class="checklist"]
> - Criar contas de serviço para serviços do Azure Machine Learning
> - Instalar e fazer logon no Azure Machine Learning Workbench.
> - Criar um projeto no Workbench
> - Executar um script nesse projeto
> - Acessar a interface de linha de comando (CLI)

Como parte do portfólio do Microsoft Azure, os serviços do Azure Machine Learning exigem uma assinatura do Azure. Para obter uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

As permissões adequadas são necessárias para criar ativos, como grupos de recursos, máquinas virtuais e outros ativos.

O aplicativo do Azure Machine Learning Workbench pode ser instalado nos seguintes sistemas operacionais:

 -  Windows 10 ou Windows Server 2016
 -  macOS Sierra ou High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Etapa 4: Criar serviços do Azure Machine Learning

Crie contas de serviços do Azure Machine Learning.

Use o portal do Azure para provisionar as contas do Azure Machine Learning:

1.  Entrar para o [portal do Azure](https://portal.azure.com/) usando as credenciais para a assinatura do Azure a ser usado. Para obter uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Alt text](media\azure-stack-solution-machine-learning\image7.png)

1.  Selecione o botão **Criar um recurso** (+) no canto superior esquerdo do portal.

    ![Criar um recurso no Portal do Azure](media\azure-stack-solution-machine-learning\image8.png)

1.  Digite **Machine Learning** na barra de pesquisa. Selecione o resultado de pesquisa denominado **Experimentação do Machine Learning (versão prévia)**.

    ![Pesquisa do Azure Machine Learning](media\azure-stack-solution-machine-learning\image9.png)

1.  No **experimentação do Machine Learning** painel, role para baixo e selecione **criar** para começar a definir a conta de experimentação.

    ![Azure Machine Learning – Criar uma conta de experimentação](media\azure-stack-solution-machine-learning\image10.png)

1.  No **experimentação do ML** painel Configurar a conta de experimentação do Machine Learning.

    | Configuração | Valor sugerido para o tutorial | DESCRIÇÃO |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome da conta de experimentação | Nome exclusivo | Insira um nome exclusivo que identifica a conta. Use um nome de departamento ou projeto que melhor identifique o experimento. O nome deve ter entre 2 e 32 caracteres. Ele deve conter apenas caracteres alfanuméricos e traços (-). |
    | Assinatura | A assinatura | Escolha a assinatura do Azure a ser usado para o teste. Se existirem várias assinaturas, escolha a assinatura apropriada, em que o recurso é cobrado. |
    | Grupo de recursos | O grupo de recursos | Usar um grupo de recursos existente na assinatura, ou insira um nome para criar um novo grupo de recursos para esta conta de experimentação. |
    | Local padrão | A região mais próxima aos usuários | Escolha o local mais próximo para os usuários e os recursos de dados. |
    | Número de estações | 2 | Insira o número de estações. Saiba como [estações afetam o preço](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Para este início rápido, apenas duas estações são necessárias. Estações podem ser adicionadas ou removidas conforme o necessário no Portal do Azure. |
    | Conta de armazenamento | Nome exclusivo | Selecione **Criar nova** e forneça um nome para criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). O nome deve ter de 3 a 24 caracteres e deve incluir somente caracteres alfanuméricos. Como alternativa, selecione **usar existente** e selecione a conta de armazenamento existente na lista. A conta de armazenamento é necessária e é usada para conter os artefatos de projeto e dados do histórico de execuções. |
    | Espaço de trabalho para a conta de Experimentação | IrisGarden<br>(o nome é usado em tutoriais) | Forneça um nome para um espaço de trabalho dessa conta. O nome deve ter entre 2 e 32 caracteres. Ele deve conter apenas caracteres alfanuméricos e traços (-). Este espaço de trabalho contém as ferramentas necessárias para criar, gerenciar e publicar os experimentos. |
    | Atribuir um proprietário para o espaço de trabalho | A conta | Selecione a própria conta como o proprietário do espaço de trabalho. |
    | Criar conta de Gerenciamento de Modelos | **verificar** | Criar uma conta de Gerenciamento de Modelos. Isso será usado para implantar e gerenciar os modelos como serviços da web em tempo real. <br><br>Embora opcional, criar a conta de gerenciamento de modelos ao mesmo tempo em que a conta de experimentação é recomendado. |
    | Nome da conta | Nome exclusivo | Escolha um nome exclusivo que identifica a conta de gerenciamento de modelos. Use o departamentais ou o nome do projeto que melhor identifique o experimento. O nome deve ter entre 2 e 32 caracteres. Ele deve conter apenas caracteres alfanuméricos e traços (-). |
    | Tipo de preço do Gerenciamento de Modelos | **DEVTEST** | Selecione **nenhum tipo de preço selecionado** para especificar o tipo de preço para a nova conta de gerenciamento de modelos. Para economizar nos custos, selecione o tipo de preço de desenvolvimento/teste se ele estiver disponível na assinatura (disponibilidade limitada). Caso contrário, selecione o tipo de preço S1. Escolha Selecionar para salvar a seleção de camada de preços. |
    | Fixar no painel | verificação | Selecione o **fixar no painel** opção para permitir o acompanhamento fácil da conta de experimentação do Machine Learning na página do painel frontal do portal do Azure. |

    ![Configuração da conta de Experimentação do Machine Learning](media\azure-stack-solution-machine-learning\image11.png)

1.  Selecione **Criar** para iniciar o processo de criação da conta de Experimentação junto com a conta de Gerenciamento de Modelos.

    ![Configuração da conta de Experimentação do Machine Learning](media\azure-stack-solution-machine-learning\image12.png)

    Pode levar alguns minutos para criar uma conta. Verifique o status do processo de implantação, selecionando o ícone de notificações (sino) na barra de ferramentas de portal do Azure.

    ![Notificações do Portal do Azure](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Instalar e fazer logon no workbench 

O Azure Machine Learning Workbench está disponível para Windows ou macOS. Consulte a lista de [plataformas com suporte](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

**Aviso:** a instalação pode levar em torno de uma hora para ser concluída.

1.  Baixe e inicie o instalador mais recente do Workbench.

    > [!Important]  
    > Baixe o instalador totalmente em disco, depois inicie-o a partir desse mesmo disco. Não executá-lo diretamente do widget de download do navegador.<br>**No Windows:<br>**  um. Baixar [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. Clique duas vezes no instalador baixado no Explorador de Arquivos.

1.  Siga na tela instruções do instalador para conclusão.

    * * A instalação pode levar até 30 minutos para ser concluída. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    O instalador baixará e configurar todas as dependências necessárias, como Python, Miniconda e outras bibliotecas relacionadas. A instalação também inclui a ferramenta de linha de comando de plataforma cruzada do Azure ou CLI do Azure.

1.  Inicie o Workbench selecionando o botão **Iniciar Workbench** na última tela do instalador.

    Se o instalador for fechado, inicie usando o **Machine Learning Workbench** atalho da área de trabalho.

1.  Selecione **entrar com a Microsoft** para autenticar com o Azure Machine Learning Workbench. Use as mesmas credenciais usadas no portal do Azure para criar as contas de experimentação e gerenciamento de modelos.

    Depois de conectado, o Workbench usa a primeira conta de experimentação, encontra-se nas assinaturas do Azure e exibe todos os espaços de trabalho e projetos associados a essa conta.

    > [!Tip]  
    > Alternar para outra conta de experimentação usando o ícone no canto inferior esquerdo da janela do aplicativo Workbench.

### <a name="create-a-new-project-in-workbench"></a>Criar um novo projeto no workbench

1.  Abra o aplicativo do Azure Machine Learning Workbench e entre, se necessário.

    - No Windows, inicie usando o **Machine Learning Workbench** atalho da área de trabalho.

    - No macOS, selecione **Azure ML Workbench** na Launchpad.

1.  Selecione o sinal de adição (+) no painel **PROJETOS**, e selecione **Novo Projeto**.

    ![Novo espaço de trabalho](media\azure-stack-solution-machine-learning\image14.png)

1.  Preencha os campos de formulário e selecione o botão **Criar** para criar um novo projeto no Workbench.

    | Campo | Valor sugerido para o tutorial | DESCRIÇÃO |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome do projeto | myIris | Insira um nome exclusivo que identifica a conta. Use o departamentais ou o nome do projeto que melhor identifique o experimento. O nome deve ter entre 2 e 32 caracteres. Ele deve conter apenas caracteres alfanuméricos e traços (-). |
    | Diretório do projeto | c:\Temp\ | Especifique o diretório no qual o projeto é criado. |
    | Descrição do projeto | deixar em branco | Campo opcional útil para descrever os projetos. |
    | URL do repositório GIT VisualStudio.com | deixar em branco | Campo opcional. Associe um projeto com um repositório Git no Visual Studio Team Services para controle do código-fonte e colaboração. [Saiba como configurar um repositório](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Espaço de trabalho selecionado | IrisGarden (se houver) | Escolha um espaço de trabalho criado para a conta de experimentação no portal do Azure. <br>Usando o guia de início rápido, o espaço de trabalho pelo nome do IrisGarden está listado. Caso contrário, use o espaço de trabalho com o nome da conta de experimentação, ou um nome de conta preferencial. |
    | Modelo do projeto | Classificando a Íris | Modelos contêm scripts e os dados usados para explorar o produto. Este modelo contém os scripts e os dados necessários para este início rápido e outros tutoriais neste site de documentação. |

    ![Novo Projeto](media\azure-stack-solution-machine-learning\image15.png)

1.  Um novo projeto é criado e o seu painel é aberto com esse projeto. Explore a home page do projeto, fontes de dados, os blocos de anotações e arquivos de código-fonte.

    ![Abrir projeto](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Anexar um destino de computação da DSVM

Depois de criar a DSVM, anexá-lo ao projeto do Azure ML.

1.  De dentro do aplicativo do Azure ML Workbench, inicie a CLI do Azure ML Workbench selecionando **arquivo**->**abra o PowerShell**

    ![Alt text](media\azure-stack-solution-machine-learning\image17.png)

1.  Depois que o PowerShell prompt abriu use o seguinte comando:

    ```PowerShell  
        az login
    ```

1.  Receba a seguinte solicitação:

     ![Alt text](media\azure-stack-solution-machine-learning\image18.png)

1.  Navegue até o site conforme detalhado no prompt e insira o código que é fornecido.

    ![Alt text](media\azure-stack-solution-machine-learning\image19.png)

1.  Selecione continuar quando solicitado, e selecione a conta do Azure que na conta Experimental de ML do Azure está associada.

    ![Alt text](media\azure-stack-solution-machine-learning\image20.png)

1.  A CLI do Azure ML Workbench, em seguida, enviará o prompt a seguir:

    ![Alt text](media\azure-stack-solution-machine-learning\image21.png)

1.  Quando o logon de conta de ML e o espaço de trabalho é mostrado como bem-sucedido, anexe a DSVM.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    A seguinte notificação será exibida:

    ![Alt text](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Esse processo levará algum tempo e gerará uma quantidade significativa de texto que puxa várias imagens do docker, registra-os e, em seguida, aplica-se o código necessário e os aplicativos para eles.

Agora podem ser executar experimentos nessa dsvm.

### <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados

Em seguida, inicie a preparação dos dados no Azure Machine Learning Workbench. Cada transformação realizada no Workbench é armazenada em um formato JSON em um pacote de preparação de dados local (\*arquivo. dprep). Este pacote de preparação de dados é o contêiner primário para o trabalho de preparação de dados no Workbench.

Este pacote de preparação de dados poderá ser entregue posteriormente a um tempo de execução, como local-C\#/CoreCLR, Scala/Spark ou Scala/HDI.

1.  Selecione o ícone de pasta para abrir o modo de exibição Arquivos e selecione **iris.csv** para abrir esse arquivo.

    O arquivo contém uma tabela com 5 colunas e 50 linhas. Quatro colunas são colunas de recursos numéricos. A quinta coluna é uma coluna de cadeia de caracteres de destino. Nenhuma das colunas tem nome de cabeçalho.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  Na **Exibição de dados**, selecione o sinal de adição (**+**) para adicionar uma nova fonte de dados. A página **Adicionar fonte de dados** será aberta.

    ![Modo de exibição de dados no Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image24.png)

1.  Selecione **arquivos de texto (\*. csv, \*. JSON, \*. txt.,...)** .

    ![Fonte de dados no Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image25.png)

1.  Selecione **Avançar**.

2.  Navegue até o arquivo **Iris**e selecione **concluir**. Ele usará os valores padrão para parâmetros como os tipos de separador e dados.

    > [!Important]  
    > Selecione o **Iris** de arquivos de dentro do diretório do projeto atual para este exercício. Caso contrário, as etapas posteriores podem falhar.

    ![Selecionar íris](media\azure-stack-solution-machine-learning\image26.png)

1.  Um novo arquivo chamado `*iris-1.dsource` é criado. O arquivo é nomeado exclusivamente `-1` porque o projeto de exemplo já é fornecido com um **dsource** arquivo.

    O arquivo é aberto e os dados são mostrados. Uma série de cabeçalhos de coluna, de **Column1** à **Column5**, é adicionado automaticamente a esse conjunto de dados. Role para baixo e observe que a última linha do conjunto de dados está vazia. A linha está vazia por causa da quebra de linha extras no arquivo CSV.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image27.png)

1.  Selecione o botão **Métricas**. Histogramas são gerados e exibidos.

    Alterne de volta para o modo de exibição de dados, selecionando o **dados** botão.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image28.png)

1.  Observe os histogramas. Um conjunto completo de estatísticas foi calculado para cada coluna.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image29.png)

1.  Comece a criar um pacote de preparação de dados selecionando o botão **Preparar**. A caixa de diálogo **Preparar** é aberta.

    O projeto de exemplo contém um **dprep** arquivo de preparação de dados que é selecionado por padrão.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image30.png)

1.  Criar um novo pacote de preparação de dados, selecionando **+ novo pacote de preparação de dados** no menu.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image31.png)

1.  Insira um novo valor para o nome do pacote (use **iris-1**) e selecione **OK**.

    Um novo pacote de preparação de dados denominado **iris-1.dprep** é criado e aberto no editor de preparação de dados.

    ![Exibição de dados de íris](media\azure-stack-solution-machine-learning\image32.png)

    Em seguida, a preparação de dados é necessária.

1.  Selecione cada cabeçalho de coluna para tornar o texto do cabeçalho editável. Em seguida, renomeie cada coluna da seguinte maneira:

    Em ordem, insira **comprimento da sépala**, **largura da sépala**, **comprimento da pétala**, **largura da pétala**, e **espécies** para as cinco colunas respectivamente.

    ![Renomeie as colunas](media\azure-stack-solution-machine-learning\image33.png)

1.  Conte valores distintos:

    1.  Selecione a coluna **Espécies**

    2.  Clique com o botão direito do mouse para selecioná-la.

    3.  Selecione **contagens de valor** no menu.

        O painel **Inspetores** é aberto abaixo dos dados. Um histograma com quatro barras é exibido. A coluna de destino possui quatro valores distintos: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**e um **(null)** valor.

    ![Selecione Contagens de valor](media\azure-stack-solution-machine-learning\image34.png)

    ![Histograma de contagem de valor](media\azure-stack-solution-machine-learning\image35.png)

1.  Para filtrar os valores nulos, selecione a barra "(nulo)" e selecione o sinal de subtração (**-**).

    Em seguida, a linha (nulo) fica cinza para indicar que ela foi filtrada.

    ![Filtrar valores nulos](media\azure-stack-solution-machine-learning\image36.png)

1.  Observe as etapas de preparação de dados individuais que são detalhadas no painel **ETAPAS**. Como as colunas são renomeadas e as linhas de valor nulo são filtradas, cada ação é gravada como uma etapa de preparação de dados. Edite as etapas individuais para ajustar suas configurações, reorganizar as etapas e remover etapas.

    ![Alt text](media\azure-stack-solution-machine-learning\image37.png)

1.  Feche o editor de preparação dos dados. Selecione o ícone **x** na guia **iris-1** com o ícone de grafo para fechar a guia. O trabalho é salvo automaticamente na **iris-1.dprep** arquivo mostrado sob a **preparações de dados** título.

    ![Feche](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Gerar código de Python para invocar um pacote de preparação de dados

A saída de um pacote de preparação de dados pode ser explorada diretamente no Python ou em um Bloco de anotações do Jupyter. Os pacotes podem ser executados em vários tempos de execução, incluindo o Python, o Spark (inclusive no Docker) e HDInsight locais.

1.  Localize o arquivo **iris-1.dprep** na guia de Preparação de Dados.

2.  Clique com o botão direito do mouse no arquivo **iris-1.dprep** e selecione **Gerar Arquivo de Código de Acesso a Dados** no menu de contexto.

    ![Gerar código](media\azure-stack-solution-machine-learning\image39.png)

    Um novo arquivo chamado **Iris-1.py** abre com as seguintes linhas de código para invocar a lógica criada como um pacote de preparação de dados:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    Dependendo do contexto no qual esse código é executado, drep representa um tipo de DataFrame diferente:

    -  Ao executar em um tempo de execução do Python, um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) é usado.

    -  Ao executar em um contexto do Spark, é usado um [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html).

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Examine os arquivos de configuração e iris_sklearn.py

1.  No projeto aberto, selecione o **arquivos** botão (o ícone de pasta) no painel à esquerda para abrir a lista de arquivos na pasta do projeto.

    ![Abra o projeto do Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image40.png)

1.  Selecione o arquivo de script **iris_sklearn.py** do Python.

    ![Escolher um script](media\azure-stack-solution-machine-learning\image41.png)

    O código abre em uma nova guia do editor de texto dentro do Workbench.

    > [!Note]  
    > O código exibido pode não ser exatamente o mesmo que o código anterior porque este projeto de exemplo é atualizado com frequência.

    ![Abra o arquivo](media\azure-stack-solution-machine-learning\image42.png)

1.  Inspecione o código de script do Python para se familiarizar com o estilo de codificação.

    O script **iris_sklearn.py** executa as seguintes tarefas:  

    -  Carrega o pacote de preparação de dados padrão chamado **iris.dprep** para criar um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Adiciona recursos aleatórios para tornar o problema mais difícil de resolver. A aleatoriedade é necessária porque o Iris é um pequeno conjunto de dados que pode ser facilmente classificado com quase 100% de precisão.

    -  Usa a biblioteca de aprendizado thescikit learnmachine para criar um modelo de regressão logística. Essa biblioteca é fornecida com o Azure Machine Learning Workbench por padrão.

    -  Serializa o modelo usando a biblioteca [pickle](https://docs.python.org/3/library/pickle.html) em um arquivo na pasta `outputs`.

    -  Carrega o modelo serializado e, em seguida, desserializa-o de volta na memória.

    -  Usa o modelo desserializado para fazer a previsão em um novo registro.

    -  Plota dois grafos, uma matriz de confusão e um receptor multiclasse operacionais curva característica (Cco), usando o [matplotlib](https://matplotlib.org/) biblioteca e, em seguida, salva-os em theoutputsfolder. Instale essa biblioteca no ambiente se não aparente.

    -  Plota a precisão de modelo e de taxa de regularização no histórico de execuções automaticamente. O objeto `run_logger` é usado ao longo do processo para registrar a taxa de regularização e para precisão do modelo nos logs.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Executar iris_sklearn. py no ambiente local

1.  Inicie a interface de linha de comando (CLI) do Azure Machine Learning:

    1.  Inicialize o Azure Machine Learning Workbench.

    2.  No menu de Workbench, selecione **Arquivo**> **Abrir Prompt de Comando**.

    A janela de interface de linha de comando (CLI) do Azure Machine Learning inicia na pasta do projeto `C:\Temp\\myIris\` no Windows. Este projeto é o mesmo que aquele criado na parte 1 do tutorial.

    > [!Important]  
    > Use essa janela da CLI para realizar as próximas etapas.

1.  Na janela da CLI, instale a biblioteca de plotagem do Python **matplotlib**, se ainda não estiver instalado.

    O script **iris_sklearn.py** tem dependências em dois pacotes do Python: **scikit-learn** e **matplotlib**. O **scikit-Saiba** pacote é instalado pelo Azure Machine Learning Workbench para a conveniência. Ainda assim, a instalação do **matplotlib** podem ser necessárias.

    Se continuar sem instalar **matplotlib**, o código neste tutorial ainda pode ser executado com êxito. Contudo, o código não conseguirá produzir os gráficos da saída de matriz de confusão e da curva CCO multiclasse mostrados nas visualizações de histórico.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    A instalação leva cerca de um minuto.

1.  Retorne para a janela do aplicativo Workbench.

2.  Localize a guia chamada **iris_sklearn.py**.

    ![Localize a guia com script](media\azure-stack-solution-machine-learning\image43.png)

1.  Na barra de ferramentas da guia, selecione **local** como o ambiente de execução andiris_sklearn.pyas o script seja executado. Eles já podem estar selecionados.

    ![Alt text](media\azure-stack-solution-machine-learning\image44.png)

1.  Mover para o lado direito da barra de ferramentas e enter0.01in a **argumentos** campo.

    Esse valor corresponde à taxa de regularização do modelo de regressão logística.

    ![Local e a opção de script](media\azure-stack-solution-machine-learning\image45.png)

1.  Selecione o botão **Executar**. Um trabalho é agendado imediatamente. O trabalho é listado no painel **Trabalhos** no lado direito da janela do workbench.

    ![Local e a opção de script](media\azure-stack-solution-machine-learning\image46.png)

    Após alguns instantes, o status do trabalho muda de **enviando**, para **executando**e, finalmente, para **concluído**.

1.  Selecione **Concluído** no texto de status do trabalho no painel **Trabalhos**.

    ![Executar sklearn](media\azure-stack-solution-machine-learning\image47.png)

    Uma janela pop-up é aberta e exibe o texto de saída padrão (stdout) para a execução. Para fechar o texto stdout, selecione a **feche** (**x**) botão no canto superior direito da janela pop-up.

    ![Saída padrão](media\azure-stack-solution-machine-learning\image48.png)

1.  No mesmo status de trabalho na **trabalhos** painel, selecione o texto azul **iris_sklearn.py \[n\] **(* n * é o número da execução) imediatamente acima do  **Concluído** status e a hora de início. A janela **Propriedades de Execução** é aberta e mostra as seguintes informações para aquela execução específica:

    -  Informações sobre **Propriedades da Execução**

    -  **Saídas**

    -  **Métricas**

    -  **Visualizações**, se houver

    -  **Logs**

    Quando a execução é concluída, a janela pop-up mostra os seguintes resultados:

    > [!Note]  
    > Como o tutorial introduz algum caráter aleatório ao conjunto de treinamento definido anteriormente, os resultados exatos podem variar dos resultados mostrados aqui.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Feche a guia **Propriedades da Execução** e retorne para a guia **iris_sklearn.py**.

1.  Repita para execuções adicionais.

Insira uma série de valores diferentes no campo **Argumentos**, desde `0.001` até `10`. Clique em **Executar** para executar o código mais algumas vezes. O valor do argumento alterado cada vez será alimentado o modelo de regressão logística no código, resultando em descobertas diferentes a cada vez.

### <a name="review-the-run-history-in-detail"></a>Examinar o histórico de execuções com detalhes

No Azure Machine Learning Workbench, cada execução do script é capturada como um registro de histórico de execução. Exibir o histórico de execução de um script específico abrindo a **execuções** modo de exibição.

1.  Para abrir a lista de **Execuções**, selecione o botão **Execuções** (ícone de relógio) na barra de ferramentas à esquerda. Em seguida, selecione **iris_sklearn. py** para mostrar a **painel de execução** ofiris_sklearn.py.

    ![Modo de execução](media\azure-stack-solution-machine-learning\image49.png)

1.  A guia **Painel de Execução** se abrirá.

    Examine as estatísticas capturadas em várias execuções. Os grafo são renderizados na parte superior da guia. Cada execução tem um número consecutivo, e os detalhes de execução são listados na tabela na parte inferior da tela.

    ![Painel de execução](media\azure-stack-solution-machine-learning\image50.png)

1.  Filtre a tabela e, em seguida, selecione qualquer um dos grafos para exibir o status, a duração, a precisão e a taxa de regularização de cada execução.

2.  Marque as caixas de seleção ao lado de duas ou mais execuções na tabela **Execuções**. Selecione o botão **Comparar** para abrir um painel de comparação detalhada. Examine a comparação lado a lado.

3.  Para voltar ao **Painel de Execução**, selecione o botão de voltar da **Lista de Execuções**, no canto superior esquerdo do painel **Comparação**.

    ![Retornar à Lista de Execuções](media\azure-stack-solution-machine-learning\image51.png)

1.  Selecione uma execução individual para ver a exibição de detalhes de execução. Observe que as estatísticas para a execução selecionada são listadas na seção **Propriedades da Execução**. Os arquivos gravados na pasta de saída são listados na **saídas** seção e baixar os arquivos a partir daí.

    ![Detalhes da execução](media\azure-stack-solution-machine-learning\image52.png)

Os dois gráficos, a matriz de confusão e a curva CCO multiclasse são renderizados na seção **Visualizações**. Todos os arquivos de log também podem ser encontrados na seção **Logs**.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Executar scripts na janela da CLI do Azure Machine Learning (ML) Workbench

1.  Inicie a interface de linha de comando (CLI) do Azure Machine Learning:

    1.  Inicialize o Azure Machine Learning Workbench.

    2.  No menu de Workbench, selecione **Arquivo** > **Abrir Prompt de Comando**.

    O inicia prompt da CLI na pasta do projeto `C:\\Temp\\myIris` no Windows. Esse é o projeto criado na parte 1 do tutorial.

    > [!Important]  
    > Use essa janela da CLI para realizar as próximas etapas.

1.  Na janela da CLI, faça logon no Azure. [Saiba mais sobre o az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Ignore esta etapa se estiver conectado.

1.  No prompt de comando, insira:

    ```CLI
        az login
    ```

    Esse comando retorna um código para usar no navegador em [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Vá para [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) no navegador. Insira o código que você recebeu do recebido na CLI.

    O aplicativo Workbench e a CLI usam caches de credenciais independentes ao autenticar para recursos do Azure. Autenticação não é necessária novamente até que o token em cache expire.

1.  Se a organização tiver várias assinaturas do Azure (ambiente corporativo), defina a assinatura a ser usado. Localize a assinatura, defina-o usando a ID da assinatura e, em seguida, testá-lo.

1.  Liste cada assinatura do Azure acessada usando este comando:

    ```CLI
        az account list -o table 
    ```

    O **lista de contas az** comando retorna a lista de assinaturas disponíveis para o logon. Se houver mais de uma, identifique o valor de ID de assinatura para a assinatura usada.

1.  Defina a assinatura do Azure usada como a conta padrão:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Onde < o-subscription-id > é o valor da ID da assinatura usada. Não inclua os colchetes.

1.  Confirme a nova configuração da assinatura solicitando os detalhes da assinatura atual.

    ```CLI
        az account show
    ```

1.  Na janela da CLI, envie o script **iris_sklearn.py** como um experimento.

    A execução do iris_sklearn. py é executada no contexto de computação local.

1.  No Windows:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  No macOS:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    A saída deve ser semelhante a: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Examine a saída. Todos devem ser a mesma saída e os resultados como quando o Workbench executa o script.

7.  Volte para o Workbench e:

    Selecione o ícone de pasta no painel esquerdo para listar os arquivos de projeto.  Abra o script do Python chamado **run.py**. Esse script é útil para fazer loop sobre várias taxas de regularização. 

    ![Retorne à lista de execuções](media\azure-stack-solution-machine-learning\image53.png)

1.  Execute o experimento várias vezes com essas taxas.

    Esse script inicia` aniris_sklearn.pyjob` com uma taxa de regularização e s `10.0` (um número absurdamente grande). O script, em seguida, corta a taxa pela metade na execução seguinte e assim por diante, até que a taxa é menor do que `0.005`. O script contém o código a seguir:

    ![Retornar à Lista de Execuções](media\azure-stack-solution-machine-learning\image54.png)

1.  Execute o script **run.py** a partir da linha de comando conforme a seguir:

    ```CLI
        python run.py
    ```
Esse comando envia `iris_sklearn.py` várias vezes com taxas de regularização diferentes

Quando `run.py` for concluído, gráficos de métricas diferentes é exibidos na exibição de lista de histórico de execuções no workbench.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Executar scripts em uma baseada em Ubuntu dados ciência da máquina Virtual (DSVM) no Azure

Para executar o script em um contêiner do Docker em um computador Linux remoto, o acesso SSH (nome de usuário e senha) é necessária para executar o computador remoto. Além disso, o computador deve ter o mecanismo do Docker instalado e em execução.

1.  Editar gerado<your DSVM Name>.runconfigfile underaml_configand altere a estrutura do valuePySparktoPython padrão:

    ```yaml  
    Framework: Python
    ```
1.  Emita o comando mesmo de antes na janela da CLI, usando o destino*<DSVM>* neste momento para executar o iris_sklearn. py em um contêiner do Docker remoto: (substitua o <DSVM> com o nome de VM de ciência de dados, sem os colchetes).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

O comando é executado como se estivessem na adocker-pythonenvironment, exceto pelo fato da execução ocorre na VM Linux remota. A janela CLI exibe as mesmas informações de saída.

### <a name="download-the-model-pickle-file"></a>Baixe o arquivo de pickle do modelo

Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado localmente no Machine Learning Workbench. Essa ação serializou o modelo de regressão logística usando o popular pacote de serialização de objeto do Python [pickle](https://docs.python.org/3/library/pickle.html).

1.  Abra o aplicativo Machine Learning Workbench. Em seguida, abra o **myIris** projeto criado na parte anterior da série de tutoriais.

2.  Depois que o projeto estiver aberto, selecione o **arquivos** botão (ícone de pasta) no painel esquerdo para abrir a lista de arquivos na pasta do projeto.

3.  Selecione o arquivo **iris_sklearn.py**. O código Python abre em uma nova guia do editor de texto dentro do workbench.

4.  Examine o arquivo **iris_sklearn.py** para ver onde o arquivo pickle foi gerado. Selecione Control + F para abrir a caixa de diálogo **Localizar** e, em seguida, localize a palavra **pickle** no código Python.

Este snippet de código mostra como o arquivo de saída processado pelo pickle foi gerado. O arquivo pickle de saída é denominado **model.pkl** no disco.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Localize o arquivo de modelo pickle nos arquivos de saída de uma execução anterior.

    Quando o **iris_sklearn. py** script é executado, o arquivo de modelo é gravado para o **saídas** pasta com o nome **pkl**. Essa pasta existe no ambiente de execução escolhido para executar o script e não na pasta do projeto local. 

    1. Para localizar o arquivo, selecione a **execuções** botão (ícone de relógio) no painel esquerdo para abrir a lista de **todas as execuções**.  

    2. A guia **Todas as Execuções** será aberta. Na tabela de execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script era **iris_sklearn. py**.  

    3. O painel **Propriedades da Execução** será aberto. Na seção de canto superior direito do painel, observe os **saídas** seção. d\. Para baixar o arquivo pickle, selecione a caixa de seleção ao lado de **pkl** do arquivo e, em seguida, selecione **baixar**. Salve o arquivo na raiz da pasta do projeto. O arquivo é necessário nas etapas posteriores.  

    ![Baixe o arquivo de pickle](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Obter arquivos de esquema e de script de pontuação

Para implantar o serviço web junto com o arquivo de modelo, o script de pontuação é necessária. Opcionalmente, é necessário um esquema para os dados de entrada do serviço web. O script de pontuação carrega o arquivo **model.pkl** da pasta atual e usa-o para gerar novas previsões.

1.  Abra o aplicativo Machine Learning Workbench. Em seguida, abra o **myIris** projeto criado na parte anterior da série de tutoriais.

2.  Depois que o projeto estiver aberto, selecione o **arquivos** botão (ícone de pasta) no painel esquerdo para abrir a lista de arquivos na pasta do projeto.

3.  Selecione o arquivo **iris_score.py**. O script de Python é aberto. Esse arquivo é usado como o arquivo de pontuação.

    ![Arquivo de pontuação](media\azure-stack-solution-machine-learning\image56.png)

1.  Para obter o arquivo de esquema, execute o script. Selecione o ambiente **local** e o script **score_iris.py** na barra de comandos e, em seguida, selecione **Executar**.

    Esse script cria um arquivo JSON na **saídas** seção, que captura o esquema de dados de entrada exigido pelo modelo.

1.  Observe o painel **Trabalhos** no lado direito do **Painel do Projeto**. Aguarde até que o trabalho mais recente * * score_iris.py** exibir o verde **concluído** status. Em seguida, selecione o hiperlink **score_iris.py** da execução de trabalho mais recente para ver os detalhes da execução.

2.  Na página **Propriedades de Execução**, na seção **Saídas**, selecione o arquivo recém-criado **service_schema.json**. Marque a caixa de seleção ao lado do nome de arquivo e, em seguida, selecione **Baixar**. Salve o arquivo na pasta raiz do projeto.

3.  Retornar à guia anterior e o **score_iris** script. Ao usar a coleta de dados, entradas e previsões do serviço web do modelo podem ser capturadas. As etapas a seguir são de interesse para coleta de dados.

4.  Examine o código na parte superior do arquivo, o que importa a classe **ModelDataCollector**. Ele contém a funcionalidade de coleta de dados de modelo:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Examine as seguintes linhas de código na função **init()**, que instancia **ModelDataCollector**:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Examine as seguintes linhas de código na função **run(input_df)**, já que ela coleta dados de entrada e de previsão:

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

Agora, prepare o ambiente para operacionalizar o modelo.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Etapa 5: Implantar e usar o registro de contêiner do Azure

Implantar e usar o registro de contêiner do Azure.

Crie um Registro de Contêiner do Azure com o comando **az acr create**. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. O grupo de recursos é o mesmo.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Logon no registro de contêiner

Use o comando **az acr login** para fazer logon na instância do ACR. Forneça o nome exclusivo fornecido para o Registro de contêiner quando ele foi criado.

    ```CLI
        az acr login --name <acrName>
    ```

O comando retorna um ' logon bem-sucedido mensagem após a conclusão.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Preparar para operacionalizar localmente para desenvolvimento e teste o serviço

Use *modo local* implantação para ser executada em contêineres do Docker no computador local e para desenvolvimento e teste.

O mecanismo do Docker deve ser executado localmente para concluir as etapas a seguir para operacionalizar o modelo. Use o `-h` sinalizador no final de cada comando para mostrar a mensagem de ajuda correspondente.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Abra a interface de linha de comando (CLI) do Azure. No aplicativo Machine Learning Workbench, no menu **Arquivo**, selecione **Abrir o prompt de comando**.

    O prompt de linha de comando é aberto no local da pasta de projeto atual **c:\\temp\\myIris**.

1.  Verifique se o provedor de recursos do Azure **Microsoft. containerregistry** é registrado na assinatura. Registre este provedor de recursos antes de criar um ambiente na etapa 3. Verificar se ele já está registrado usando o comando a seguir:

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Exiba esta saída:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Se **Microsoft. containerregistry** é registrado não use o comando a seguir:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    O registro pode levar alguns minutos. Verificar o status do registro usando anterior **lista de provedor az** comando ou o comando a seguir:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    A terceira linha da saída exibe **"registrationState": "Registrar"**. Aguarde alguns minutos e repita a **mostram** comando até que a saída exibe **"registrationState": "registrado.**

1.  Crie o ambiente. Execute essa etapa uma vez por ambiente.

    O seguinte comando de instalação requer acesso de Colaborador à assinatura. É necessário acesso de Colaborador para o grupo de recursos implantando. Especifique o nome do grupo de recursos como parte do comando setup usando o gflag.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Siga as instruções na tela para provisionar uma conta de armazenamento para armazenar imagens do Docker, um registro de contêiner do Azure que lista as imagens do Docker e uma conta do Azure Application Insights para coletar telemetria. **Se usar o cswitch, o comando adicionalmente criará um cluster do serviço de contêiner**.

    O nome do cluster é uma maneira de identificar o ambiente. O local deve ser o mesmo que o local da conta de gerenciamento de modelos criada no portal do Azure.

    Para verificar se o ambiente foi instalado com êxito, use o seguinte comando para verificar o status:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Certifique-se de que o "Estado de provisionamento" tem o valor "Êxito", conforme mostrado, antes de configurar o ambiente na etapa 5:

    ![Estado de Provisionamento](media\azure-stack-solution-machine-learning\image57.png)

1.  Defina o ambiente.

    Depois que a configuração for concluída, use o comando a seguir para definir as variáveis de ambiente necessárias para operacionalizar o ambiente. Use o mesmo nome de ambiente usado anteriormente na etapa 3. Use o mesmo nome de grupo de recursos que foi enviado na janela de comando quando o processo de configuração foi concluído.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Para verificar a configuração adequada do ambiente operacionalizado para implantação de serviço da web local, digite o seguinte comando:

    ```CLI
    az ml env show
    ```

    Agora, crie o serviço web em tempo real.

    > [!Note]  
    > Reutilize a conta de gerenciamento de modelos e o ambiente para implantações de serviço web posteriores. Não é necessário criá-los para cada serviço web. Uma conta ou um ambiente pode ter vários serviços Web associados a ele.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Criar um serviço Web em tempo real usando comandos separados

Como uma alternativa para o **em tempo real de criar o serviço de az ml** comando mostrado anteriormente, essas etapas também podem ser executadas separadamente.

Primeiro, registre o modelo. Em seguida, gere o manifesto, compile a imagem do Docker e crie o serviço Web. Essa abordagem passo a passo fornece mais flexibilidade em cada etapa. Além disso, as entidades geradas nas etapas anteriores podem ser reutilizadas.

1. Registre o modelo, fornecendo o nome do arquivo pickle.

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Este comando gera uma ID de modelo.

2.  Criar um manifesto.

    Para criar um manifesto, use o seguinte comando e forneça a ID do modelo de saída da etapa anterior: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Este comando gera uma ID de manifesto.

3.  Crie uma imagem do Docker.

    Para criar uma imagem do Docker, use o comando a seguir e forneça a saída de valor de ID de manifesto da etapa anterior. As dependências de conda também podem ser usadas por meio de `-c` alternar. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Este comando gera uma ID de imagem do Docker.

2.  Criar o serviço.

    Para criar um serviço, use o seguinte comando e forneça a ID da imagem de saída da etapa anterior: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Este comando gera uma ID de serviço Web.
    
    Em seguida, execute o serviço web.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Etapa 6: Implantar um cluster Kubernetes no Azure Stack

Implante um cluster Kubernetes no Azure Stack.

Kubernetes pode ser instalado usando modelos do Gerenciador de recursos do Azure gerados pelo mecanismo de serviços de contêiner do Azure (ACS) no Azure Stack. [*Kubernetes* ](https://kubernetes.io/) é um sistema de código-fonte aberto para automatizar a implantação, dimensionamento e gerenciamento de aplicativos em contêineres. Um [ *contêiner* ](https://www.docker.com/what-container) está contida em uma imagem, semelhante a uma VM. Ao contrário de uma VM, a imagem de contêiner inclui os recursos necessários para executar um aplicativo, como o código, o tempo de execução para executar o código, bibliotecas específicas e configurações.

Use Kubernetes para:

 -  Desenvolva aplicativos massivamente escalonáveis, pode ser atualizados, que podem ser implantados em segundos.

 -  Simplificar o design do aplicativo e melhorar sua confiabilidade por aplicativos diferentes do Helm. [*Helm* ](https://github.com/kubernetes/helm) é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes.

 -  Com facilidade, monitorar e diagnosticar a integridade dos aplicativos com a escala e funcionalidade de atualização.

> [!Note]  
> Instalação do cluster levará cerca de uma hora, por favor, plano adequadamente.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Pré-requisitos para implantação de cluster do Kubernetes

Para começar, confirme as permissões e preparação para o Azure Stack:

1.  Validar que o **criar Cluster do Kubernetes (versão prévia)** Item está disponível no Azure Stack Marketplace. Se este item estiver ausente, trabalhe com um operador de pilha do Azure para adicionar este item para o ambiente de pilha do Azure.

2.  Verifique se a capacidade de criar aplicativos no locatário do Azure Active Directory (Azure AD). Permissões são necessárias para a implantação do Kubernetes.

    Para obter instruções sobre como verificar as permissões, consulte [ *permissões do Active Directory do Azure Verifique*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Gere um público e privado par de chaves SSH para entrar na VM do Linux no Azure Stack. A chave pública é necessária ao criar o cluster. Para obter instruções, consulte [gerar uma chave de autenticação para o SSH](#generate-an-authenticatio-key-for-ssh).

4.  Verifique se a assinatura no portal de locatário do Azure Stack é válida e se há suficiente IP público endereços disponíveis para adicionar novos aplicativos.

    O cluster não pode ser implantado para Azure Stack **administrador** assinatura. Use uma **usuário** assinatura.

###  <a name="generate-an-authentication-key-for-ssh"></a>Gerar uma chave de autenticação para o SSH

De dentro do subsistema do Windows para a sessão do Linux, use os seguintes comandos para gerar uma chave de autenticação: 

1. Digite:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Selecione `id_rsa` quando solicitado. 
3. Crie uma senha quando solicitado. É importante observar essa senha para uso posterior. 
    A saída será semelhante à abaixo: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alt text](media\azure-stack-solution-machine-learning\image58.png)

4. Depois de gerar a chave, cole as informações de chave usando os seguintes comandos: 
    ```Bash
    cat id_rsa.pub
    ```
    A saída será semelhante à abaixo: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Copie a chave gerada para o SSH chave campo público.

### <a name="create-a-service-principal-in-azure-ad"></a>Criar uma entidade de serviço no Azure AD

1.  Entrar para global [ *portal do Azure*](http://www.poartal.azure.com/).

2.  Entre usando um locatário do Azure AD associado à instância do Azure Stack.

3.  Criar um aplicativo Azure AD.

    1. Selecione **do Azure Active Directory** > **+ registros do aplicativo**> **novo registro de aplicativo**.
    2. Insira um **nome** do aplicativo. 
    3. Selecione **aplicativo Web / API**. 
    4. Insira `http://localhost` para o **URL de logon**. 
    5. Selecione **Criar**.

1.  Anote a **ID do aplicativo**. Essa ID é necessária ao criar o cluster e é referenciado como **ID do cliente de entidade de serviço**.

2.  Selecione **as configurações** > **chaves**.

    1. Insira o **descrição**. 
    2. Selecione **nunca expira** para **Expires**. 
    3. Clique em **Salvar**. Certifique-se de observar a cadeia de caracteres de chave. A cadeia de caracteres de chave é necessária ao criar o cluster e é referenciado como o **segredo do cliente de entidade de serviço**.

### <a name="give-the-service-principal-access"></a>Conceder acesso à entidade de serviço

Conceder acesso à entidade de serviço para a assinatura para que ele pode criar recursos.

1.  Entrar para o [portal de administração](https://adminportal.local.azurestack.external/).

2.  Selecione **mais serviços** > * * as assinaturas do usuário * * > **+ adicionar**.

3.  Selecione a assinatura criada.

4.  Selecione **controle de acesso (IAM)** > selecione **+ adicionar**.

5.  Selecione o **proprietário** função.

6.  Selecione o nome do aplicativo criado para o serviço principal. Digite o nome na caixa de pesquisa, se necessário.

7.  Clique em **Salvar**.

8.  Abra o [portal do Azure Stack](https://portal.local.azurestack.external).

9.  Selecione **+ novo** > **computação** > **Cluster Kubernetes**. Selecione **Criar**.

    ![Modelo de Solução de Implementação](media\azure-stack-solution-machine-learning\image59.png)

10\. Selecione **Noções básicas de** na criar o Cluster Kubernetes.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Insira o **o nome de usuário de administrador VM Linux**. Nome de usuário para as máquinas virtuais de Linux que fazem parte do cluster Kubernetes e DVM.

12. Insira o **chave pública SSH** usado para autorização em todas as máquinas Linux criado como parte do cluster Kubernetes e DVM.

13. Insira o **prefixo de DNS do mestre de perfil** que é exclusivo para a região. Isso deve ser uma região exclusivos nome, como `ask8s-12345`. Tentar escolhê-lo mesmo que o grupo de recursos nomeie como melhor prática.

    > [!Note]  
    > Para cada cluster, use um prefixo DNS do mestre de perfil novos e exclusivos.

14. Insira o **contagem de perfil do agente de Pool**. A contagem de contém o número de agentes no cluster. Pode haver de 1 a 4.

15. Insira o **entidade de serviço ClientId** isso é usado pelo provedor de nuvem do Azure do Kubernetes.

16. Insira o **segredo do cliente de entidade de serviço** criadas durante a criação do aplicativo de serviço principal.

17. Insira o **versão do provedor de nuvem Azure Kubernetes**. Essa é a versão para o provedor do Azure do Kubernetes. O Azure Stack libera uma criação personalizada de Kubernetes para cada versão do Azure Stack.

18. Selecione o **assinatura** ID.

19. Insira o nome de um novo grupo de recursos ou selecione um grupo de recursos. O nome do recurso precisa ser alfanuméricos e minúsculos.

20. Selecione o **local** do grupo de recursos. Essa é a região escolhida para a instalação do Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Especifique as configurações do Azure Stack

1.  Selecione o **configurações de carimbo de data / Azure Stack**.

    ![Modelo de Solução de Implementação](media\azure-stack-solution-machine-learning\image61.png)

2.  Insira o **ponto de extremidade do Azure Resource Manager de locatário**. Esse é o ponto de extremidade do Azure Resource Manager para conectar-se para criar o grupo de recursos para o cluster Kubernetes. O ponto de extremidade do operador do Azure Stack é necessária para um sistema integrado. Para o Azure Stack desenvolvimento ASDK (Kit), use `https://management.local.azurestack.external`.

3.  Insira o **ID do locatário** para o locatário. Ver [ *obter ID do locatário* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) para localizar o locatário do valor de ID.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Instalar kubectl no ambiente do Windows PowerShell

De dentro do ambiente WSL, execute os seguintes comandos para instalar o kubectl no ambiente de WSL.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Instalar kubectl no subsistema do Windows para o ambiente do Linux

De dentro do ambiente WSL, execute os seguintes comandos para instalar o kubectl no ambiente de WSL.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Configurar kubectl

Para que o kubectl localizar e acessar um cluster Kubernetes, uma*kubeconfig arquivo* é necessária. Isso é criado automaticamente quando um cluster é criado usando kube-up.sh ou implantando um cluster do Minikube. Consulte a [ *guias de Introdução* ](https://kubernetes.io/docs/setup/) para obter mais informações sobre a criação de clusters. Para acessar um cluster criado por outro usuário, consulte o [ *documento acesso ao compartilhamento de Cluster*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Por padrão, a configuração de kubectl está localizada em **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Verifique a configuração de kubectl

Verifique que kubectl está configurado corretamente, fazendo com que o estado do cluster:

```Bash  
kubectl cluster-info
```

kubectl está configurado corretamente para acessar o cluster quando a resposta da url é exibida.

kubectl não está corretamente configurado ou não consegue se conectar a um cluster Kubernetes, se a seguinte mensagem é exibida:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Por exemplo, ao executar um cluster Kubernetes em um laptop local, uma ferramenta pode ser necessária (minikube ou semelhante) para executar novamente os comandos mencionados acima.

Se as informações do cluster kubectl retorna a resposta da url, mas o cluster ainda não está acessível, verificar a configuração apropriada, usando:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Habilitar o preenchimento automático de shell

kubectl inclui suporte de preenchimento automático, tornando a habilitação de shell rápida e fácil.

O script de conclusão em si é gerado pelo kubectl e é acessível do perfil.

### <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao Cluster Kubernetes

Para se conectar ao cluster, o cliente de linha de comando Kubernetes (**kubectl**) é necessária. Instruções para conectar e gerenciar o cluster são encontradas no [documentação dos serviços de contêiner do Azure.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Qualquer cliente SSH pode ser usado para conectar o cluster Kubernetes. Recomenda-se o subsistema do Windows para Linux (WSL). O computador se conectar ao Kubernetes Cluster estará no grupo de recursos criado para o cluster e será iniciado com o prefixo da borda vmd-pilha ml.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Uma vez conectado à máquina Kubernetes, execute a seguinte máquina para obter o arquivo de configuração do Kubernetes.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

A saída se parece algo parecido com isto:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Copie essas informações de caminho de arquivos e, em seguida, execute o comando a seguir, colando no caminho do arquivo kubeconfig copiado acima:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

A saída será um grande bloco de texto, que é o conteúdo JSON bruto. Copie o texto de saída e, em seguida, cole este código em um arquivo do Visual Studio, salvando como um arquivo JSON. Isso resulta em um arquivo kubeconfig.json armazenados localmente. (Salvar no /mnt/Retention/c/usuários/ <current user> /documentos/diretório Kube como kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Configurar o Cluster Kubernetes

Depois que o arquivo JSON local é obtido, em uma nova sessão WSL, use os seguintes comandos para configurar o Cluster.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

As definições de configuração do Kubernetes serão definidas (veja a saída abaixo).

![Alt text](media\azure-stack-solution-machine-learning\image62.png)

Inicie o serviço de proxy local:

```Bash  
kubectl proxy
```

Navegue até o interface do usuário do cluster kubernetes no seguinte endereço: `https://localhost:8001`.

![Alt text](media\azure-stack-solution-machine-learning\image63.png)

Agora você tem um lugar para implantar o contêiner e um contêiner que reside na nuvem que você pode ver no local.

![Alt text](media\azure-stack-solution-machine-learning\image64.png)

Personalizar o **iris_deployment.yaml** arquivo (localizado no /*mnt/c/usuários/<current user>/documentos/Kube diretório*) para que **webservicename** e contêineres  **Imagem** e **nome** correspondem à implantação, usando qualquer editor de códigos de escolha.

![Alt text](media\azure-stack-solution-machine-learning\image65.png)

Defina a porta do contêiner como **5001.**

![Alt text](media\azure-stack-solution-machine-learning\image66.png)

E, em seguida, crie o **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Criar um segredo no cluster que contém o token de autorização

Um cluster Kubernetes usa o segredo da **docker-registry** tipo para autenticar com um registro de contêiner para efetuar pull de uma imagem privada.

Crie esse segredo, nomeando- **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Localize:

- **< your-registro do servidor >** é o registro de contêiner do Azure **servidor de logon**.
- **< your-name >** é o registro de contêiner do Azure **nome de usuário**.
- **< your pword >** é o registro de contêiner do Azure **senha**. Verifique se que a senha está entre aspas.
- **< your-email >** é o usuário que tem acesso de R/W ao contêiner.
- Encontrar essas informações sobre o **contêiner do Azure** **registro** sob **chaves de acesso**.
- Credenciais do docker agora são definidas no cluster como um segredo chamado **azuremlcr**.

Salvar a **iris_deployment.yaml** arquivo (localizado no /*mnt/c/usuários/<current user>/documentos/Kube diretório*).

### <a name="create-the-kubernetes-container"></a>Criar o contêiner do Kubernetes

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Verifique o status da implantação:

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

A implantação pode levar algum tempo.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Configurar o Visual Studio Team Services para implantar automaticamente

#### <a name="create-a-team-project"></a>Criar um projeto de equipe

1.  Certifique-se de [associação do grupo de administradores de coleção de projeto.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Para criar projetos de equipe **criar novos projetos** permissão deve ser definida como **permitir**.

2.  Na página de projetos, selecione **novo projeto**.

    ![Alt text](media\azure-stack-solution-machine-learning\image69.png)

1.  Nomeie o projeto **HybridMLIris**.

2.  Selecione seu tipo de controle de origem inicial como **Git**

3.  Selecione um processo e selecione **criar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importar alguns códigos criar repositório

Um repositório Git para o código YAML é necessário.

#### <a name="add-user-to-the-git-repo"></a>Adicionar o usuário para o repositório GIT

1.  No painel de projeto padrão, selecione as credenciais do Git de gerar.

    ![Alt text](media\azure-stack-solution-machine-learning\image71.png)

1.  Insira a senha sempre que necessário e salve as credenciais do Git.

    ![Alt text](media\azure-stack-solution-machine-learning\image72.png)

1.  Inicializar o repositório, selecionando o **inicializar** botão e a criação de uma **README** arquivo.

    ![Alt text](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Clonar o repositório Git localmente e carregar o código. 

1.  Crie um diretório no computador em `c:\\users\\<User>\\source\\repos\\hybridMLIris`e clone o repositório

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image74.png)

1.  Navegue até o repositório recém-clonado:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Cópia de **iris_deployment.yaml** arquivo no repositório.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Confirmar a alteração no GIT

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Preparar-se a versão do agente e uma compilação particular para integração com o VSTS

#### <a name="prerequisites"></a>Pré-requisitos

VSTS autentica no Azure Resource Manager usando uma entidade de serviço. Para o VSTS ser capaz de provisionar recursos em uma assinatura do Azure Stack requer Colaborador status. \ **a seguir estão as etapas de alto nível que precisam ser configurados para habilitar essa autenticação:**

1.  Entidade de serviço devem ser criada ou uma existente pode ser usada.

2.  As chaves de autenticação precisam ser criadas para a entidade de serviço.

3.  Assinatura de pilha do Azure precisa ser validada por meio do controle de acesso baseado em função para permitir que o SPN a ser parte da função colaboradores.

4.  Uma nova definição de serviço no VSTS deve ser criada usando os pontos de extremidade do Azure Stack, bem como informações de SPN.

#### <a name="service-principal-creation"></a>Criação da entidade de serviço

Consulte a [instruções de criação da entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) para criar uma entidade de serviço e escolha o aplicativo Web/API para o tipo de aplicativo.

**Criação de chave de acesso**

Uma entidade de serviço requer uma chave para autenticação, siga as etapas nesta seção para gerar uma chave.

1.  Partir **registros de aplicativo** no Azure Active Directory, selecione o aplicativo.

    ![selecionar aplicativo](media\azure-stack-solution-machine-learning\image77.png)

1.  Anote o valor de **ID do aplicativo. O valor é usado ao configurar o ponto de extremidade de serviço no VSTS.**

    ![Alt text](media\azure-stack-solution-machine-learning\image78.png)

1.  Para gerar uma chave de autenticação, selecione **Configurações**.

    ![selecione configurações](media\azure-stack-solution-machine-learning\image79.png)

1.  Selecione **Chaves**.

    ![selecionar chaves](media\azure-stack-solution-machine-learning\image80.png)

1.  Forneça uma descrição da chave e uma duração para a chave. Ao terminar, escolha **Salvar**.

    ![salvar chave](media\azure-stack-solution-machine-learning\image81.png)

Após salvar a chave, o valor da chave é exibido. Copie esse valor, pois ela é necessária mais tarde. O **valor de chave** com o aplicativo ID é necessária para fazer logon como o aplicativo. Store o valor da chave em que o aplicativo pode recuperá-lo.

![Alt text](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Obter ID de locatário

Como parte da configuração do ponto de extremidade de serviço, o VSTS requer o **ID do locatário** que corresponde ao diretório do AAD, o carimbo de data / Azure Stack foi implantado. Siga as etapas nesta seção para coletar a ID de locatário.

1.  Selecione **Azure Active Directory**.

    ![selecionar azure active directory](media\azure-stack-solution-machine-learning\image83.png)

1.  Para obter a ID de locatário, selecione **propriedades** para o locatário do Azure AD.

    ![selecionar propriedades do Azure AD](media\azure-stack-solution-machine-learning\image84.png)

1.  Copie a **ID de diretório**. Esse valor é a ID de locatário.

    ![ID do locatário](media\azure-stack-solution-machine-learning\image85.png)

Conceder os direitos de entidade de serviço para implantar recursos na assinatura do Azure Stack

Para acessar recursos na assinatura, atribua o aplicativo a uma função. Decida qual função representa as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, consulte [RBAC: funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Defina o escopo no nível da assinatura, grupo de recursos ou recurso. As permissão são herdadas para níveis inferiores do escopo. Por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos permite que ele leia o grupo de recursos e quaisquer recursos que ele contém.

1.  Navegue até o nível desejado de escopo para atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, escolha **Assinaturas**.

    ![Alt text](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Selecione o **assinatura** (grupo de recursos ou recurso) para atribuir o aplicativo.

    ![selecionar assinatura para atribuição](media\azure-stack-solution-machine-learning\image87.png)

1.  Selecione **Controle de Acesso (IAM)**.

    ![selecionar acesso](media\azure-stack-solution-machine-learning\image88.png)

1.  Selecione **Adicionar**.

    ![escolher adicionar](media\azure-stack-solution-machine-learning\image89.png)

1.  Selecione a função para atribuir o aplicativo. A imagem a seguir mostra a **proprietário** função.

    ![Alt text](media\azure-stack-solution-machine-learning\image90.png)

1.  Por padrão, os aplicativos do Azure Active Directory não são exibidos nas opções disponíveis. Para localizar o aplicativo **forneça o nome** na pesquisa de campo e selecioná-lo.

    ![Alt text](media\azure-stack-solution-machine-learning\image91.png)

1.  Selecione **Salvar** para finalizar a atribuição da função. O aplicativo é exibido na lista de usuários atribuídos a uma função para esse escopo.

### <a name="role-based-access-control"></a>Controle de Acesso Baseado em Função

Controle de acesso do Azure baseado em função (RBAC) permite o gerenciamento de acesso refinado para o Azure e o Azure Stack. Usando o RBAC, pode ser concedida somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Para obter mais informações sobre o controle de acesso baseado em função, consulte [gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**Pools de agente do VSTS**

Em vez de gerenciar cada agente individualmente, os agentes são organizados em **pools de agente**. Um pool de agentes define o limite de compartilhamento para todos os agentes nesse pool. No VSTS, os pools de agente estão no escopo para a conta do VSTS, portanto, podem ser compartilhados entre projetos de equipe. Para obter mais informações e um tutorial sobre como criar VSTS pools de agente, consulte [criar Pools de agente e filas.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Adicione o token de acesso aPersonal (PAT) para o Azure Stack**

 -  Faça logon no conta do VSTS e selecione **perfil de conta** nome.

 -  Selecione **gerenciar segurança** a página de criação de token de acesso.

![Alt text](media\azure-stack-solution-machine-learning\image92.png)

![Alt text](media\azure-stack-solution-machine-learning\image93.jpeg)

![Alt text](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Obter as informações de token. Ele não será exibido novamente após deixar essa tela.

1.  Cópia de **token**.

    ![Alt text](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalação do que agente de build do VSTS no Azure Stack hospedou o servidor de compilação

1.  Conectar-se para o **servidor de compilação** implantado no host do Azure Stack.

    > [!Note]  
    > Verifique se o que servidor de compilação da hospedado do Azure Stack está acessível pela Internet pública. Caso contrário, trabalhar com um operador de pilha do Azure para obter acesso.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Atualize o servidor de compilação do Ubuntu para a versão mais recente (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Esta operação levará algum tempo.

2.  Instale aplicativos de pré-requisito para o servidor de compilação. De bash shell do Ubuntu com base em execução do servidor de compilação os comandos a seguir:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Baixe e implante o agente de compilação como um serviço usando um **token de acesso pessoal (PAT)** e executados como a conta de administrador de VM.

    ![Alt text](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Vá para a pasta do agente de compilação extraídos. Execute o seguinte código.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image97.png)

2.  Após **./config.sh**terminar, execute o seguinte código para ativar o serviço na inicialização do servidor e iniciar o serviço:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

O agente agora está visível na pasta do VSTS.

#### <a name="endpoint-creation-permissions"></a>Permissões de criação de ponto de extremidade

Os usuários podem criar pontos de extremidade para que compilações do VSTO podem implantar aplicativos de serviço do Azure para a pilha. O VSTS se conecta ao agente de compilação, em seguida, conecta-se com o Azure Stack.

![Alt text](media\azure-stack-solution-machine-learning\image98.png)

1.  Sobre o **as configurações** menu, selecione **segurança**.

2.  No **grupos do VSTS** na lista à esquerda, selecione **criadores de ponto de extremidade**.

    ![Alt text](media\azure-stack-solution-machine-learning\image99.png)

3.  No **guia de membros** selecionar a **+ adicionar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image100.png)

1.  Tipo de **nome de usuário** e selecione o nome de usuário na lista.

2.  Selecione **Salvar alterações**.

    ![Alt text](media\azure-stack-solution-machine-learning\image101.png)

3.  No **grupos do VSTS** na lista à esquerda, selecione **administradores de ponto de extremidade**.

4.  No **guia de membros** selecionar a **+ adicionar**.

5.  Digite um **nome de usuário** e selecione o usuário na lista.

6.  Selecione **salvar as alterações.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    O agente de compilação no Azure Stack ganha instruções do VSTS, que, em seguida, transmite informações de ponto de extremidade para comunicação com o Azure Stack.

    VSTS para a conexão do Azure Stack agora está pronto.

    ![Alt text](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>Configurar definições de compilação e versão

Agora que as conexões são estabelecidas, você manualmente criada extremidade do Azure, AKS e registro de contêiner do Azure para a compilação do mapa e definições de versão.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Criar a definição de compilação para código YAML a

1.  Selecione a seção se baseia em um hub de Build e versão e criar uma nova definição.

    ![Alt text](media\azure-stack-solution-machine-learning\image104.png)

1.  Selecionar Git VSTS e selecione o repositório criado anteriormente.

    ![Alt text](media\azure-stack-solution-machine-learning\image105.png)

1.  Selecione o Pipeline vazio como o modelo

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nomeie a compilação **cópia artefato** e selecione o servidor de compilação da pilha do Azure para a fila do agente.

    ![Alt text](media\azure-stack-solution-machine-learning\image107.png)

1.  Selecione fase 1 em processos e renomeie-o para **artefato de cópia**, em seguida, **adicionar uma tarefa** para a fase:

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

1.  Selecione **publicar artefatos de Build** da **utilitário** e selecione **adicionar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Selecione o **caminho para publicar** e selecione o **iris_deployment.yaml** arquivo.

    ![Alt text](media\azure-stack-solution-machine-learning\image110.png)

1.  Nomeie o artefato **iris_deployment** e selecione o local de publicação seja **Visual Studio Team Services/TFS**.

    ![Alt text](media\azure-stack-solution-machine-learning\image111.png)

1.  Selecione **salvar e enfileirar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image112.png)

1.  Verificar o status da compilação, selecionando a ID de compilação.

    ![Alt text](media\azure-stack-solution-machine-learning\image113.png)

Sucesso será semelhante a este:

![Alt text](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Criar a definição de versão para o código YAML

1.  Selecione a seção de versões no hub de Build e versão, uma nova definição

    ![Alt text](media\azure-stack-solution-machine-learning\image115.png)

1.  Selecione o Pipeline vazio como um modelo.

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nome do ambiente do Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image116.png)

1.  Adicionar um novo artefato selecionando **artefatos** e **+ adicionar**

2.  Selecione o Build como o tipo de fonte e **HybridMLIris** como o projeto.

3.  Em seguida, selecione a definição de compilação criada anteriormente para o código-fonte.

4.  Em seguida, selecione **adicionar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image117.png)

1.  Selecione Azure Stack de ambientes, e adicione uma nova tarefa para o Azure Stack

    ![Alt text](media\azure-stack-solution-machine-learning\image118.png)


1.  Sobre a fase de agente, defina a fila do agente para o Azure Stack Build Server hospedado.

    ![Alt text](media\azure-stack-solution-machine-learning\image119.png)

1.  Adicionar uma nova tarefa para esta fase, selecione a opção implantar a tarefa do Kubernetes em implantar e selecione Adicionar.

    ![Alt text](media\azure-stack-solution-machine-learning\image120.png)


1.  Denomine **Kubectl Apply** (nome padrão) e selecione o comando apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image121.png)

    Agora, crie uma nova Conexão de serviço do Kubernetes.

#### <a name="create-kubernetes-service-endpoint"></a>Criar ponto de extremidade de serviço do Kubernetes

1.  Em Conexão de serviço do Kubernetes, selecione o **+ novo** botão e selecione**Kubernetes**na lista. Você pode usar esse ponto de extremidade para se conectar a**VSTS**e o**serviço de contêiner do Azure (AKS)**.

2.  **Nome da Conexão**: forneça o nome de conexão.

3.  **URL do servidor**: forneça o endereço do serviço de contêiner no formathttp: / / {endereço do servidor de API}

4.  **Kubeconfig**: para obter o valor de Kubeconfig, execute os seguintes comandos do Azure em um prompt de comando iniciado com o privilégio de administrador.

    > [!Important]  
    > Use essa janela da CLI para realizar as próximas etapas.

6.  Na janela da CLI, faça logon no Azure. [Saiba mais sobre o az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  No prompt de comando, insira:

    ```CLI
        az login
    ```

10. Esse comando retorna um código para usar no navegador em <https://aka.ms/devicelogin>.

11. Vá para <https://aka.ms/devicelogin> no navegador. Quando solicitado, insira o código recebido na CLI, no navegador.

    ![Ponto de extremidade de serviço do Kubernetes](media\azure-stack-solution-machine-learning\image122.png)

1.  Digite o seguinte comando no prompt de comando para obter as credenciais de acesso para o cluster Kubernetes.

### <a name="azure-ml-workbench-cli"></a>CLI do Azure ML Workbench

grupo de AZ aks get-credentials recursos- <yourResourceGroup> nome <yourazurecontainerservice>

![Ponto de extremidade de serviço do Kubernetes](media\azure-stack-solution-machine-learning\image123.png)

1.  Navegue até a **.kube**pasta sob o diretório base (por exemplo: c:\\usuários\\<user>\\documentos\\Kube)

2.  Copie o conteúdo do**config**de arquivo e cole-o na janela de Conexão do Kubernetes. Selecione o**Okey**botão.

    ![Ponto de extremidade de serviço do Kubernetes](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Depois que o ponto de extremidade do Kubernetes é criado e selecionado, selecione a caixa de seleção de arquivos de configuração de uso para adicionar um arquivo de configuração. Em seguida, navegue até o arquivo de iris_deployment.yaml nos artefatos vinculados.

    ![Alt text](media\azure-stack-solution-machine-learning\image125.png)

    ![Alt text](media\azure-stack-solution-machine-learning\image126.png)

4.  Salve a definição da versão.

#### <a name="check-the-status-of-the-release-definition"></a>Verifique o status da definição de versão. 

Depois de salvo, a definição da versão do VSTS deve iniciar automaticamente uma versão.

Verifique o status da implantação, a execução de um comando rápido no prompt de comando WSL, em seguida, verificando a UI Kubernetes.

```Bash  
kubectl get deployments
```

A saída deve ser semelhante a isso, enquanto estiver no processo de implantação.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Depois que o kubernetes interface do usuário está em execução, navegue até a implantação na [ **https://localhost:8001/** ](https://localhost:8001/) , em seguida, navegue até **cargas de trabalho -> conjuntos de réplicas**.

![Alt text](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>Implantar o serviço YAML

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Carregar o iris_service.yaml para os repositório e sincronizar alterações

1.  Navegue até o repositório recém-clonado:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Cópia de **iris_service.yaml** arquivo no repositório.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Confirmar a alteração no GIT

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alt text](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Atualizar a definição de compilação para código YAML a

1.  Selecione a seção se baseia em um hub de Build e versão e a definição criada anteriormente.

    ![Alt text](media\azure-stack-solution-machine-learning\image130.png)

2.  Selecione o botão Editar para editar a definição.

    ![Alt text](media\azure-stack-solution-machine-learning\image131.png)

3.  **Adicionar uma tarefa** para a fase. Selecione **publicar artefatos de Build** da **utilitário** e selecione **adicionar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

4.  Denomine **Kubectl Apply** (nome padrão) e selecione o comando apply.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Atualizar a definição de versão para o código YAML

1.  Selecione a seção theReleases sob o hub de Build e versão e a definição da versão criada anteriormente. Em seguida, selecione o Link editar.

    ![Alt text](media\azure-stack-solution-machine-learning\image132.png)

1.  Selecione o ambiente **do Azure Stack** , em seguida, adicione uma nova tarefa para o Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image133.png)

1.  Adicionar um **nova tarefa** para esta fase, selecione o **implantar no Kubernetes** tarefa sob **implantar** e selecione **adicionar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image134.png)

1.  Denomine **Kubectl Apply** (nome padrão) e selecione o comando apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Definir a conexão Kubernates Service para a Conexão de pilha do Azure criado anteriormente e, em seguida, selecione a **usar arquivos de configuração** caixa de seleção para adicionar um arquivo de configuração. Navegue até o arquivo iris_service.yaml nos artefatos vinculados.

    ![Alt text](media\azure-stack-solution-machine-learning\image135.png)


    ![Alt text](media\azure-stack-solution-machine-learning\image136.png)

1.  Salve a definição da versão.

#### <a name="check-the-status-of-the-release-definition"></a>Verificar o status da definição de versão

Depois de salvo, a definição da versão do VSTS deve iniciar automaticamente uma versão.

Verifique o status da implantação, a execução de um comando rápido no prompt de comando WSL, em seguida, verificando a UI Kubernetes.

```Bash  
kubectl get deployments
```

A saída deve ser semelhante a isso, enquanto estiver no processo de implantação.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Depois que o kubernetes interface do usuário está em execução, navegue até a implantação na [ **https://localhost:8001/** ](https://localhost:8001/) , em seguida, navegue até **cargas de trabalho -> conjuntos de réplicas**.

![Alt text](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Pontuação de Kubernetes e validação

Iniciar a interface do usuário do Kubernetes

```Bash  
kubectl proxy
```

Navegue até a UI Kubernetes, então, vá para **implantações** -> **implantação íris** -> **novo conjunto de réplicas**  ->  **Iris-implantação-xxxxxxxxx** (onde os xs são a ID de implantação).

![Alt text](media\azure-stack-solution-machine-learning\image138.png)

Em seguida, navegue até **Services** e selecione o **ponto de extremidade externo** do serviço para validar que ele está funcionando.

![Alt text](media\azure-stack-solution-machine-learning\image139.png)

Deve ser exibida uma mensagem de validação semelhante à seguinte:

![Alt text](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Criar aplicativo de funções de pontuação no Portal do Azure Stack de pilha do Azure

Um aplicativo de funções é necessária para hospedar a execução de cada função. Um aplicativo de funções permite que a função de agrupamento como uma unidade lógica para fácil gerenciamento, implantação e compartilhamento de recursos.

1.  No portal do usuário do Azure Stack, selecione a **+ novo** botão encontrado no canto superior esquerdo, em seguida, selecione**Web + móvel** >**aplicativo de funções**.

    ![Alt text](media\azure-stack-solution-machine-learning\image141.png)

1.  Nomeie a função **funções de dados** e coloque-o no mesmo grupo de recursos com o aprendizado de máquina restantes conteúdo. Permitir que a ferramenta de criação automática de um novo plano do serviço de aplicativo para consumo e usar a conta de armazenamento criada anteriormente para o armazenamento de aplicativo.

    ![Definir novas configurações de aplicativo de função](media\azure-stack-solution-machine-learning\image142.png)

1.  Selecione**criar**para provisionar e implantar o aplicativo de funções.

2.  Selecione o ícone de notificação no canto superior direito do portal e ficar atento a**implantação bem-sucedida** mensagem.

    ![Definir novas configurações do aplicativo de funções](media\azure-stack-solution-machine-learning\image143.png)

1.  Selecione**ir para o recurso** para exibir o novo aplicativo de funções.

    ![Alt text](media\azure-stack-solution-machine-learning\image144.png)

1.  Criar uma nova função selecionando **funções**, em seguida, a **+ nova função** botão.

    ![Alt text](media\azure-stack-solution-machine-learning\image145.png)

1.  Selecione o gatilho HTTP

    ![Alt text](media\azure-stack-solution-machine-learning\image146.png)

1.  Selecione **C\#**  como o idioma e o nome da função: **dados limpar de pontuação**e defina o nível de autorização como **anônimo**.

    ![Alt text](media\azure-stack-solution-machine-learning\image147.png)

1.  Copie e cole o conteúdo do exemplo de código para dados limpar de pontuação em uma função.

    ![Alt text](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Usar o Postman para validar funções

Para garantir que você configurou seu Kbernetes e as funções corretamente você pode usar o aplicativo gratuito Postman para testar e validar esquemas e funções. Para iniciar esse processo, primeiro você precisa coletar algumas informações de sua instância do Kubernetes.

1.  Navegue até a UI Kubernetes, então, vá para **implantações** -> **implantação íris** -> **novo conjunto de réplicas**  ->  **Iris-implantação-xxxxxxxxx** (onde os xs são a ID de implantação)

    ![Alt text](media\azure-stack-solution-machine-learning\image138.png)

1.  Em seguida, navegue até **Services** e copie o **ponto de extremidade externo**.

    ![Alt text](media\azure-stack-solution-machine-learning\image149.png)

1.  Baixe e instale o aplicativo Postman [aqui](https://www.getpostman.com/apps) se necessário.

2.  Entrar para o aplicativo Postman e feche a caixa de diálogo Novo arquivo.

    ![Alt text](media\azure-stack-solution-machine-learning\image150.png)

1.  No aplicativo postman, selecione POST...

    ![Alt text](media\azure-stack-solution-machine-learning\image151.png)

1.  Colar a **ponto de extremidade externo** URL para o aplicativo postman sob o **URL de solicitação** adicionando  **\\pontuação** ao final da URL conforme mostrado abaixo.

    ![Alt text](media\azure-stack-solution-machine-learning\image152.png)

1.  Selecione o **corpo** guia e, em seguida, os tipo de dados como **brutos**, em seguida, **JSON**.

    ![Alt text](media\azure-stack-solution-machine-learning\image153.png)

1.  Em um navegador da web, navegue até **ponto de extremidade externo**. Adicionando o seguinte à URL **/swagger.json** isso nos leva para o arquivo de Swagger de serviços usado para testar a instalação.

    ![Alt text](media\azure-stack-solution-machine-learning\image154.png)

1.  Copie o exemplo listado na **swagger** arquivo.

2.  No aplicativo Postman, cole o exemplo no corpo da postagem e selecione **enviar**. Ele deve retornar um valor semelhante ao mostrado abaixo.

    ![Alt text](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Etapa 7: Criar uma conta de armazenamento do Azure Stack e a fila de armazenamento

Crie uma conta de armazenamento do Azure Stack e a fila de armazenamento para os dados.

1.  Entrar no portal do usuário do Azure Stack. (Cada pilha do Azure tem um portal exclusivo URL)

2.  No portal do usuário do Azure Stack, expanda o menu à esquerda para abrir o menu de serviços e escolha **todos os serviços**. Role para baixo até **armazenamento** e escolha **contas de armazenamento**. No **contas de armazenamento** janela escolher **Add**.

3.  Insira um nome para a conta de armazenamento.

4.  Selecione a opção de replicação para a conta de armazenamento: **LRS**.

5.  Especifique um novo grupo de recursos ou selecione um grupo de recursos existente.

6.  Selecione **Local** para o local para a conta de armazenamento.

7.  Selecione**criar**para criar a conta de armazenamento.

    ![Alt text](media\azure-stack-solution-machine-learning\image156.png)

1.  Escolha a conta de armazenamento criada recentemente.

2.  Selecione na**filas**.

    ![Alt text](media\azure-stack-solution-machine-learning\image157.png)

1.  Selecione na **+ fila** e nomeie a fila e selecione **Okey.**

    ![Alt text](media\azure-stack-solution-machine-learning\image158.png)

1.  Obter o **cadeia de caracteres de Conexão** para a fila de armazenamento e copie-o.

    ![Alt text](media\azure-stack-solution-machine-learning\image159.png)

1.  Navegue até o aplicativo de funções do Azure e, em seguida, selecione **configurações do aplicativo**.

    ![Alt text](media\azure-stack-solution-machine-learning\image160.png)

1.  De dentro das configurações de aplicativo do aplicativo de funções, role para baixo até as configurações do aplicativo e selecione **+ adicionar nova configuração**.

    ![Alt text](media\azure-stack-solution-machine-learning\image161.png)

1.  Insira o nome da conta de armazenamento na **nome** campo, adicionando ao final; armazenamento

Isso permite que o aplicativo entender o que se trata de um ponto de extremidade de conta de armazenamento.

1.  Em seguida, cole a cadeia de caracteres de Conexão para o **valor** campo.

    ![Alt text](media\azure-stack-solution-machine-learning\image162.png)

1.  Role até a parte superior das configurações de aplicativo e selecione **salvar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Atualizar a função de pontuação para usar a fila de armazenamento

1.  Selecione na **integrar** na função e cancele a seleção de **obter** opção.

2.  Clique em **Salvar**.

3.  Em seguida, selecione **+ nova saída** de saídas.

    ![Alt text](media\azure-stack-solution-machine-learning\image164.png)

1.  Em seguida, selecione **armazenamento de filas do Azure** e escolha **selecione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image165.png)

1.  Atualizar o **nome da fila** para a fila de armazenamento criado anteriormente e, em seguida, defina o **Conexão da conta de armazenamento** para a conexão da conta de armazenamento criada anteriormente e selecione **salvar.**

    ![Alt text](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Etapa 8: Criar uma função para manipular os dados limpos

Crie uma nova função do Azure Stack para mover os dados limpos da pilha do Azure para o Azure.

1.  Criar uma nova função selecionando **funções**, em seguida, a **+ nova função** botão.

    ![Alt text](media\azure-stack-solution-machine-learning\image167.png)

1.  Selecione **gatilho de temporizador**.

    ![Alt text](media\azure-stack-solution-machine-learning\image168.png)

1.  Selecione **C\#**  como o idioma e o nome da função: **upload para o azure** e defina a agenda **0 0 \*/1 \* \* \***  que CRON notação é uma vez por hora.

    ![Alt text](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Obter a cadeia de Conexão para a conta de armazenamento hospedado do Azure

1.  Navegue até <https://portal.azure.com> e, em seguida, selecione o **conta de armazenamento do Azure** criado anteriormente.

2.  Selecione **chaves de acesso**, em seguida, copie o **cadeia de caracteres de Conexão** da conta de armazenamento.

    ![Alt text](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Atualizar o upload para o azure função usar o armazenamento do Azure hospedados

1.  Navegue até o aplicativo de funções do Azure e, em seguida, selecione **configurações do aplicativo**.

    ![Alt text](media\azure-stack-solution-machine-learning\image171.png)

1.  De dentro das configurações de aplicativo do aplicativo de funções, role para baixo até as configurações do aplicativo e selecione **+ adicionar nova configuração**.

    ![Alt text](media\azure-stack-solution-machine-learning\image172.png)

1.  Insira o nome da conta de armazenamento na **nome** campo, adicionando ao final; armazenamento

Isso permite que o aplicativo entender o que se trata de um ponto de extremidade de conta de armazenamento.

1.  Em seguida, cole o Azure hospedados cadeia conta de armazenamento Conexão na **valor** campo.

    ![Alt text](media\azure-stack-solution-machine-learning\image173.png)

1.  Role até a parte superior das configurações de aplicativo e selecione **salvar**.

    ![Alt text](media\azure-stack-solution-machine-learning\image174.png)

1.  Navegue de volta para o **upload para o azure** função.

2.  Selecione na **integrar** na função.

3.  Em seguida, selecione **+ nova saída** de saídas.

    ![Alt text](media\azure-stack-solution-machine-learning\image175.png)

1.  Em seguida, selecione **armazenamento de BLOBs do Azure** e escolha **selecione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image176.png)

1.  Atualização de **caminho** ao contêiner de armazenamento criado anteriormente no seguinte formato: **uploadeddata /. txt de {rand guid}** e, em seguida, defina o **Conexão da conta de armazenamento** para o Conexão de conta de armazenamento do Azure criada anteriormente e selecione **salvar.**

    ![Alt text](media\azure-stack-solution-machine-learning\image177.png)

1.  O conteúdo do exemplo de código para copiar e colar **upload para o azure** em uma função.

2.  Modifique conforme necessário para apontar para a cadeia de caracteres de Conexão de contas de armazenamento.

3.  Salve e execute o código.

    ![Alt text](media\azure-stack-solution-machine-learning\image178.png)

1.  Verifique a conta de armazenamento hospedado do Azure para ver os dados de backup na nuvem do Azure foi analisada: êxito será semelhante de abaixo.

    ![Alt text](media\azure-stack-solution-machine-learning\image179.png)

Os dados foi corrigidos de dados confidenciais por meio da Azure Stack Kubernetes Machine Learning hospedado e carregados para a nuvem pública do Azure da pilha de locais do Azure, por meio de aplicativos do Azure Stack hospedado função e pode preparar os dados para uploads em uma borda/desconectada cenário.

## <a name="next-steps"></a>Próximas etapas

 - Para saber mais sobre os padrões de nuvem do Azure, consulte [padrões de Design de nuvem](https://docs.microsoft.com/azure/architecture/patterns).