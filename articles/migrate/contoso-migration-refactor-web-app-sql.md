---
title: Refatorar um aplicativo da Contoso migrando-o para VMs do Azure e grupo de disponibilidade AlwaysOn do SQL Server | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local migrando-o para um aplicativo Web de contêiner do Azure e um banco de dados do SQL Server do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005183"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migração da Contoso: refatorar um aplicativo local para um aplicativo Web do Azure e Banco de Dados SQL do Azure

Este artigo demonstra como a Contoso refatora o aplicativo SmartHotel no Azure. Eles migram a VM de front-end do aplicativo para um aplicativo Web do Azure e o banco de dados do aplicativo para um Banco de Dados SQL do Azure.

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
Artigo 9: Refatorar um aplicativo para um aplicativo Web do Azure e para o Banco de Dados SQL do Azure | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Este artigo
[Artigo 10: Refatorar um aplicativo Linux para aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra sua implantação do TFS (Team Foundation Server) local migrando-a para o VSTS (Visual Studio Team Services) no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível


Neste artigo, a Contoso migra o Windows de duas camadas. Aplicativo NET SmartHotel em execução nas VMs do VMware para o Azure. Se você quiser usar este aplicativo, ele é fornecido como código aberto e você pode fazer o download dele no [ GitHub ](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou de perto com seus parceiros de negócios para entender o que eles querem alcançar com essa migração:

- **Abordar o crescimento dos negócios** : a Contoso está crescendo e há pressão sobre seus sistemas e infraestrutura locais.
- **Aumentar a eficiência**: a Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.
- **Custos**: a Contoso deseja minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas foram usadas para determinar o melhor método de migração.

**Requisitos** | **Detalhes**
--- | --- 
**Aplicativo** | O aplicativo no Azure permanecerá tão importante quanto é hoje.<br/><br/> Ele deve ter os mesmos recursos de desempenho que tem atualmente no VMWare.<br/><br/> Eles não querem investir no aplicativo. Por enquanto, eles simplesmente desejam movê-lo com segurança para a nuvem.<br/><br/> Eles querem interromper o suporte ao Windows Server 2008 R2, no qual o aplicativo atualmente é executado.<br/><br/> Eles desejam sair do SQL Server 2008 R2 para uma plataforma de Banco de Dados PaaS moderna, que minimizará a necessidade de gerenciamento.<br/><br/> A Contoso deseja aproveitar seus investimentos em licenciamento do SQL Server e o Software Assurance quando possível.<br/><br/> Além disso, a Contoso deseja reduzir o ponto único de falha na camada da Web.
**Limitações** | O aplicativo consiste em um aplicativo ASP.NET e um serviço WCF em execução na mesma VM. Eles querem dividir isso em dois aplicativos Web usando o Serviço de Aplicativo do Azure. 
**As tabelas** | Eles desejam mover o aplicativo para o Azure, mas não querem executá-lo em VMs. Eles desejam utilizar os serviços de PaaS do Azure para as camadas de dados e da Web. 

## <a name="solution-design"></a>Design da solução

Depois de fixar suas metas e requisitos, a Contoso cria e examina uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que ela usará para a migração.

### <a name="current-app"></a>Aplicativo atual

- O aplicativo local SmartHotel é dividido em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.


### <a name="proposed-solution"></a>Solução proposta

- Para a camada de banco de dados do aplicativo, a Contoso comparou o Banco de Dados SQL do Azure com o SQL Server usando [este artigo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Eles decidiram usar o Banco de Dados SQL do Azure por alguns motivos:
    - O Banco de Dados SQL do Azure é um serviço gerenciado de banco de dados relacional. Ele oferece desempenho previsível em vários níveis de serviço, com administração quase zero. As vantagens incluem a escalabilidade dinâmica sem tempo de inatividade, a otimização inteligente interna e a escalabilidade e a disponibilidade globais.
    - Eles podem aproveitar o DMA (Assistente de Migração de Dados) leve para avaliar e migrar o banco de dados local para o SQL do Azure.
    - Com o Software Assurance, eles podem trocar as licenças existentes por tarifas com desconto em um Banco de Dados SQL, usando o Benefício Híbrido do Azure para SQL Server. Isso pode proporcionar uma economia de até 30%.
    - O Banco de Dados SQL fornece uma série de recursos de segurança, incluindo o mascaramento de dados dinâmico Always Encrypted e detecção de ameaça de segurança de nível de linha.
- Para a camada da Web do aplicativo, eles decidiram usar o Serviço de Aplicativo do Azure. Esse serviço de PaaS permite isso para implantar o aplicativo com apenas algumas mudanças de configuração. Eles usarão o Visual Studio para fazer a alteração e implantar dois aplicativos Web. Um para o site e outro para o serviço WCF.
  
### <a name="solution-review"></a>Análise de solução
A Contoso avalia seu projeto proposto reunindo uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Prós** | O código do aplicativo SmartHotel não precisará ser alterado para a migração para o Azure.<br/><br/> Eles podem aproveitar seus investimentos no Software Assurance usando o Benefício Híbrido do Azure para o SQL Server e para o Windows Server.<br/><br/> Após a migração eles não precisarão mais dar suporte ao Windows Server 2008 R2. [Saiba mais](https://support.microsoft.com/lifecycle).<br/><br/> Eles podem configurar a camada da Web do aplicativo com várias instâncias, para que ela não seja mais um ponto único de falha.<br/><br/> Eles não serão mais dependentes do antigo SQL Server 2008 R2.<br/><br/> O Banco de Dados SQL é compatível com os requisitos técnicos da Contoso. Eles avaliaram o banco de dados local usando o Assistente de Migração de Banco de Dados e constataram que é compatível.<br/><br/> O Banco de Dados SQL tem tolerância a falhas interna que a Contoso não precisa configurar. Isso garante que a camada de dados não é mais um ponto único de failover.
**Contras** | Os Serviços de Aplicativos do Azure são compatíveis com apenas uma implantação de aplicativo para cada aplicativo Web. Isso significa que dois aplicativos Web devem ser provisionados (um para o site e outro para o serviço WCF).<br/><br/> Se eles usarem o Assistente de Migração de Dados em vez do Serviço de Migração de Dados para migrar seu banco de dados, a Contoso não terá a infraestrutura pronta para migrar bancos de dados em escala. Eles precisarão criar outra região para garantir o failover caso a região primária fique indisponível.

## <a name="proposed-architecture"></a>Arquitetura proposta

![Arquitetura de cenário](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processo de migração

1. A Contoso provisiona uma instância do SQL do Azure e migram o banco de dados SmartHotel para ela.
2. Eles provisionam e configuram aplicativos Web e implantam o aplicativo SmartHotel para eles.

    ![Processo de migração](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração do banco de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Eles usará DMA para avaliar e detectar problemas de compatibilidade que podem afetar sua funcionalidade de banco de dados no Azure. DMA avalia a paridade de recursos entre SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta que pode ser baixada gratuitamente.
[Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) | Um serviço de banco de dados de nuvem relacional inteligente e totalmente gerenciado. | Custo com base em recursos, taxa de transferência e tamanho. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Serviço de Aplicativo do Azure – Aplicativos Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Crie aplicativos de nuvem avançados usando uma plataforma totalmente gerenciada | Custo com base no tamanho, na localização e na duração do uso. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Pré-requisitos

Veja o que você (e a Contoso) precisa para executar este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | Você já deve ter criado uma assinatura quando realizou a avaliação no primeiro artigo desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.


## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Provisionar uma instância do Banco de Dados SQL no Azure**: a Contoso provisiona uma instância do SQL no Azure. Depois que o site do aplicativo for migrado para o Azure, o aplicativo da Web do serviço WCF apontará para esta instância.
> * **Etapa 2: Migrar o banco de dados com o DMA**: eles migram o banco de dados de aplicativo com o Assistente de Migração de Banco de Dados.
> * **Etapa 3: Provisionar aplicativos Web**: eles provisionam os dois aplicativos Web.
> * **Etapa 4: Configurar cadeias de conexão**: eles configuram cadeias de conexão para que o aplicativo Web da camada da Web, o aplicativo Web do serviço WCF e a instância do SQL possam se comunicar.
> * **Etapa 5: Publicar aplicativos Web**: como etapa final, a Contoso publica os aplicativos no Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Etapa 1: Provisionar um Banco de Dados SQL do Azure

1. Eles escolhem criar um Banco de Dados SQL no Azure. 

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

A Contoso migrará o banco de dados SmartHotel usando o DMA.

### <a name="install-dma"></a>Instalar o DMA

1. Eles fazem o download da ferramenta do [ Centro de Download da Microsoft ](https://www.microsoft.com/download/details.aspx?id=53595) para a VM do SQL Server no local (**SQLVM**).
2. Eles executam a instalação (DownloadMigrationAssistant.msi) na VM.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

### <a name="migrate-the-database-with-dma"></a>Migrar o banco de dados com o DMA

1. No DMA, crie um novo projeto (**SmartHotelDB**) e selecione **Migração** 
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

9. Após a migração ser concluída, a Contoso pode verificar se o banco de dados está em execução na instância SQL do Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Eles excluem o banco de dados SQL extra **SmartHotel.Registration** no portal do Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Etapa 3: Provisionar aplicativos Web

Com o banco de dados migrado, a Contoso pode provisionar os dois aplicativos Web.

1. Eles selecionam **Aplicativo Web** no portal.

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Eles fornecem um nome de aplicativo (**SHWEB-EUS2**), executam o aplicativo no Windows e o colocam no grupo de recursos de produção **ContosoRG**. Eles criam um novo plano e um serviço de aplicativo.

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Depois que o aplicativo Web é provisionado, eles repetem o processo para criar um aplicativo Web para o serviço WCF (**SHWCF-EUS2**)

    ![Aplicativo Web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Após concluírem, eles navegam par ao endereço dos aplicativos para verificar se foram criados com êxito.

## <a name="step-4-configure-connection-strings"></a>Etapa 4: Configurar cadeias de conexão

A Contoso precisa para se certificar de que os aplicativos Web e o banco de dados possam se comunicar. Para fazer isso, eles configuram cadeias de conexão no código e nos aplicativos Web.

1. No aplicativo Web para o serviço WCF (**SHWCF-EUS2**) > **Configurações** > **Configurações de aplicativo**, eles adicionam uma nova cadeia de conexão chamada **DefaultConnection**.
2. A cadeia de conexão é obtida do banco de dados **SmartHotel-Registration** e deve ser atualizada com as credenciais corretas.

    ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Usando o Visual Studio, a Contoso abre o arquivo de solução da pasta **SmartHotel360-internal-booking-apps**. A seção **connectionStrings** do arquivo web.config para o serviço WCF SmartHotel.Registration.Wcf deve ser atualizada com a cadeia de conexão.

     ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/string2.png)

4. A seção **client** do arquivo web.config para o SmartHotel.Registration.Web deve ser alterada para apontar para a nova localização do serviço WCF. Esta é a URL do aplicativo Web WCF que hospeda o ponto de extremidade de serviço.

    ![Cadeia de conexão](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>Etapa 5: Publicar aplicativos Web

Como etapa final, a Contoso publica os aplicativos Web no Azure.

1. No Visual Studio, eles clicam com o botão direito do mouse no projeto SmartHotel.REgistration.Wcf > **Publicar**.

    ![Publicar](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Eles iniciam a publicação.

    ![Publicar](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Eles selecionam um Serviço de Aplicativo existente porque eles já criaram o aplicativo Web.

    ![Publicar](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Depois que eles selecionarem o aplicativo WCF, o Visual Studio o implanta.

    ![Publicar](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Em seguida, eles repetem o processo para publicar o aplicativo Web – SmartHotel.Registration.Web.

    ![Publicar](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


Neste ponto, o aplicativo é migrado com êxito para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Após a migração, a Contoso precisa concluir estas etapas de limpeza:  

- Remova as VMs locais do inventário do vCenter.
- Remova as VMs das tarefas de backup locais.
- Atualize a documentação interna para mostrar os novos locais para o aplicativo SmartHotel. Mostre o banco de dados como em execução no Banco de Dados SQL do Azure e o front-end como em execução nos dois aplicativos Web.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.


## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

- A Contoso precisa garantir que seu novo banco de dados **SmartHotel-Registration** é seguro. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Em particular, eles devem atualizar os aplicativos Web para usar SSL com certificados.

### <a name="backups"></a>Backups

- A Contoso precisa examinar os requisitos de backup do Banco de Dados SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Eles devem considerar a implementação de grupos de failover para fornecer failover regional para o banco de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- A Contoso precisa considerar a implantação do aplicativo Web na região Leste dos EUA 2 e Centro dos EUA principal para resiliência. Eles poderiam configurar o Gerenciador de Tráfego para garantir o failover no caso de interrupções regionais.

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Depois que todos os recursos estiverem implantados, a Contoso deverá atribuir marcações ao Azure com base no seu [planejamento de infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Todo o licenciamento se baseia no custo dos serviços de PaaS que a Contoso está consumindo. Isso será deduzido do EA.
1. A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso refatorou o aplicativo SmartHotel no Azure migrando a VM de front-end do aplicativo para dois aplicativos Web do Azure. Eles migraram o banco de dados do aplicativo para um Banco de Dados SQL do Azure.






