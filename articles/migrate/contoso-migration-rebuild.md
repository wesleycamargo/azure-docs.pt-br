---
title: Recompilar um aplicativo no local da Contoso para o Azure | Microsoft Docs
description: Saiba como a Contoso recompila um aplicativo no Azure usando Serviços de Aplicativos do Azure, o serviço do Kubernetes, o CosmosDB, o Azure Functions e os serviços cognitivos.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 3d835a7bd93426e57c5ab204d277faca22ae0638
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42143932"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migração da Contoso: recompilar um aplicativo local para o Azure

Este artigo demonstra como a Contoso migra e recompila seu aplicativo SmartHotel no Azure. Eles migram a VM de front-end do aplicativo para aplicativos Web dos Serviços de Aplicativos do Azure. O back-end do aplicativo é criado usando microsserviços implantados em contêineres gerenciados pelo AKS (Serviço de Kubernetes do Azure). O site interage com o Azure Functions, fornecendo funcionalidade de fotos de animais de estimação. 

Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Os cenários crescem em complexidade e adicionaremos artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais](contoso-migration-assessment.md)  | Mostra como a Contoso executa uma avaliação de um aplicativo SmartHotel de dois níveis no local em execução no VMware. A Contoso avalia as VMs de aplicativos com o serviço [ do Azure Migrate ](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [ Assistente de Migração de Banco de Dados ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Hospedar novamente um aplicativo em VMs do Azure e uma Instância Gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso executa uma migração de elevação e troca para o Azure no aplicativo SmartHotel. A Contoso migra a VM frontend do aplicativo usando [ Azure Site Recovery ](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados do aplicativo para uma Instância Gerenciada do SQL, usando o [ Serviço de Migração do Banco de Dados do Azure ](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra as VMs do aplicativo SmartHotel usando somente o Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo para VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo e o serviço Database Migration para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso faz uma migração de elevação e deslocamento do aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no Servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e migra o banco de dados do aplicativo para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Disponível
[Artigo 10: Refatorar um aplicativo Linux para aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra a implantação do TFS (Team Foundation Server) local migrando-a para o VSTS (Visual Studio Team Services) no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo para os contêineres do Azure e no Banco de Dados SQL](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
Artigo 13: Recompilar um aplicativo para o Azure | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Este artigo.

Neste artigo, a Contoso migra o Windows de duas camadas. Aplicativo NET SmartHotel em execução nas VMs do VMware para o Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou de perto com seus parceiros de negócios para entender o que eles querem alcançar com essa migração:

- **Tratar o crescimento dos negócios**: a Contoso está crescendo. Eles desejam fornecer experiências diferenciadas para seus clientes em seus sites.
- **Agilidade**: a Contoso deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global. 
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.
- **Custos**: a Contoso deseja minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou os requisitos do aplicativo para esta migração. Esses requisitos foram usados para determinar o melhor método de migração:
 - O aplicativo no Azure permanecerá tão importante quanto é hoje. Ele deve ter um bom desempenho e ser dimensionado facilmente.
 - O aplicativo não deve usar componentes de IaaS. Tudo o que deve ser criado para usar PaaS ou serviços sem servidor.
 - Os builds de aplicativo devem ser executados nos serviços de nuvem e os contêineres devem residir em um Registro de contêiner de toda a empresa privado na nuvem.
 - O serviço de API usado para fotos de animais de estimação deve ser precisa e confiável no mundo real, uma vez que as decisões tomadas pelo aplicativo devem ser respeitadas em seus hotéis. Qualquer animal de estimação com acesso concedido pode permanecer nos hotéis.

## <a name="solution-design"></a>Design da solução

Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo local SmartHotel é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


### <a name="proposed-architecture"></a>Arquitetura proposta

- O front-end do aplicativo será implantado como um aplicativo Web de Serviços de Aplicativos do Azure, na sua região primária.
- Uma função do Azure fornecerá os uploads de fotos de animais de estimação e o site interagirá com essa funcionalidade.
- A função de foto animais de estimação utiliza a API da Pesquisa Visual dos Serviços Cognitivos e o CosmosDB.
- O back-end do site é criado usando microsserviços. Eles serão implantados em contêineres gerenciados no AKS (Serviço de Kubernetes do Azure).
- Os contêineres serão compilados usando o VSTS e enviados por push para o ACR (Registro de Contêiner do Azure).
- Por enquanto, a Contoso implantará manualmente o código de função e de aplicativo Web usando o Visual Studio.
- Os microsserviços serão implantados usando um script do PowerShell que chama as ferramentas de linha de comando do Kubernetes.

    ![Arquitetura de cenário](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Análise de solução
A Contoso avalia seu projeto proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | Usar o PaaS e soluções sem servidor para a implantação de ponta a ponta reduz significativamente o tempo de gerenciamento que a Contoso deve fornecer.<br/><br/> Mudar para uma arquitetura de microsserviço permite que a Contoso estenda facilmente sua solução ao longo do tempo.<br/><br/> A nova funcionalidade pode ser colocada online sem interromper qualquer uma das bases de código de soluções existentes.<br/><br/> O aplicativo Web será configurado com várias instâncias sem nenhum ponto único de falha.<br/><br/> O dimensionamento automático será habilitado para que o aplicativo possa lidar com diferentes volumes de tráfego.<br/><br/> Com a mudança para os serviços de PaaS, a Contoso pode desativar desatualizadas soluções em execução no sistema operacional Windows Server 2008 R2.<br/><br/> O CosmosDB tem tolerância a falhas interna, que não requer nenhuma configuração pela Contoso. Isso significa que a camada de dados não é mais um ponto único de failover.
**Contras** | Contêineres são mais complexos do que outras opções de migração. A curva de aprendizado pode ser um problema para a Contoso.  Eles apresentam um novo nível de complexidade que fornece bastante valor apesar da curva.<br/><br/> A equipe de operações da Contoso precisará se esforçar para entender e dar suporte ao Azure, aos contêineres e aos microsserviços para o aplicativo.<br/><br/> A Contoso não implementou totalmente o DevOps para toda a solução. Eles precisam pensar nisso para a implantação de serviços para o AKS, funções e Serviços de Aplicativos.



### <a name="migration-process"></a>Processo de migração

1. A Contoso provisiona o ACR, o AKS e o CosmosDB.
2. Eles provisionam a infraestrutura para a implantação, incluindo o aplicativo Web, a conta de armazenamento, a função e a API do Azure. 
3. Depois que a infraestrutura estiver em vigor, eles criarão suas imagens de contêiner de microsserviços usando o VSTS, que as envia por push para o ACR.
4. A Contoso implantará esses microsserviços no AKS usando um script do PowerShell.
5. Por fim, eles implantarão o aplicativo Web e a função do Azure.

    ![Processo de migração](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Simplifica o gerenciamento, a implantação e as operações do Kubernetes. Fornece um serviço de orquestração de contêiner do Kubernetes totalmente gerenciado.  | O AKS é um serviço gratuito.  Pague apenas pelas máquinas virtuais, pelo armazenamento associado e pelos recursos de rede consumidos. [Saiba mais](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Funções do Azure](https://azure.microsoft.com/services/functions/) | Acelera o desenvolvimento com uma experiência de computação sem servidor orientada por evento. Dimensione sob demanda.  | Pague apenas pelos recursos consumidos. O plano é cobrado com base no consumo de recursos e execuções por segundo. [Saiba mais](https://azure.microsoft.com/pricing/details/functions/).
[Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) | Armazena imagens para todos os tipos de implantações de contêiner. | Custo com base em recursos, armazenamento e duração de uso. [Saiba mais](https://azure.microsoft.com/pricing/details/container-registry/).
[Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/containers/) | Crie, implante e dimensione rapidamente aplicativos de API, móveis e Web de nível corporativo em execução em qualquer plataforma. | Os Planos do Serviço de Aplicativo são cobrados por segundo. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você (e a Contoso) precisa para executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | Você já deve ter criado uma assinatura quando realizou a avaliação no primeiro artigo desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.
**Pré-requisitos de desenvolvedor** | A Contoso precisa das ferramentas a seguir em uma estação de trabalho de desenvolvedor:<br/><br/> - [Visual Studio 2017 Community Edition: versão 15.5](https://www.visualstudio.com/)<br/><br/> Carga de trabalho .NET habilitada.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [PowerShell do Azure](https://azure.microsoft.com/downloads/)<br/><br/> [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) configurado para usar Contêineres do Windows.



## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar o AKS e o ACR**: a Contoso provisiona o cluster do AKS gerenciado e o Registro de Contêiner do Azure usando o PowerShell
> * **Etapa 2: Criar contêineres do Docker**: eles configuram a CI para contêineres do Docker usando o VSTS e as envia por push para o ACR.
> * **Etapa 3: Implantar microsserviços de back-end**: eles implantam o restante da infraestrutura que será utilizada por microsserviços de back-end.
> * **Etapa 4: Implantar a infraestrutura de front-end**: eles implantam a infraestrutura de front-end, incluindo o armazenamento de blobs para os telefones de animais de estimação, o Cosmos DB e a API de Pesquisa Visual.
> * **Etapa 5: Migrar o back-end**: eles implantam microsserviços e executam no AKS, para migrar o back-end.
> * **Etapa 6: Publicar o front-end**: eles publicam o aplicativo SmartHotel para o Serviço de Aplicativo do Azure e o Aplicativo de funções que será chamado pelo serviço de animais de estimação.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Etapa 1: Provisionar um cluster do AKS e o ACR

A Contoso executa um script de implantação para criar o cluster Kubernetes gerenciado usando o AKS e o Registro de Contêiner do Azure.

- As instruções desta seção usam o repositório **SmartHotel360-Azure-backend**.
- O repositório do GitHub **SmartHotel360-Azure-backend** contém todo o software para essa parte da implantação.

Eles provisionam da seguinte maneira:

1. Antes de começar, a Contoso garante que todo o software de pré-requisito está instalado no computador de desenvolvimento sendo usado. 
2. Eles clonam o repositório localmente para o computador de desenvolvimento usando o Git.

    **clone do git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  A Contoso abre a pasta usando o Visual Studio Code e move para o diretório **/implantar/k8s**, que contém o script **aks-gen-env.ps1**.
4. Eles executam o script para criar o cluster Kubernetes gerenciado usando o AKS e o Registro de Contêiner.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  Com o arquivo aberto, eles atualizam o parâmetro $location para **eastus2** e salvam o arquivo.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. Eles clicam em **Exibir** > **Terminal Integrado** para abrir o terminal integrado no Código.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. No terminal integrado do PowerShell, eles entram no Azure usando o comando Connect-AzureRmAccount. [Saiba mais](https://docs.microsoft.com/powershell/azure/get-started-azureps) sobre como começar a trabalhar com o PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. Eles autenticam a CLI do Azure executando o comando **az login** e seguindo as instruções para autenticar usando o navegador da Web. [Saiba mais](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) sobre o logon com a CLI do Azure.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. Eles executam o seguinte comando, passando o nome do grupo de recursos de ContosoRG, o nome do cluster AKS smarthotel-aks-eus2 e o nome do novo Registro.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. O Azure cria outro grupo de recursos, que contém os recursos para o cluster do AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. Depois que a implantação é concluída, a Contoso instala a ferramenta de linha de comando **kubectl**. A ferramenta já está instalada no Azure CloudShell.

    **az aks install-cli**

12. Eles verificam a conexão com o cluster executando o comando **kubectl get nodes**. O nó tem o mesmo nome da VM no grupo de recursos criado automaticamente.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. Eles executam o comando a seguir para iniciar o Painel do Kubernetes: 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. Uma guia do navegador é aberta no Painel. Essa é uma conexão por túnel usando a CLI do Azure. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Etapa 2: Criar um contêiner do Docker

### <a name="create-a-vsts-and-build"></a>Criar um VSTS e compilar

A Contoso cria um projeto do VSTS e configura um Build de CI para criar o contêiner e, em seguida, o envia por push para o ACR. As instruções nesta seção usam o repositório [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).

1. Do visualstudio.com, eles criam uma nova conta (**contosodevops360.visualstudio.com**) e a configuram para usar o Git.

2. Eles criam um novo projeto (**smarthotelrefactor**) usando o Git para controle de versão e o Agile para o fluxo de trabalho.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Eles importarem o repositório do GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. Em **Build e Versão**, eles criam uma nova definição usando o Git do VSTS como uma fonte, do repositório **smarthotel** importado. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Eles escolhem iniciar com um pipeline vazio.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Eles selecionam **Versão Prévia do Linux Hospedada** para a definição de build.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Na **Fase 1**, adicionam uma tarefa **Docker Compose**. Essa tarefa cria o Docker Compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Eles repetem e adicionam outra tarefa **Docker Compose**. Esta envia por push os contêineres para o ACR.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Eles selecionam a primeira tarefa (para compilar) e configuram a compilação com a assinatura do Azure, a autorização e o ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Eles especificam o caminho do arquivo**docket-compose.yaml** na pasta **src** do repositório. Eles escolhem criar imagens de serviço e incluem a marcação mais recente. Quando a ação muda para **Criar imagens de serviço**, o nome da tarefa do VSTS muda para **Criar serviços automaticamente**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Agora, a Contoso configura a segunda tarefa do Docker (para enviar por push). Eles selecionam a assinatura e o ACR **smarthotelacreus2**. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Novamente, eles inserem o arquivo para o arquivo docker-compose.yaml e selecionam **Enviar imagens de serviço por push** e incluem a marcação mais recente. Quando a ação muda para **Enviar imagens de serviço por push**, o nome da tarefa do VSTS muda para **Enviar serviços por push automaticamente**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Com as tarefas do VSTS configuradas, a Contoso salva a definição de build e inicia o processo de build.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Eles clicam no trabalho de build para verificar o progresso.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Após a conclusão do build, o ACR mostra os novos repositórios, que são populados com os contêineres usados pelos microsserviços.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Etapa 3: Implantar microsserviços de back-end

Com o cluster do AKS criado e o build das imagens do Docker, a Contoso agora implanta o restante da infraestrutura que será utilizada pelos microsserviços de back-end.

- As instruções na seção usam o repositório [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Na pasta **/deploy/k8s/arm** há um único script para criar todos os itens. 

Eles implantam da seguinte maneira:

1. A Contoso usa o arquivo deploy.cmd para implantar os recursos do Azure no grupo de recursos ContosoRG e a região EUS2, digitando o seguinte comando:

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![Implantar o back-end](./media/contoso-migration-rebuild/backend1.png)

2. Eles capturam a cadeia de conexão de cada banco de dados para ser usada posteriormente.

    ![Implantar o back-end](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Etapa 4: Implantar a infraestrutura de front-end

A Contoso precisa implantar a infraestrutura que será usada pelos aplicativos front-end. Eles criam um contêiner de armazenamento de blobs para armazenar as imagens de animais de estimação, o banco de dados do Cosmos para armazenar documentos com as informações de animais de estimação e a API de Pesquisa Visual para o site. 

As instruções desta seção usam o repositório [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web).

### <a name="create-storage-containers"></a>Criar contêineres de armazenamento

1.  No portal do Azure, a Contoso abre a conta de armazenamento que foi criada e clica em **Blobs**.
2.  Eles criam um novo contêiner (**Pets**) com o nível de acesso público definido como o contêiner. Os usuários carregarão as fotos de seu animal de estimação para esse contêiner.

    ![Armazenamento de blobs](./media/contoso-migration-rebuild/blob1.png)

3. Eles criam um segundo novo contêiner chamado **settings**. Um arquivo com todas as configurações de aplicativo front-end será colocado nesse contêiner.

    ![Armazenamento de blobs](./media/contoso-migration-rebuild/blob2.png)

4. Eles capturam os detalhes de acesso da conta de armazenamento em um arquivo de texto para referência futura.

    ![Armazenamento de blobs](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Provisionar um banco de dados do Cosmos

A Contoso provisiona um banco de dados do Cosmos a ser usado para obter informações de animais de estimação.

1. Eles criam um **Azure Cosmos DB** no Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Eles especificam um nome (**contosomarthotel**), selecionam a API SQL e a colocam no grupo de recursos de produção ContosoRG, na região Leste dos EUA 2 principal.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Eles adicionam uma nova coleção ao banco de dados, com a capacidade e a taxa de transferência padrão.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Eles anotam as informações de conexão do banco de dados para referência futura. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Provisionar Pesquisa Visual Computacional

A Contoso provisiona a API da Pesquisa Visual Computacional. A API será chamada pela função, para avaliar imagens carregadas pelos usuários.

1. Eles criam uma instância de **Pesquisa Visual Computacional** no Azure Marketplace.

     ![Visual Computacional](./media/contoso-migration-rebuild/vision1.png)

2. Eles provisionam a API (**smarthotelpets**) no grupo de recursos de produção ContosoRG, na região Leste dos EUA 2 principal.

    ![Visual Computacional](./media/contoso-migration-rebuild/vision2.png)

3. Eles salvam as configurações de conexão para a API em um arquivo de texto para referência posterior.

     ![Visual Computacional](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Etapa 5: Implantar serviços de back-end no Azure

Agora, a Contoso precisa implantar o controlador de entrada NGINX para permitir o tráfego de entrada para os serviços e, em seguida, implantar os microsserviços no cluster de AKS.

As instruções nesta seção usam o repositório [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).



1. Eles usam o Visual Studio Code para atualizar o arquivo **/deploy/k8s/config_loal.yml**. Eles atualizam as várias conexões de banco de dados com base nas informações que anotaram anteriormente.

     ![Implantar microsserviços](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Algumas das definições de configuração (por exemplo Active Directory B2C) não são abordadas neste artigo. Há mais informações sobre essas configurações no repositório.

2. O arquivo deploy.ps1 exclui todo o conteúdo do cluster (com exceção do controlador de entrada e de saída) e implanta os microsserviços. Eles o executam da seguinte maneira:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. Eles executam o comando a seguir para verificar o status dos serviços:

    ```
    kubectl get services
    ```
4. Eles abrem o painel do Kubernetes para examinar a implantação:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Etapa 6: Publicar o front-end

Como etapa final, a Contoso publica o aplicativo SmartHotel no Serviço de Aplicativo do Azure e no Aplicativo de funções que é chamado pelo serviço de animais de estimação.

As instruções nesta seção usam o [repositório SmartHotel-public-web.](https://github.com/Microsoft/SmartHotel360-public-web) repositório.

### <a name="set-up-web-app-to-use-contoso-resources"></a>Configurar o aplicativo Web para usar os recursos da Contoso

1. A Contoso clona o repositório localmente para o computador de desenvolvimento usando o Git:

    **clone do git https://github.com/Microsoft/SmartHotel360-public-web.git**

2. No Visual Studio, eles abrem a pasta para mostrar todos os arquivos no repositório.

    ![Publicar o front-end](./media/contoso-migration-rebuild/front-publish1.png)

3. Eles fazem as alterações de configuração necessárias na configuração padrão.

    - Quando o aplicativo Web é iniciado, ele procura a configuração de aplicativo **SettingsUrl**.
    - Essa variável deve conter uma URL para um arquivo de configuração.
    - Por padrão, a configuração usada é um ponto de extremidade público.

4. Eles atualizam o arquivo **/config-sample.json/sample.json**. Esse é o arquivo de configuração para a Web ao usar o ponto de extremidade público.

    - Eles editam as seções **urls** e **pets_config** com os valores dos pontos de extremidade da API do AKS, das contas de armazenamento e do banco de dados do Cosmos. 
    - As URLs devem corresponder ao nome DNS do novo aplicativo Web que Contoso criará.
    - Para a Contoso, isso é **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Publicar o front-end](./media/contoso-migration-rebuild/front-publish2.png)

5. Depois que o arquivo é atualizado, eles o renomeiam como **smarthotelsettingsurl** e o carregam no blob de armazenamento criado anteriormente.

     ![Publicar o front-end](./media/contoso-migration-rebuild/front-publish3.png)

6. Eles clicam no arquivo para obter a URL. Essa URL é usada pelo aplicativo quando ele começa a baixar o arquivo de configuração.

    ![Publicar o front-end](./media/contoso-migration-rebuild/front-publish4.png)

7. Eles atualizam a **SettingsUrl** nos arquivos **appsettings.Production.json** para a URL do novo arquivo.

    ![Publicar o front-end](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Implantar o site para o Serviço de Aplicativo do Azure

Agora, a Contoso pode publicar seu site.


1. Eles habilitam o monitoramento do Application Insights no Visual Studio 2017. Para fazer isso, eles selecionam o projeto **PublicWeb** no Gerenciador de Soluções e pesquisam por **Adicionar Application Insights**. Eles registram o aplicativo com a instância do Application Insight que foi criada quando eles implantaram a infraestrutura.

    ![Publicar o site](./media/contoso-migration-rebuild/deploy-website1.png)

2. No Visual Studio, eles conectam o projeto PublicWeb ao App Insights e atualizam para mostrar que está configurado.
 
    ![Publicar o site](./media/contoso-migration-rebuild/deploy-website2.png)

3. No Visual Studio, eles criam e publicam o aplicativo Web.

    ![Publicar o site](./media/contoso-migration-rebuild/deploy-website3.png)

5. Eles especificam um nome do aplicativo o colocam no grupo de recursos de produção **ContosoRG**, na região Leste dos EUA 2 principal.

    ![Publicar o site](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

1. Eles usam o Visual Studio para criar e publicar a função. Para fazer isso, eles com o botão direito do mouse em **PetCheckerFunction** > **Publicar**. Em seguida, eles criam uma nova função de Serviço de Aplicativo.

     ![Implantar a função](./media/contoso-migration-rebuild/function1.png)

2. Eles especificam o nome **smarthotelpetchecker** e o colocam no grupo de recursos ContosoRG e adicionam um novo plano de serviço de aplicativo.

     ![Implantar a função](./media/contoso-migration-rebuild/function2.png)

3. Eles selecionam a conta de armazenamento e criam a função.

    ![Implantar a função](./media/contoso-migration-rebuild/function3.png)

4. A implantação é iniciada com o provisionamento do aplicativo de funções no Azure. Em **Publicar**, a Contoso adiciona as configurações de aplicativo para armazenamento, o banco de dados do Cosmos e a API da Pesquisa Visual Computacional.

    ![Implantar a função](./media/contoso-migration-rebuild/function4.png)

5. Para executar a função localmente primeiro, eles atualizam as configurações em **PetCheckerFunction/local.settings.json**.

    ![Implantar a função](./media/contoso-migration-rebuild/function5.png)

6. Depois que a função é implantada, ela aparece no portal do Azure, com o status **Em Execução**.

    ![Implantar a função](./media/contoso-migration-rebuild/function6.png)


7. Eles navegam até o aplicativo para testar se aplicativo Pet Checker está funcionando como esperado em [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets).
8. Eles clicam no avatar para carregar uma imagem.

    ![Implantar a função](./media/contoso-migration-rebuild/function7.png)

9. A primeira foto que desejam verificar é de um cachorro pequeno.

    ![Implantar a função](./media/contoso-migration-rebuild/function8.png)

10. O aplicativo retornar uma mensagem de aceitação.

    ![Implantar a função](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

- A Contoso precisa garantir que seus novos bancos de dados estejam seguros. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- O aplicativo precisa ser atualizado para usar SSL com certificados. A instância de contêiner deve ser reimplantada para responder em 443.
- Eles devem considerar usar o KeyVault para proteger os segredos para seus aplicativos do Service Fabric. [Saiba mais](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Backup e recuperação de desastre

- A Contoso precisa examinar os requisitos de backup do Banco de Dados SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Eles devem considerar a implementação de grupos de failover SQL para fornecer failover regional para o banco de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Eles podem aproveitar a replicação geográfica para o SKU premium ACR. [Saiba mais](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Depois que todos os recursos estiverem implantados, a Contoso deverá atribuir marcações ao Azure com base no seu [planejamento de infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Todo o licenciamento se baseia no custo dos serviços de PaaS que a Contoso está consumindo. Isso será deduzido do EA.
- A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso recompila o aplicativo SmartHotel no Azure. Eles recompilaram a VM de front-end do aplicativo local para aplicativos Web dos Serviços de Aplicativos do Azure. Eles criaram o back-end do aplicativo usando microsserviços implantados em contêineres gerenciados pelo AKS (Serviço de Kubernetes do Azure). Eles aprimoraram a funcionalidade do aplicativo com um aplicativo de foto de animal de estimação.




