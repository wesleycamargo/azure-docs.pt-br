---
title: Refatorar um aplicativo Contoso migrando-o para o Azure Web App e o Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local migrando-o para um banco de dados do Azure Web App e do Azure SQL Server.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/03/2018
ms.author: raynew
ms.openlocfilehash: 370f90f0bbc2ebdb386aca5f47b909640271dbbf
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783041"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migração da Contoso: refatorar um aplicativo local para um aplicativo Web do Azure e Banco de Dados SQL do Azure

Este artigo demonstra como Contoso refatores seu aplicativo SmartHotel360 no Azure. Eles migram a VM de front-end do aplicativo para um aplicativo Web do Azure e o banco de dados do aplicativo para um Banco de Dados SQL do Azure.

Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Cenários aumentam em complexidade. Adicionaremos mais artigos com o tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da série de artigos, da estratégia de migração da Contoso e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar a infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | A Contoso executa uma avaliação de seu aplicativo do SmartHotel360 locais em execução no VMware. A Contoso avalia as VMs do aplicativo usando o serviço de Migrações para Azure e o banco de dados do SQL Server do aplicativo usando o Assistente de Migração de Dados. | Disponível
[Artigo 4: Hospedar novamente um aplicativo em uma Instância Gerenciada do Banco de Dados SQL e VM do Azure](contoso-migration-rehost-vm-sql-managed-instance.md) | A Contoso executa uma migração de elevação e troca para o Azure em seu aplicativo SmartHotel360 local. A Contoso migra a VM front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível   
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra suas VMs de aplicativos do SmartHotel360 para VMs do Azure usando o serviço de recuperação de sites. | Disponível
[Artigo 6: Hospedar novamente um aplicativo para VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo do SmartHotel360. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para as VMs do Azure, usando o Azure Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no Servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para as VMs do Azure usando o Azure Site Recovery e migra o banco de dados do aplicativo para uma instância do MySQL Server no Azure usando o MySQL Workbench. | Disponível
Artigo 9: Refatorar um aplicativo em um banco de dados do Azure Web App e do Azure SQL | A Contoso migra o aplicativo SmartHotel360 para um aplicativo Web do Azure e migra o banco de dados de aplicativos para uma instância do Azure SQL Server com o Assistente de Migração de Banco de Dados | Este artigo
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra seu aplicativo osTicket do Linux para um aplicativo Web do Azure em várias regiões do Azure usando o Gerenciador de Tráfego do Azure, integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível 
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local para o Visual Studio Team Services no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra seu aplicativo SmartHotel360 para o Azure. Em seguida, ela cria novamente a camada da Web do aplicativo como um contêiner do Windows em execução no Azure Service Fabric e o banco de dados com o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recria seu aplicativo SmartHotel360 usando um intervalo de recursos e serviços do Azure, incluindo o Serviço de Aplicativo do Azure, o Serviço Azuis de Kubernetes (AKS), as Funções do Azure, os Serviços Cognitivos do Azure e o Banco de Dados do Azure Cosmos. | Disponível

Neste artigo, a Contoso migra o Windows de duas camadas. NET SmartHotel360 o aplicativo em execução em VMs do VMware para Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Abordar o crescimento dos negócios**: a Contoso está crescendo e há pressão sobre os sistemas e a infraestrutura locais.
- **Aumentar a eficiência**: a Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.
- **Custos**: a Contoso deseja minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração.

**Requisitos** | **Detalhes**
--- | --- 
**Aplicativo** | O aplicativo no Azure permanecerá tão importante quanto é hoje.<br/><br/> Ele deve ter os mesmos recursos de desempenho que tem atualmente no VMWare.<br/><br/> A equipe não quer investir no aplicativo. Por enquanto, os administradores simplesmente movem o aplicativo com segurança para a nuvem.<br/><br/> A equipe deseja parar de oferecer suporte ao Windows Server 2008 R2, no qual o aplicativo é executado atualmente.<br/><br/> A equipe também deseja se afastar do SQL Server 2008 R2 para uma plataforma de banco de dados PaaS moderna, o que minimizará a necessidade de gerenciamento.<br/><br/> A Contoso deseja aproveitar seu investimento em licenciamento do SQL Server e o Software Assurance quando possível.<br/><br/> Além disso, a Contoso deseja reduzir o ponto único de falha na camada da web.
**Limitações** | O aplicativo consiste em um aplicativo ASP.NET e um serviço WCF em execução na mesma VM. Eles querem dividir isso em dois aplicativos Web usando o Serviço de Aplicativo do Azure. 
**As tabelas** | Contoso quer mover o aplicativo para o Azure, mas não quer executá-lo em VMs. A Contoso quer aproveitar os serviços de PaaS do Azure para as camadas da Web e de dados. 
**DevOps** | Contoso quer mover para um modelo de DevOps, usando o Visual Studio Team Services (VSTS) para suas construções e liberar pipelines.

## <a name="solution-design"></a>Design da solução

Depois de fixar as metas e os requisitos, a Contoso projeta e analisa uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que serão usados para migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo local do SmartHotel360 é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


### <a name="proposed-solution"></a>Solução proposta

- Para a camada de banco de dados do aplicativo, a Contoso comparou o Banco de Dados SQL do Azure com o SQL Server usando [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). A Contoso decidiu usar o Banco de Dados SQL do Azure por alguns motivos:
    - O Banco de Dados SQL do Azure é um serviço gerenciado de banco de dados relacional. Ele oferece desempenho previsível em vários níveis de serviço, com administração quase zero. As vantagens incluem a escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente interna e a escalabilidade e a disponibilidade globais.
    - A Contoso pode aproveitar o leve Assistente de Migração de Dados (DMA) para avaliar e migrar o banco de dados local para o Azure SQL.
    - Com o Software Assurance, a Contoso pode trocar licenças existentes por taxas com desconto em um banco de dados SQL, usando o Benefício Híbrido do Azure para SQL Server. Isso pode proporcionar uma economia de até 30%.
    - O Banco de Dados SQL fornece uma série de recursos de segurança, incluindo o mascaramento de dados dinâmico Always Encrypted e detecção de ameaça de segurança de nível de linha.
- Para a camada da Web do aplicativo, a Contoso decidiu usar o Serviço de Aplicativo do Azure. Esse serviço de PaaS permite isso para implantar o aplicativo com apenas algumas mudanças de configuração. A Contoso usará o Visual Studio para fazer a alteração e implantar dois aplicativos da Web. Um para o site e outro para o serviço WCF.
- Para atender aos requisitos de um pipeline de DevOps, a Contoso selecionou para usar o VSTS. Eles vão implantar o VSTS para gerenciamento de código-fonte (SCM) com Git repos. As compilações e versões automáticas serão usadas para criar o código e implantá-lo nos aplicativos da Web do Azure.
  
### <a name="solution-review"></a>Análise de solução
A Contoso avalia seu projeto proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | O código do aplicativo SmartHotel360 não precisará ser alterado para migração para o Azure.<br/><br/> A Contoso pode aproveitar seu investimento em Software Assurance usando o Benefício Híbrido do Azure para o SQL Server e o Windows Server.<br/><br/> Após a migração, o Windows Server 2008 R2 não precisará ser suportado. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> A Contoso pode configurar a camada da Web do aplicativo com várias instâncias para que ela não seja mais um ponto único de falha.<br/><br/> O banco de dados não dependerá mais do antigo SQL Server 2008 R2.<br/><br/> Banco de dados SQL suporta os requisitos técnicos. A Contoso avaliou o banco de dados local usando o Database Migration Assistant e descobriu que ele é compatível.<br/><br/> O Banco de Dados SQL tem tolerância a falhas interna que a Contoso não precisa configurar. Isso garante que a camada de dados não é mais um ponto único de failover.
**Contras** | Os Serviços de Aplicativos do Azure são compatíveis com apenas uma implantação de aplicativo para cada aplicativo Web. Isso significa que dois aplicativos Web devem ser provisionados (um para o site e outro para o serviço WCF).<br/><br/> Se a Contoso usar o Data Migration Assistant em vez do Data Migration Service para migrar o banco de dados, ele não terá a infraestrutura pronta para migrar bancos de dados em escala. A Contoso precisará construir outra região para garantir o failover se a região primária estiver indisponível.

## <a name="proposed-architecture"></a>Arquitetura proposta

![Arquitetura de cenário](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processo de migração

1. A Contoso provisiona uma instância do SQL do Azure e migra o banco de dados do SmartHotel360 para ela.
2. A Contoso provisiona e configura os Web Apps e implanta o aplicativo SmartHotel360 para eles.

    ![Processo de migração](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração do banco de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | A Contoso usará o DMA para avaliar e detectar problemas de compatibilidade que possam afetar a funcionalidade do banco de dados no Azure. DMA avalia a paridade de recursos entre SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta que pode ser baixada gratuitamente.
[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Um serviço de banco de dados de nuvem relacional inteligente e totalmente gerenciado. | Custo com base em recursos, taxa de transferência e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Serviço de Aplicativo do Azure – Aplicativos Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Crie aplicativos de nuvem avançados usando uma plataforma totalmente gerenciada | Custo com base no tamanho, na localização e na duração do uso. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Pré-requisitos

Aqui, a Contoso precisa executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | A Contoso criou assinaturas durante um artigo anterior. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.


## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar uma instância do Banco de Dados SQL no Azure**: a Contoso provisiona uma instância do SQL no Azure. Depois que o site do aplicativo for migrado para o Azure, o aplicativo da Web do serviço WCF apontará para esta instância.
> * **Etapa 2: Migrar o banco de dados com o DMA**: a Contoso migra o banco de dados do aplicativo com o Assistente de Migração do Banco de Dados.
> * **Etapa 3: provisionar aplicativos da Web**: a Contoso provisiona os dois aplicativos da web.
> * **Etapa 4: Configurar o VSTS**: a Contoso cria um novo projeto do VSTS e importa o repositório do Git.
> * **Etapa 5: Configurar as sequências de conexão**: a Contoso configura as sequências de conexão para que o aplicativo Web da camada da web, o aplicativo Web do serviço WCF e a instância do SQL possam se comunicar.
> * **Etapa 6: configurar os pipelines de criação e liberação no VSTS**: como etapa final, a Contoso configura os pipelines de criação e liberação para criar o aplicativo e os implementa em dois aplicativos Web do Azure separados.


## <a name="step-1-provision-an-azure-sql-database"></a>Etapa 1: Provisionar um Banco de Dados SQL do Azure

1. Os administradores da Contoso Selecione para criar um banco de dados SQL no Azure. 

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Eles especificam um nome de banco de dados para corresponder ao banco de dados em execução na VM local (**SmartHotel.Registration**). Eles colocam o banco de dados no grupo de recursos ContosoRG. Esse é o grupo de recursos que eles usam para recursos de produção no Azure.

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Eles configuram uma nova instância do SQL Server (**sql-smarthotel-eus2**) na região primária.

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Eles definem o tipo de preço para corresponder às suas necessidades de servidor e banco de dados. E escolhem economizar dinheiro com o Benefício Híbrido do Azure porque já tem uma licença do SQL Server.
5. Para dimensionamento eles usam a compra baseada em VCore e definem os limites para os requisitos esperados.

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Em seguida, eles criam a instância de banco de dados.

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Depois que a instância é criada, eles abrem o banco de dados e anotam os detalhes que serão necessários quando usarem o Assistente de Migração de Banco de dados para a migração.

    ![Provisionar SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Precisa de mais ajuda?**

- [Obtenha ajuda ](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) o provisionamento de um Banco de Dados SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) os limites de recursos do VCore.


## <a name="step-2-migrate-the-database-with-dma"></a>Etapa 2: migrar o banco de dados com o DMA

Os administradores da Contoso migrarão o banco de dados do SmartHotel360 usando o DMA.

### <a name="install-dma"></a>Instalar o DMA

1. Eles fazem o download da ferramenta do [ Centro de Download da Microsoft ](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a instalação (DownloadMigrationAssistant.msi) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

### <a name="migrate-the-database-with-dma"></a>Migrar o banco de dados com o DMA

1. O DMA, criam um novo projeto (**SmartHotelDB**) e selecione **migração** 
2. Eles escolhem o tipo de servidor de origem como **SQL Server** e o destino como **Banco de Dados SQL do Azure**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Nos detalhes da migração, eles adicionam **SQLVM** como o servidor de origem e o banco de dados **SmartHotel.Registration**. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Eles recebem um erro que parece estar associado à autenticação. No entanto, após investigar, o problema é o ponto (.) no nome do banco de dados. Como solução alternativa, eles decidiram provisionar um novo banco de dados SQL usando o nome **SmartHotel-Registration** para resolver o problema. Quando eles executam o DMA novamente, são capazes de selecionar **SmartHotel-Registration** e continuar com o assistente.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. Em **Selecionar Objetos**, selecionam as tabelas de banco de dados e gerar um script SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Depois que o DMS cria o script, eles clicam em **Implantar esquema**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. O DMA confirma que a implantação foi realizada com êxito.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Agora eles iniciam a migração.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Após a conclusão da migração, os administradores da Contoso podem verificar se o banco de dados está sendo executado na instância do Azure SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Eles excluem o banco de dados SQL extra **SmartHotel.Registration** no portal do Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Etapa 3: Provisionar aplicativos Web

Com o banco de dados migrado, os administradores da Contoso agora podem provisionar os dois aplicativos da web.

1. Eles selecionam **Aplicativo Web** no portal.

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Eles fornecem um nome de aplicativo (**SHWEB-EUS2**), executam o aplicativo no Windows e o colocam no grupo de recursos de produção **ContosoRG**. Eles criam um novo plano e um serviço de aplicativo.

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Depois que o aplicativo Web é provisionado, eles repetem o processo para criar um aplicativo Web para o serviço WCF (**SHWCF-EUS2**)

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Após concluírem, eles navegam par ao endereço dos aplicativos para verificar se foram criados com êxito.


## <a name="step-4-set-up-vsts"></a>Etapa 4: Configurar o VSTS


A Contoso precisa criar a infraestrutura e os pipelines de DevOps para o aplicativo.  Para fazer isso, os administradores da Contoso criam um novo projeto VSTS, importam o código e, em seguida, configuram os pipelines de criação e liberação.

1.   Na conta Contoso VSTS, eles criam um novo projeto (**ContosoSmartHotelRefactor**) e selecionam **Git** para controle de versão.

    ![Novo Projeto](./media/contoso-migration-refactor-web-app-sql/vsts1.png)

2. Eles importam o repositório Git que atualmente contém seu código do aplicativo. Ele está em um [repositório público](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) e você pode baixá-lo.

    ![Baixar código do aplicativo](./media/contoso-migration-refactor-web-app-sql/vsts2.png)

3. Depois que o código for importado, eles conectarão o Visual Studio ao repositório e clonarão o código usando o Team Explorer.

    ![Conectar-se ao repositório](./media/contoso-migration-refactor-web-app-sql/vsts3.png)

4. Depois que o repositório for clonado para o computador do desenvolvedor, eles abrirão o arquivo de solução do aplicativo. O aplicativo Web e o serviço wccf têm, cada um, um projeto separado dentro do arquivo.

    ![Arquivo de solução](./media/contoso-migration-refactor-web-app-sql/vsts4.png)

## <a name="step-5-configure-connection-strings"></a>Etapa 5: Configurar sequências de conexão

Os administradores da Contoso precisam garantir que os aplicativos da Web e o banco de dados possam se comunicar. Para fazer isso, eles configuram cadeias de conexão no código e nos aplicativos Web.

1. No aplicativo Web para o serviço WCF (**SHWCF-EUS2**) > **Configurações** > **Configurações de aplicativo**, eles adicionam uma nova cadeia de conexão chamada **DefaultConnection**.
2. A cadeia de conexão é obtida do banco de dados **SmartHotel-Registration** e deve ser atualizada com as credenciais corretas.

    ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Usando o Visual Studio, eles abrem o projeto **SmartHotel.Registration.wcf** do arquivo da solução. A seção **connectionStrings** do arquivo web.config para o serviço WCF SmartHotel.Registration.Wcf deve ser atualizada com a cadeia de conexão.

     ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/string2.png)

4. A seção **client** do arquivo web.config para o SmartHotel.Registration.Web deve ser alterada para apontar para a nova localização do serviço WCF. Esta é a URL do aplicativo Web WCF que hospeda o ponto de extremidade de serviço.

    ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. Depois que as alterações estão no código, os administradores precisam confirmar as alterações. Usando o Team Explorer no Visual Studio, eles confirmam e sincronizam.


## <a name="step-6-set-up-build-and-release-pipelines-in-vsts"></a>Etapa 6: Configurar a compilação e liberar pipelines no VSTS

Agora, os administradores da Contoso configuram o VSTS para realizar o processo de build e de lançamento para acionar as práticas do DevOps.

1. No VSTS, eles clicam em **Build e lançamento** > **Novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Eles selecionam **Git do VSTS** e o respectivo repositório.

    ![Git e repositório](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. Em **Selecione um modelo**, eles selecionam o modelo ASP.NET para sua construção.

     ![Modelo do ASP.NET](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. Eles especificam o nome ContosoSmartHotelRefactor-ASP.NET-CI para a compilação e clicam em **Save & Queue**.

     ![Salvar e enfileirar](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. Isso inicia a primeira compilação. Eles clicam no número da compilação para assistir ao processo. Depois de terminado, eles podem ver o feedback do processo.

    ![Comentários](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. Depois de uma construção bem-sucedida, abra a construção e clique em Eles clicam em **Artefatos**. Esta pasta contém os resultados da compilação

    - Os dois arquivos zip são os pacotes que contêm os aplicativos.
    - Esses arquivos são usados no pipeline de lançamento para implantação nos aplicativos da Web do Azure

     ![Artefato](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Clicarem **Releases** > **+ novo pipeline**.

    ![Novo pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Eles selecionam o modelo de implantação do Serviço de Aplicativo do Azure.

    ![Modelo de serviço de aplicativo do Azure](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Eles nomeiam o canal de liberação **ContosoSmartHotelRefactor** e especificam o nome do aplicativo Web do WCF (SHWCF-EUS2) para o nome do ambiente.

    ![Ambiente](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. No ambiente, eles clicam em **1 fase, 1 tarefa** para configurar a implantação do serviço WCF.

    ![Implantar o WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Eles verificam se a assinatura está selecionada e autorizada e selecionam o **nome do serviço de aplicativo**.

     ![Selecionar o serviço de aplicativo](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. Em **Artefatos**, eles selecionam **+ Adicionar um artefato** e selecionam para construir com o pipeline **ContosoSmarthotelRefactor-ASP.NET-CI**.

     ![Compilação](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Eles clicam no raio do artefato verificado, para ativar o acionador de implantação contínua.

     ![Raio](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. Além disso, observe que o acionador de implantação contínua deve ser definido como **Ativado**.

   ![Implantação contínua habilitada](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Agora, eles, clique em **implantar o serviço de aplicativo do Azure**.

    ![Implantar serviço de aplicativo](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. Em **Selecione um arquivo ou pasta**, eles localizam o **arquivo SmartHotel.Registration.Wcf.zip** que estava criando durante a construção, e clilck **Salvar** .- sql

    ![Salve o WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Eles clicam em **Pipeline** >**+ Add**, para adicionar um ambiente para **SHWEB-EUS2**, selecionando outra implantação do Serviço de Aplicativo do Azure.

    ![Adicionar o ambiente](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. Eles repetem o processo para publicar o arquivo do aplicativo da web (**SmartHotel.Registration.Web.zip**) no aplicativo da Web correto.

    ![Publicar aplicativo web](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Depois que ele for salvo, o pipeline de lançamento será exibido da seguinte maneira.

     ![Resumo de pipeline de versão](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Eles voltam para **Build** e clicam em **Triggers** > **Ativam a integração contínua**. Isso permite que o pipeline, quando as alterações são confirmadas no código, ocorra a criação e liberação completas.

    ![Ativar integração contínua](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Eles clicam em **Salvar e Fila** para executar o pipeline completo. Uma nova compilação é disparada, que, por sua vez, cria a primeira versão do aplicativo para o Serviço de Aplicativo do Azure.

    ![Salve o pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Os administradores da Contoso podem seguir o processo de criação e liberação de pipeline do VSTS. Após a conclusão da compilação, a liberação será iniciada.

    ![Criar e liberar aplicativo](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. Após o término do pipeline, os dois sites foram implantados e o aplicativo está funcionando on-line.

    ![Versão de término](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

Neste ponto, o aplicativo é migrado com êxito para o Azure.



## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa concluir estas etapas de limpeza:  

- Remova as VMs locais do inventário do vCenter.
- Remova as VMs das tarefas de backup locais.
- Atualize a documentação interna para mostrar os novos locais para o aplicativo SmartHotel360. Mostre o banco de dados como em execução no Banco de Dados SQL do Azure e o front-end como em execução nos dois aplicativos Web.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.


## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

- A Contoso precisa garantir que o novo **banco de dados de Registro do SmartHotel** seja seguro. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, a Contoso deve atualizar os aplicativos da Web para usar SSL com certificados.

### <a name="backups"></a>Backups

- A Contoso precisa examinar os requisitos de backup do Banco de Dados SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- A Contoso também precisa aprender sobre como gerenciar backups e restaurações do Banco de Dados SQL. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) sobre backups automáticos.
- A Contoso deve considerar a implementação de grupos de failover para fornecer failover regional para o banco de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- A Contoso precisa considerar a implantação do Web App na região principal do leste dos EUA 2 e da região central dos EUA para obter resiliência. A Contoso pode configurar o Gerenciador de Tráfego para garantir o failover em caso de interrupções regionais.

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Depois que todos os recursos estiverem implantados, a Contoso deverá atribuir marcações ao Azure com base no seu [planejamento de infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Todo o licenciamento se baseia no custo dos serviços de PaaS que a Contoso está consumindo. Isso será deduzido do EA.
- A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorou o aplicativo SmartHotel360 no Azure migrando a VM de front-end do aplicativo para dois Web Apps do Azure. O banco de dados do aplicativo foi mirado para um banco de dados SQL do Azure.






