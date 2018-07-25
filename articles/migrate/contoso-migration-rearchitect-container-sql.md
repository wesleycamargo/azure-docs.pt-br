---
title: Recriar a arquitetura de um aplicativo Contoso em um contêiner do Azure e o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como a Contoso recria a arquitetura de um aplicativo nos Contêineres do Windows do Azure e no Banco de Dados SQL do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003190"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migração da Contoso: recriar a arquitetura de um aplicativo local para um contêiner do Azure e o Banco de Dados SQL do Azure

Este artigo demonstra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel no Azure. Eles migram a VM de front-end do aplicativo para um contêiner do Windows do Azure e o banco de dados do aplicativo para um Banco de Dados SQL do Azure.

Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Os cenários crescem em complexidade e adicionaremos artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais](contoso-migration-assessment.md)  | Mostra como a Contoso executa uma avaliação de um aplicativo SmartHotel de dois níveis no local em execução no VMware. A Contoso avalia as VMs de aplicativos com o serviço [ do Azure Migrate ](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [ Assistente de Migração de Banco de Dados ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[ Artigo 4: Reorganizar um aplicativo para VMs do Azure e uma Instância Gerenciada do SQL ](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso executa uma migração de elevação e troca para o Azure no aplicativo SmartHotel. A Contoso migra a VM frontend do aplicativo usando [ Azure Site Recovery ](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados do aplicativo para uma Instância Gerenciada do SQL, usando o [ Serviço de Migração do Banco de Dados do Azure ](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[ Artigo 5: Re-hospede um aplicativo para VMs do Azure ](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra as VMs do aplicativo SmartHotel usando somente o Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo para VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo e o serviço Database Migration para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[ Artigo 7: Mudança de host de um aplicativo Linux para VMs do Azure ](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso faz uma migração de elevação e deslocamento do aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery | Disponível
[Artigo 8: Novo host a um aplicativo do Linux para as VMs do Azure e o Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e migra o banco de dados do aplicativo para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo para um aplicativo Web do Azure e para o Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Disponível
[Artigo 10: Refatorar um aplicativo Linux para aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
Artigo 11: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Este artigo.
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível

Neste artigo, a Contoso migra o Windows de duas camadas. Aplicativo NET SmartHotel em execução nas VMs do VMware para o Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou de perto com seus parceiros de negócios para entender o que eles querem alcançar com essa migração:

- **Abordar o crescimento dos negócios**: a Contoso está crescendo e, como resultado, há pressão sobre seus sistemas e infraestrutura locais.
- **Aumentar a eficiência**: a Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.
- **Custos**: a Contoso deseja minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração.

**Metas** | **Detalhes**
--- | --- 
**Solicitações de aplicativo** | O aplicativo no Azure permanecerá tão importante quanto é hoje.<br/><br/> Ele deve ter os mesmos recursos de desempenho que tem atualmente no VMWare.<br/><br/> Eles querem interromper o suporte ao Windows Server 2008 R2, no qual o aplicativo atualmente é executado e estão dispostos a investir no aplicativo.<br/><br/> Eles desejam sair do SQL Server 2008 R2 para uma plataforma de Banco de Dados PaaS moderna, que minimizará a necessidade de gerenciamento.<br/><br/> A Contoso deseja aproveitar seus investimentos em licenciamento do SQL Server e o Software Assurance quando possível.<br/><br/> Eles querem ser capazes de escalar verticalmente a camada de Web do aplicativo.
**Limitações** | O aplicativo consiste em um aplicativo ASP.NET e um serviço WCF em execução na mesma VM. Eles querem dividir isso em dois aplicativos Web usando o Serviço de Aplicativo do Azure. 
**Solicitações do Azure** | Eles querem mover o aplicativo para o Azure e executá-lo em um contêiner para estender a vida útil do aplicativo. Eles não querem iniciar completamente do zero para implementar o aplicativo no Azure. 

## <a name="solution-design"></a>Design da solução

Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo local SmartHotel é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


### <a name="proposed-architecture"></a>Arquitetura proposta

- Para a camada de banco de dados do aplicativo, a Contoso comparou o Banco de Dados SQL do Azure com o SQL Server usando [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Eles decidiram usar o Banco de Dados SQL do Azure por alguns motivos:
    - O Banco de Dados SQL do Azure é um serviço gerenciado de banco de dados relacional. Ele oferece desempenho previsível em vários níveis de serviço, com administração quase zero. As vantagens incluem a escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente interna e a escalabilidade e a disponibilidade globais.
    - Eles podem aproveitar o DMA (Assistente de Migração de Dados) leve para avaliar e migrar o banco de dados local para o SQL do Azure.
    - Com o Software Assurance, eles podem trocar as licenças existentes por tarifas com desconto em um Banco de Dados SQL, usando o Benefício Híbrido do Azure para SQL Server. Isso pode proporcionar uma economia de até 30%.
    - O Banco de Dados SQL fornece uma série de recursos de segurança, incluindo o mascaramento de dados dinâmico Always Encrypted e detecção de ameaça de segurança de nível de linha.
- Para a camada do aplicativo Web, eles decidiram convertê-la para o Contêiner do Windows usando o Visual Studio.
    - Eles implantarão o aplicativo usando o Azure Service Fabric e extrairão a imagem de contêiner do Windows do ACR (Registro de Contêiner do Azure).
    - Um protótipo para estender o aplicativo para incluir a análise de sentimento será implementado como outro serviço no Service Fabric, conectado ao Cosmos DB.  Isso lerá as informações de Tweets e exibirá no aplicativo.

    ![Arquitetura de cenário](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Análise de solução
A Contoso avalia seu projeto proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | O código do aplicativo SmartHotel precisará ser alterado para a migração para o Azure Service Fabric. No entanto, o esforço é mínimo, usando as ferramentas do SDK do Service Fabric para as alterações.<br/><br/> Com a mudança para o Service Fabric, eles podem começar a desenvolver microsserviços para adicionar ao aplicativo rapidamente ao longo do tempo, sem riscos para a base de código original.<br/><br/> Os Contêineres do Windows oferecem os mesmos benefícios que os contêineres em geral. Eles melhoram a agilidade, a portabilidade e o controle.<br/><br/> Eles podem aproveitar seus investimentos no Software Assurance usando o Benefício Híbrido do Azure para o SQL Server e para o Windows Server.<br/><br/> Após a migração eles não precisarão mais dar suporte ao Windows Server 2008 R2. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> Eles podem configurar a camada da Web do aplicativo com várias instâncias, para que ela não seja mais um ponto único de falha.<br/><br/> Eles não serão mais dependentes do antigo SQL Server 2008 R2.<br/><br/> O Banco de Dados SQL é compatível com os requisitos técnicos da Contoso. Eles avaliaram o banco de dados local usando o Assistente de Migração de Banco de Dados e constataram que é compatível.<br/><br/> O Banco de Dados SQL tem tolerância a falhas interna que a Contoso não precisa configurar. Isso garante que a camada de dados não é mais um ponto único de failover.
**Contras** | Contêineres são mais complexos do que outras opções de migração. A curva de aprendizado em contêineres pode ser um problema para a Contoso.  Eles apresentam um novo nível de complexidade que fornece bastante valor apesar da curva.<br/><br/> A equipe de operações da Contoso precisará se esforçar para entender e dar suporte ao Azure, aos contêineres e aos microsserviços para o aplicativo.<br/><br/> Se eles usarem o Assistente de Migração de Dados em vez do Serviço de Migração de Dados para migrar seu banco de dados, a Contoso não terá a infraestrutura pronta para migrar bancos de dados em escala.



### <a name="migration-process"></a>Processo de migração

1. A Contoso provisiona o cluster do Azure Service Fabric para Windows.
2. Eles provisionam uma instância do SQL do Azure e migram o banco de dados SmartHotel para ela.
3. Eles convertem a VM de camada da Web para um contêiner do Docker usando as ferramentas do SDK do Service Fabric.
4. Eles conectam o cluster do Service Fabric e o ACR e implantam o aplicativo usando o Azure Service Fabric.

    ![Processo de migração](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração do banco de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Eles usará DMA para avaliar e detectar problemas de compatibilidade que podem afetar sua funcionalidade de banco de dados no Azure. DMA avalia a paridade de recursos entre SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta que pode ser baixada gratuitamente.
[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Um serviço de banco de dados de nuvem relacional inteligente e totalmente gerenciado. | Custo com base em recursos, taxa de transferência e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) | Armazene imagens para todos os tipos de implantações de contêiner. | Custo com base em recursos, armazenamento e duração de uso. [Saiba mais](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Crie e opere aplicativos sempre ativos, escalonáveis e distribuídos | Custos com base no tamanho, localização e duração dos nós de computação. [Saiba mais](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você (e a Contoso) precisa para executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | Você já deve ter criado uma assinatura quando realizou a avaliação no primeiro artigo desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.
**Pré-requisitos de desenvolvedor** | A Contoso precisa das ferramentas a seguir em uma estação de trabalho de desenvolvedor:<br/><br/> - [Visual Studio 2017 Community Edition: versão 15.5](https://www.visualstudio.com/)<br/><br/> - Carga de trabalho .NET habilitada.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 ou posterior](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) ou Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) configurado para usar Contêineres do Windows.



## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar uma instância do Banco de Dados SQL no Azure**: a Contoso provisiona uma instância do SQL no Azure. Após a VM da Web de front-end ser migrada para um contêiner do Azure, a instância do contêiner com o front-end da Web do aplicativo apontará para esse banco de dados.
> * **Etapa 2: Provisionar o Azure Service Fabric**: eles provisionam um Cluster do Service Fabric.
> * **Etapa 4: Migrar o banco de dados com o DMA**: eles migram o banco de dados de aplicativo com o Assistente de Migração de Banco de Dados.
> * **Etapa 5: Converter o aplicativo em um contêiner**: eles convertem o aplicativo em um contêiner usando o Visual Studio e SDK Tools.
> * **Etapa 6: Publicar o aplicativo**: eles publicam o aplicativo para o ACR e o Cluster do Service Fabric.
> * **Etapa 7: Estender o aplicativo**: depois que o aplicativo está público, eles o estendem para aproveitar os recursos do Azure e o publicam novamente para o Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Etapa 1: Provisionar um Banco de Dados SQL do Azure

1. Eles escolhem criar um Banco de Dados SQL no Azure. 

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Eles especificam um nome de banco de dados para corresponder ao banco de dados em execução na VM local (**SmartHotel.Registration**). Eles colocam o banco de dados no grupo de recursos ContosoRG. Esse é o grupo de recursos que eles usam para recursos de produção no Azure.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Eles configuram uma nova instância do SQL Server (**sql-smarthotel-eus2**) na região primária.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Eles definem o tipo de preço para corresponder às suas necessidades de servidor e banco de dados. E escolhem economizar dinheiro com o Benefício Híbrido do Azure porque já tem uma licença do SQL Server.
5. Para dimensionamento eles usam a compra baseada em VCore e definem os limites para os requisitos esperados.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Em seguida, eles criam a instância de banco de dados.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Depois que a instância é criada, eles abrem o banco de dados e anotam os detalhes que serão necessários quando usarem o Assistente de Migração de Banco de dados para a migração.

    ![Provisionar SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Precisa de mais ajuda?**

- [Obtenha ajuda ](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) o provisionamento de um Banco de Dados SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) os limites de recursos do VCore.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Etapa 2: Criar um ACR e provisionar um contêiner do Azure

O contêiner do Azure é criado usando os arquivos exportados da VM da Web. O contêiner é hospedado no ACR (Registro de Contêiner do Azure).


1. A Contoso cria um Registro de Contêiner no portal do Azure.

     ![Registro de Contêiner](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Eles fornecem um nome para o Registro (**contosoacreus2**) e o colocam na região primária, no grupo de recursos que usam para seus recursos de infraestrutura. Eles habilitam o acesso para usuários administrativos e o definem como um SKU premium para que possam aproveitar a replicação geográfica.

    ![Registro de Contêiner](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Etapa 3: Provisionar o Azure Service Fabric

O contêiner SmartHotel será executado no clister do Azure Service Fabric. A Contoso cria o Cluster do Service Fabric da seguinte maneira:

1. Crie um recurso do Service Fabric do Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. Em **Básico**, eles fornecem um nome exclusivo de DS para o cluster e as credenciais para acessar a VM local. Eles colocam o recurso no grupo de recursos de produção (**ContosoRG**) na região Leste dos EUA 2 primária.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. Em **Configuração do tipo de nó**, eles inserem um nome de tipo de nó, as configurações de durabilidade, o tamanho da VM e os pontos de extremidade do aplicativo.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. Em **Criar cofre de chaves**, eles criam um novo cofre de chaves em seu grupo de recursos de infraestrutura para abrigar o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. Em **Políticas de Acesso**, eles habilitam o acesso às máquinas virtuais para implantar o cofre de chaves.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Eles especificam um nome para o certificado.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Na página de resumo, eles copiam o link que é usado para baixar o certificado. Eles precisam disso para se conectar ao cluster do Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Após a validação ser bem-sucedida, eles provisionam o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. No Assistente de Importação de Certificados, eles importam o certificado baixado para computadores de desenvolvimento. O certificado é usado para autenticar o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Depois que o cluster é provisionado, eles se conectam ao Gerenciador de Cluster do Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Eles precisam selecionar o certificado correto.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. As cargas do Service Fabric Explorer e o Administrador da Contoso podem gerenciar o cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>Etapa 3: Migrar o banco de dados com o DMA

A Contoso migrará o banco de dados SmartHotel usando o DMA.

### <a name="install-dma"></a>Instalar o DMA

1. Eles fazem o download da ferramenta do [ Centro de Download da Microsoft ](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a instalação (DownloadMigrationAssistant.msi) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

### <a name="configure-the-firewall"></a>Configurar o firewall

Para se conectar ao Banco de Dados SQL do Azure, é necessária uma regra de firewall.

1. Nas propriedades **Firewall e redes virtuais** do banco de dados, eles permitem o acesso aos serviços do Azure e adicionam uma regra para o endereço IP do cliente da VM do SQL Server local.
2. Uma regra de firewall do nível do servidor é criada.

    ![Firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Precisa de mais ajuda?

[Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) como criar e gerenciar as regras de firewall para o Banco de Dados SQL do Azure.

### <a name="migrate"></a>Migrar

1. No DMA, crie um novo projeto (**SmartHotelDB**) e selecione **Migração** 
2. Eles escolhem o tipo de servidor de origem como **SQL Server** e o destino como **Banco de Dados SQL do Azure**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Nos detalhes da migração, eles adicionam **SQLVM** como o servidor de origem e o banco de dados **SmartHotel.Registration**. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Eles recebem um erro que parece estar associado à autenticação. No entanto, após investigar, o problema é o ponto (.) no nome do banco de dados. Como solução alternativa, eles decidiram provisionar um novo banco de dados SQL usando o nome **SmartHotel-Registration** para resolver o problema. Quando eles executam o DMA novamente, são capazes de selecionar **SmartHotel-Registration** e continuar com o assistente.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. Em **Selecionar Objetos**, selecionam as tabelas de banco de dados e gerar um script SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Depois que o DMS cria o script, eles clicam em **Implantar esquema**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. O DMA confirma que a implantação foi realizada com êxito.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Agora eles iniciam a migração.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Após a migração ser concluída, a Contoso pode verificar se o banco de dados está em execução na instância SQL do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Eles excluem o banco de dados SQL extra **SmartHotel.Registration** no portal do Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>Etapa 4: Converter o aplicativo em um contêiner

O aplicativo local é um aplicativo tradicional de três camadas:

- Ele contém WebForms e um Serviço WCF conectando ao SQL Server.
- Ele usa o Entity Framework para se integrar aos dados no banco de dados SQL, expondo-os por meio de um serviço WCF.
- O aplicativo WebForms interage com o serviço WCF.

A Contoso converterá o aplicativo em um contêiner usando o Visual Studio e SDK Tools da seguinte maneira:

1. Eles clonam o repositório localmente em um computador de desenvolvedor:

    **clone do git https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Usando o Visual Studio, eles abrem o arquivo da solução (SmartHotel.Registration.sln) no diretório **SmartHotel360-internal-booking-apps\src\Registration** do repositório local.  Dois aplicativos são mostrados. O front-end da Web SmartHotel.Registration.Web e o aplicativo do serviço WCF SmartHotel.Registration.WCF.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Eles clicam com o botão direito do mouse no aplicativo Web > **Adicionar** > **Suporte de Orquestrador de Contêineres**.
4. Em **Adicionar Suporte a Orquestra de Contêineres**, eles selecionam **Service Fabric**.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. A Contoso repete o processo para o aplicativo SmartHotel.Registration.WCF.
6. Agora, a Contoso verifica como a solução foi alterada.

    - O novo aplicativo é **SmartHotel.RegistrationApplication/**
    - Ele contém dois serviços: **SmartHotel.Registration.WCF** e **SmartHotel.Registration.Web**.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. O Visual Studio criou o arquivo do Docker e baixou as imagens necessárias localmente para o computador de desenvolvedor.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Um arquivo de manifesto (**ServiceManifest.xml**) é criado e aberto pelo Visual Studio. Esse arquivo informa ao Service Fabric como configurar o contêiner quando ele for implantado no Azure.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Outro arquivo de manifesto (**ApplicationManifest.xml) contém os aplicativos de configuração para os contêineres.

    ![Contêiner](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Etapa 5: Publicar o aplicativo


Por fim, a Contoso pode publicar o aplicativo para o ACR e o cluster do Service Fabric.

> [!NOTE]
> Foram feitas algumas alterações relacionadas ao cluster do Service Fabric no aplicativo SmartHotel. Você pode baixar o código de aplicativo original e o modernizado do [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). O arquivo alterado é o **AppliationModern/ApplicationParameters/Cloud.xml**.


1. No Visual Studio, atualizam a cadeia de conexão para conectar o aplicativo ao Banco de Dados SQL do Azure. A cadeia de conexão pode ser encontrada no banco de dados no portal do Azure.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. A Contoso publica o aplicativo no Service Fabric usando o Visual Studio. Eles clicam com o botão direito do mouse no Aplicativo do Service Fabric > **Publicar**.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Eles selecionam a assinatura, o ponto de extremidade de conexão e o ACR. Em seguida, clique em **Publicar**.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Depois que a implantação for concluída, o SmartHotel estará executando o Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Para se conectar ao aplicativo, a Contoso direciona o tráfego para o endereço IP público do Azure Load Balanceer na frente de seus nós do Service Fabric.

    ![Publicar](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Etapa 6: Estender o aplicativo e publicar novamente

Depois que o banco de dados e o aplicativo SmartHotel estão em execução no Azure, a Contoso deseja estender o aplicativo.

- Os desenvolvedores da Contoso estão criando um protótipo de um novo aplicativo .NET Core, que será executado no cluster do Service Fabric.
- O aplicativo será usado para extrair dados de sentimento do CosmosDB.
- Esses dados serão ser na forma de Tweets que são processados usando uma função sem servidor do Azure e a API de Análise de Texto de Serviços Cognitivos.

### <a name="provision-azure-cosmos-db"></a>Provisionar um Azure Cosmos DB

Como primeira etapa, a Contoso provisiona um Azure Cosmos DB.

1. Eles criam um recurso do Azure Cosmos DB no Azure Marketplace.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Eles fornecem um nome de banco de dados (**contososmarthotel**), selecionam a API do SQL e colocam o recurso no grupo de recursos de produção, na região Leste dos EUA 2 primária.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. Em **Introdução**, eles selecionam **Data Explorer** e adicionam uma nova coleção.
4. Em **Adicionar Coleção**, eles fornecem IDs e definem a capacidade de armazenamento e a taxa de transferência.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. No portal, eles abrem o novo banco de dados > **Coleção** > **Documentos** e clicam em **Novo Documento**.
6. Eles colam o código JSON a seguir na janela do documento. Esses são dados de exemplo na forma de um único tweet.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Eles localizam o ponto de extremidade do Cosmos DB e a chave de autenticação. Eles são usados no aplicativo para se conectar à coleção. No banco de dados, clicam em **Chaves** e copiam o URI e a chave primária para o Bloco de Notas.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Atualizar o aplicativo de sentimento

Com o Cosmos DB provisionado, a Contoso pode configurar o aplicativo para se conectar a ele.

1. No Visual Studio, eles abrem o arquivo ApplicationModern\ApplicationParameters\cloud.xml no Gerenciador de Soluções.

    ![Aplicativo de sentimento](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Eles preenchem os dois parâmetros a seguir:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplicativo de sentimento](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Republicar o aplicativo

Depois de estender o aplicativo, a Contoso o republica para o Azure.

1. No portal, clicam com o botão direito do mouse no aplicativo do Service Fabric > **Publicar**.

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Eles selecionam a assinatura, o ponto de extremidade de conexão e o ACR. Em seguida, clique em **Publicar**.

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Depois que a implantação for concluída, o SmartHotel estará executando o Service Fabric. O console de Gerenciamento do Service Fabric agora mostra três serviços.

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. A Contoso pode clicar pelos serviços para ver que o aplicativo SentimentIntegration está funcionando

    ![Republicar](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa concluir estas etapas de limpeza:  

- Remova as VMs locais do inventário do vCenter.
- Remova as VMs das tarefas de backup locais.
- Atualize a documentação interna para mostrar os novos locais para o aplicativo SmartHotel. Mostre o banco de dados como em execução no Banco de Dados SQL do Azure e o front-end como em execução no Service Fabric.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.


## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

- A Contoso precisa garantir que seu novo banco de dados **SmartHotel-Registration** é seguro. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, eles devem atualizar o contêiner para usar SSL com certificados.
- Eles devem considerar usar o KeyVault para proteger os segredos para seus aplicativos do Service Fabric. [Saiba mais](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Backups

- A Contoso precisa examinar os requisitos de backup do Banco de Dados SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Eles devem considerar a implementação de grupos de failover para fornecer failover regional para o banco de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Eles podem aproveitar a replicação geográfica para o SKU premium ACR. [Saiba mais](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- A Contoso precisa considerar a implantação do aplicativo Web na região Leste dos EUA 2 e Centro dos EUA principal quando o Aplicativo Web para Contêineres estiver disponível. Eles poderiam configurar o Gerenciador de Tráfego para garantir o failover no caso de interrupções regionais.

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Depois que todos os recursos estiverem implantados, a Contoso deverá atribuir marcações ao Azure com base no seu [planejamento de infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Todo o licenciamento se baseia no custo dos serviços de PaaS que a Contoso está consumindo. Isso será deduzido do EA.
1. A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorou o aplicativo SmartHotel no Azure migrando a VM de front-end do aplicativo para o Service Fabric. Eles migraram o banco de dados do aplicativo para um Banco de Dados SQL do Azure.





