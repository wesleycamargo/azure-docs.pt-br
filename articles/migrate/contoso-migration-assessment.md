---
title: Avaliar as cargas de trabalho do local para a migração da Contoso para o Azure | Microsoft Docs
description: Saiba como Contoso avalia seus computadores locais para a migração para o Azure com a migração do Azure e Migraton de banco de dados
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006559"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migração de Contoso: avaliar as cargas de trabalho do local para a migração para o Azure

Este artigo mostra como Contoso avalia seu aplicativo de SmartHotel local, em preparação para a migração para o Azure.

Este documento é o terceiro de série de artigos de documento como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações de plano de fundo e uma série de cenários de implantação que ilustram como configurar uma infraestrutura de migração, avaliar a adequação dos recursos locais para a migração e executar diferentes tipos de migrações. Os cenários crescem em complexidade; incluiremos outros artigos ao longo do tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
Artigo 3: Avaliar recursos locais para migração para o Azure  | Mostra como a Contoso executa uma avaliação de um aplicativo SmartHotel de dois níveis no local em execução no VMware. A Contoso avalia as VMs de aplicativos com o serviço [ do Azure Migrate ](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [ Assistente de Migração de Banco de Dados ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Este artigo.
[Artigo 4: Hospedar novamente um aplicativo em VMs do Azure e uma Instância Gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso executa uma migração lift-and-shift para o Azure para o aplicativo SmartHotel local. A Contoso migra a VM frontend do aplicativo usando [ Azure Site Recovery ](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados do aplicativo para uma Instância Gerenciada do SQL, usando o [ Serviço de Migração do Banco de Dados do Azure ](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra as VMs do aplicativo SmartHotel para VMs do Azure usando o serviço Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo e o serviço Database Migration para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso faz uma migração de elevação e deslocamento do aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e migra o banco de dados do aplicativo para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra sua implantação do TFS (Team Foundation Server) local migrando-a para o VSTS (Visual Studio Team Services) no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível


## <a name="overview"></a>Visão geral

Ao considerar a migração para o Azure, empresa Contoso deseja executar uma avaliação de técnica e financeira para descobrir se suas cargas de trabalho local são adequadas para a migração para a nuvem. Em particular, a equipe de Contoso deseja avaliar a compatibilidade de máquina e o banco de dados para migração e estimar a capacidade e custos para a execução de seus recursos no Azure.

Para obter seu molhado pés e entender melhor as tecnologias envolvidas, vai avaliar dois dos seus aplicativos locais, resumidos na tabela a seguir. Observe que eles estão avaliando cenários de migração que refazem e refaturam aplicativos para migração. Saiba mais sobre a nova hospedagem e refatoração no [visão geral da migração Contoso](contoso-migration-overview.md).

**Nome do aplicativo** | **Plataforma** | **Camadas de aplicativo** | **Detalhes**
--- | --- | --- | ---
SmartHotel<br/><br/> Gerencia os requisitos de viagem da Contoso | Em execução no Windows com um banco de dados do SQL Server | Aplicativo de duas camadas com o site da Web front-end ASP.NET em execução em uma VM (WEBVM) e o SQL Server em execução em outra máquina virtual (SQLVM) | As VMs são VMware, em execução em um host de ESXi gerenciado pelo servidor do vCenter.<br/><br/> O aplicativo de exemplo pode ser baixado do [GitHub](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Suporte técnico do Contoso serviço de aplicativo | Em execução no Linux/Apache, com um PHP MySQL (LÂMPADA). | Aplicativo de duas camadas com um site PHP de frontend em uma VM (OSTICKETWEB) e o banco de dados MySQL em execução em outra VM (OSTICKETMYSQL) | O aplicativo é usado por aplicativos de atendimento ao cliente para rastrear problemas para funcionários internos e clientes externos.<br/><br/> O aplicativo de exemplo pode ser baixado do [GitHub](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Arquitetura atual


Aqui está um diagrama que mostra a atual infraestrutura local Contoso.

![Arquitetura da Contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- A Contoso tem um data center principal localizado na cidade de Nova York do Leste dos Estados Unidos.
- Eles têm três ramificações locais adicionais nos Estados Unidos.
- O data center principal está conectado à internet com uma conexão de ethernet metro fibra (500 mbps).
- Cada ramificação está conectada localmente à internet usando conexões da classe de negócios, com túneis VPN IPSec para o data center principal. Isso permite que seus toda a rede a ser conectado permanentemente e otimiza a conectividade com a internet.
- O data center principal é totalmente virtualizado com o VMware. Eles têm dois hosts de virtualização ESXi 6.5, gerenciados pelo vCenter Server 6.5.
- A Contoso usa o Active Directory para gerenciamento de identidade e os servidores DNS na rede interna.
- Executam os controladores de domínio no data center em máquinas virtuais do VMware. Os controladores de domínio no locais branches executados em servidores físicos.





## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com seus parceiros comerciais para entender o que a empresa deseja atingir com essa migração:

- **Endereço de crescimento comercial**: Contoso está crescendo e como resultado, há pressão de seus sistemas locais e da infraestrutura.
- **Aumentar a eficiência**: Contoso precisa remover procedimentos desnecessários e simplificar processos para seus desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.

## <a name="assessment-goals"></a>Objetivos da avaliação

A equipe de nuvem Contoso foi fixado para baixo de objetivos de sua avaliação de migração de:

- Após a migração, os aplicativos no Azure devem ter os mesmos recursos de desempenho de hoje em seu ambiente de VMWare no local.  Mover para a nuvem não significa que o desempenho do aplicativo é menos crítico.
- A Contoso deve entender a compatibilidade de seus aplicativos e bancos de dados com os requisitos do Azure, bem como suas opções de hospedagem no Azure.
- Administração de banco de dados da Contoso deve ser minimizada depois aplicativos foram movidas para a nuvem.  
- A Contoso deseja entender não apenas as opções de migração, mas também os custos associados com a infraestrutura depois que ele é movido para a nuvem.

## <a name="assessment-tools"></a>Ferramentas de avaliação
Contoso está usando ferramentas da Microsoft para a avaliação. Essas ferramentas alinham com suas metas e devem fornecer todas as informações necessárias.

**Tecnologia** | **Descrição** | **Custo**
--- | --- | ---
[Assistente de migração do banco de dados (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Eles usará DMA para avaliar e detectar problemas de compatibilidade que podem afetar sua funcionalidade de banco de dados no Azure. DMA avalia a paridade de recursos entre SQL origens e destinos e recomenda melhorias de desempenho e confiabilidade. | É uma ferramenta que pode ser baixada gratuitamente.
[Migrações para Azure](https://docs.microsoft.com/azure/migrate/migrate-overview) | A Contoso usará este serviço para avaliar suas VMs VMware. Ele avalia a adequação da migração dos computadores e fornece estimativas de dimensionamento e custo para a execução no Azure.  | Não há atualmente (pode 2018) sem custos por usando esse serviço.
[Mapa do Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | As Migrações para Azure usam o Mapa do Serviço para mostrar as dependências entre computadores que você deseja migrar. |  O Mapa do Serviço é parte do Azure Log Analytics. Ele pode ser usado gratuitamente por 180 dias.

Nesse cenário, a Contoso baixa e executa o DMA para avaliar o banco de dados do SQL Server local para seu aplicativo de viagem. Usar o Azure migrar com o mapeamento de dependência para avaliar o aplicativo VMs, antes da migração para o Azure.



## <a name="assessment-architecture"></a>Arquitetura de avaliação


![Arquitetura de avaliação da migração](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso é um nome fictício que representa uma empresa típica.
- A Contoso tem um datacenter local (**contoso datacenter**), com os controladores de domínio local (CONTOSODC1, CONTOSODC2).
- Máquinas virtuais do VMware estão localizados em uma VMware ESXI hosts que executam a versão 6.5. Hosts: **contosohost1**, **contosohost2**
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução em uma VM.
- O aplicativo de viagem SmartHotel:
    - O aplicativo está em camadas em duas máquinas virtuais VMware, **WEBVM** e **SQLVM**.
    - As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com**.
    - As máquinas virtuais estiverem executando o Windows Server 2008 R2 Datacenter com SP1.
- O ambiente VMware é gerenciado pelo vCenter Server (**vcenter.contoso.com**) em execução em uma VM.
- Aplicativo de suporte técnico de serviço a OSTicket:
    - O aplicativo está em camadas em duas VMs, **OSTICKETWEB** e **OSTICKETMYSQL**.
    - As VMs estão sendo executados no servidor do Ubuntu Linux 16.04-LTS.
    - A VM OSTICKETWEB está em execução Apache 2 e 7.0 do PHP.
    - A VM OSTICKETMYSQL está executando MySQL 5.7.22.

![Arquitetura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que precisa Contoso (e) para a avaliação:

- Acesso de proprietário ou colaborador da assinatura do Azure, ou para um grupo de recursos na assinatura do Azure.
- Um vCenter Server local executando a versão 5.5, 6.0 ou 6.5.
- Uma conta somente leitura no vCenter Server ou permissões para criar uma.
- Permissões para criar uma VM no vCenter Server usando um modelo OVA.
- Pelo menos um host ESXi executando a versão 5.0 ou posterior.
- Pelo menos duas VMs VMware locais, uma delas executando um banco de dados do SQL Server.
- Permissões para instalar agentes de migrar do Azure em cada VM.
- As VMs devem ter conectividade direta com a Internet.
        - Você pode restringir o acesso à internet para as [URLs necessárias](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Se as máquinas com nenhuma conectividade com a internet, o [gateway OMS](../log-analytics/log-analytics-oms-gateway.md) precisa ser instalado neles.
- O FQDN da VM que está executando a instância do SQL Server, para a avaliação do banco de dados.
- O Firewall do Windows em execução na VM do SQL Server deve permitir conexões externas na porta TCP 1433 (padrão), para que o DMA possa se conectar.


## <a name="assessment-overview"></a>Visão geral da avaliação

Aqui está como Contoso realizará a avaliação:


> [!div class="checklist"]
> * **Etapa 1: baixar e instalar o DMA**: preparar o DMA para avaliação do banco de dados do SQL Server local.
> * **Etapa 2: avaliar o banco de dados com DMA**: executar e analisar a avaliação do banco de dados.
> * **Etapa 3: preparar a avaliação de VMs com as Migrações para Azure**: configurar as contas locais e ajustar as configurações do VMware.
> * **Etapa 4: descobrir as VMs locais com Migrações para Azure**: criar uma VM do coletor das Migrações para Azure. Em seguida, eles executarem o coletor para descobrir as VMs para avaliação.
> * **Etapa 5: Preparar para a análise de dependência com o Azure migrar**: instalar o Azure migrar agentes nas VMs, para que eles possam ver o mapeamento de dependência entre as máquinas virtuais.
> * **Etapa 6: Avaliar as VMs com o Azure migrar**: verificar dependências, agrupar as máquinas virtuais e executar a avaliação. Depois que a avaliação estiver pronta, eles analisá-lo na preparação para migração.


## <a name="step-1-download-and-install-the-dma"></a>Etapa 1: baixar e instalar o DMA

1. Contoso downloads DMA do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - O assistente pode ser instalado em qualquer computador que possa se conectar à instância do SQL. Não é necessário executá-lo no computador do SQL Server.
    - Ele não deve ser executado no computador de host do SQL Server.
2. Eles executarem o arquivo de instalação baixado (DownloadMigrationAssistant.msi), para iniciar a instalação.
3. Sobre o **concluir** página, selecione **iniciar Microsoft dados Assistente de migração** antes de concluir o assistente.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Etapa 2: Executar e analisar a avaliação de banco de dados para SmartHotel

Agora, a Contoso pode executar uma avaliação para analisar seu SQL Server no local para o aplicativo SmartHotel.

1. No Assistente de migração do banco de dados, clicar **novo**, selecione **avaliação**e forneça a avaliação de um nome de projeto - **SmartHotel**.
2. Eles selecionam o **tipo de servidor de origem** como **do SQL Server em máquinas virtuais Azure**.

    ![Selecionar a origem](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      No momento, o DMA não dá suporte à avaliação de migração para uma Instância Gerenciada SQL. Como alternativa, a Contoso usa o SQL Server na VM do Azure como o destino suposto para a avaliação.

3. Em **selecionar a versão de destino**, eles selecionam 2017 do SQL Server como a versão de destino. Eles precisam Selecione essa opção porque ela é a versão usada pela instância gerenciada do SQL.
4. Eles selecionar para descobrir informações sobre compatibilidade e novos recursos:
    - **Problemas de compatibilidade** Observe as alterações que podem interromper a migração ou que exigem um ajuste secundário antes da migração. Isso mantém você informado sobre os recursos em uso no momento em que foram substituídos. Os problemas são organizados por nível de compatibilidade.
    - **Recomendação de novos recursos** notas novos recursos na plataforma do SQL Server de destino que pode ser usado para o banco de dados após a migração. Eles são organizados por desempenho, segurança e armazenamento.

    ![Selecionar o destino](./media/contoso-migration-assessment/dma-assessment-2.png)

2. Em **conectar a um servidor**, eles inserirem o nome da máquina virtual que executa o banco de dados e as credenciais para acessá-lo. Eles precisam habilitar **certificado do servidor confiável** para certificar-se de que eles podem obter com o SQL Server. Em seguida, clicar **conectar**.

    ![Selecionar o destino](./media/contoso-migration-assessment/dma-assessment-3.png)

3. Em **adicionar fonte**, eles Adicionar banco de dados que desejam avaliar e, em seguida, clique em **próximo** para iniciar a avaliação.
4. A avaliação é criada.

    ![Criar avaliação](./media/contoso-migration-assessment/dma-assessment-4.png)

5. Em **resultados da revisão**, eles podem ver os resultados da avaliação.


### <a name="analyze-the-database-assessment"></a>Analisar a avaliação do banco de dados

Resultados são exibidos como elas estão disponíveis. Se a correção de problemas que precisam clicar **avaliação reiniciar** para executar novamente a avaliação.

1. No **problemas de compatibilidade** de relatório, eles verificarem se há problemas de cada nível de compatibilidade. Os níveis de compatibilidade mapeiam para as versões do SQL Server da seguinte forma:

    - 100: SQL Server 2008/Banco de Dados SQL do Azure
    - 110: SQL Server 2012/Banco de Dados SQL do Azure
    - 120: SQL Server 2014/Banco de Dados SQL do Azure
    - 130: SQL Server 2016/Banco de Dados SQL do Azure
    - 140: SQL Server 2017/Banco de Dados SQL do Azure

    ![Problemas de compatibilidade](./media/contoso-migration-assessment/dma-assessment-5.png)

2. No **recurso recomendações** de relatório, a Contoso pode exibir os recursos de armazenamento, segurança e desempenho que recomenda a avaliação após a migração. Uma variedade de recursos, incluindo o OLTP in-memory e Columnstore, Stretch Database, sempre criptografado, máscara de dados dinâmicos e criptografia de dados transparente (TDE), são recomendados.

    ![Recomendações de recurso](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > É recomendável que a Contoso [habilita TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) para todos os SQL Server bancos de dados e não é mais crítica quando os bancos de dados estão na nuvem. TDE deve ser habilitada apenas depois da migração. Se o TDE já estiver habilitada, você precisará mover o certificado ou chave assimétrica no banco de dados mestre do servidor de destino. [Saiba mais](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Eles podem exportar a avaliação no formato JSON ou CSV.

Observe que, se você estiver executando uma avaliação de escala maior você pode:

- Execute várias avaliações simultaneamente e exibir o estado de avaliações abrindo o **todas as avaliações** página.
- [Consolidar avaliações em um banco de dados do SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Consolidar avaliações em um relatório do Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Etapa 3: preparar a avaliação de VMs com as Migrações para Azure

Contoso precisa para criar uma conta do VMware que migrar do Azure usará para descobrir as VMs para avaliação automaticamente, verifique as permissões para criar uma máquina virtual, observe as portas que precisam ser abertas e defina as estatísticas de nível de configurações.

### <a name="set-up-a-vmware-account"></a>Configurar uma conta do VMware

 Descoberta VM requer uma conta de somente leitura no vCenter, com as seguintes propriedades:

- Tipo de usuário: pelo menos um usuário somente leitura.
- Permissões: objeto de Data Center –> Propagar para o Objeto Filho, role=Read-only.
- Detalhes: usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

### <a name="verify-permissions-to-create-a-vm"></a>Verificar permissões para criar uma VM

A Contoso verifica se tem permissões para criar uma VM importando um arquivo no formato .OVA. [Saiba mais](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Verificar portas

A avaliação da Contoso usa o mapeamento de dependência. Este recurso requer um agente instalado em máquinas virtuais que você deseja avaliar. O agente deve ser capaz de se conectar ao Azure da porta TCP 443 em cada VM. [Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) sobre os requisitos de conexão.


### <a name="set-statistics-settings"></a>Definir configurações de estatísticas

Antes de começar a implantação, a Contoso deve definir as configurações de estatísticas para o servidor do vCenter para o nível 3. Observe que:

- Depois de definir o nível, você precisa aguardar no mínimo um dia antes de executar a avaliação. Caso contrário, ele pode não funcionar conforme o esperado.
- Se o nível for maior que 3, a avaliação funcionará, mas:
    - Não será possível coletar dados de desempenho de discos e rede.
    - Em relação ao armazenamento, as Migrações para Azure recomendam um disco padrão no Azure, com o mesmo tamanho do disco local.
    - Em relação à rede, para cada adaptador de rede local, um adaptador de rede será recomendado no Azure.
    - Em relação à computação, as Migrações para Azure examinarão o tamanho da memória e dos núcleos da VM e recomendarão uma VM do Azure com a mesma configuração. Se houver vários discos qualificados, será recomendado aquele com o menor custo.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) sobre tamanhos com nível 3.

Eles definir o nível da seguinte maneira:

1. O cliente Web vSphere, abrirem a instância do servidor vCenter.
2. Em **gerenciar** > **configurações** > **geral**, clicarem **editar**.
3. Em **estatísticas**, eles definir configurações de nível de estatística **nível 3**.

    ![Nível de estatísticas do vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Etapa 4: descobrir VMs

Para descobrir as VMs, a Contoso cria um projeto de migração do Azure. Eles baixarem e configurar o coletor de VM e executar o coletor para descobrir suas VMs no local.

### <a name="create-a-project"></a>Criar um projeto

1. No [portal do Azure](https://portal.azure.com), eles procuram **migrar do Azure**e crie um projeto (ContosoMigration).
2. Que especificam um nome de projeto, a assinatura do Azure e criar um novo grupo de recursos do Azure, **ContosoFailoverRG**. Observe que:

    - Só é possível criar um projeto de Migrações para Azure nas regiões Centro-Oeste ou Leste dos EUA.
    - Você pode planejar uma migração para qualquer local de destino.
    - O local do projeto só é usado para armazenar os metadados coletados a partir das VMs locais.

    ![Migrações para Azure](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Baixe o dispositivo coletor

O Migrações para Azure cria uma VM local conhecida como o dispositivo coletor. Essa VM descobre as VMs do VMware locais e envia os metadados sobre elas para o serviço Migrações para Azure. Para configurar o dispositivo de coletor, Contoso baixa um. Modelo de OVA e o importa para o servidor do vCenter local para criar a VM.

1. No projeto de migração do Azure > **Introdução** > **Discover & avaliar** > **descobrir máquinas**, baixam o. Arquivo de modelo OVA.
2. Eles copiar a ID do projeto e a chave. Isso é necessário para configurar o coletor.

    ![Baixe o arquivo .ova](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificar o dispositivo coletor

Antes de implantar a VM, a Contoso verifica se o. Arquivo OVA é seguro.

1. No computador no qual o arquivo baixado, eles abrirem uma janela de comando do administrador.
2. Eles executarem o seguinte comando para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas configurações (versão 1.0.9.12)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Criar o dispositivo coletor

Agora, a Contoso pode importar o arquivo baixado para o servidor do vCenter e provisionar a VM do servidor de configuração.

1. No console do cliente vSphere, clicarem **arquivo** > **implantar o modelo de OVF**.

    ![Implantar o OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. No Assistente de modelo de implantação OVF > **fonte**, elas especificam o local da. Arquivo OVA.
3. Em **Nome e Local**, eles especificam um nome amigável para a VM do coletor e o local do inventário no qual a VM será hospedada. Eles também especificam o host ou cluster no qual o dispositivo coletor será executado.
5. Em **armazenamento**, elas especificam o local de armazenamento e, em **formato de disco**, como eles desejam provisionar o armazenamento.
7. Em **mapeamento de rede**, elas especificam a rede à qual se conectar o coletor de VM. A rede precisa de conectividade com a Internet para enviar metadados para o Azure.
8. Eles examine as configurações e selecione **ligar após a implantação**> **concluir**. Uma mensagem confirmando a conclusão bem-sucedida é emitida depois que o dispositivo é criado.

### <a name="run-the-collector-to-discover-vms"></a>Executar o coletor para descobrir VMs

Agora eles executarem o coletor para descobrir as VMs. Observe que o coletor atualmente suporta apenas "Inglês (Estados Unidos)" como o idioma do sistema operacional e o idioma da interface do coletor.

1. No vSphere Client console > **abrir o Console**, elas especificam o idioma, fuso horário e preferências de senha para o coletor de VM.
2. Na área de trabalho, clicarem o **executar coletor** atalho.

    ![Atalho do coletor](./media/contoso-migration-assessment/collector-shortcut.png)

4. Em que o coletor de migrar do Azure > **configurar pré-requisitos**, eles aceitar os termos de licença e ler as informações de terceiros.
5. O coletor verifica se a VM tem acesso à internet, a hora é sincronizada, e se o serviço coletor está em execução (ele é instalado por padrão na máquina virtual). Ele também instala o VMWare PowerCLI.

    > [!NOTE]
    > Estamos presumindo que a VM tem acesso direto à Internet, sem um proxy.

    ![Verificar pré-requisitos](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. Em **especificar detalhes do servidor vCenter**, elas especificam o nome (FQDN) ou endereço IP do servidor vCenter e as credenciais somente leitura usadas para descoberta.
7. Eles selecionam um escopo de descoberta VM. O coletor só pode descobrir VMs dentro do escopo especificado. O escopo pode ser definido para uma pasta, datacenter ou cluster específicos. Ele não deve conter mais de 1500 VMs.

    ![Conectar-se ao vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. Em **projeto de migração de especificar**, elas especificam a ID do projeto de migração do Azure e a chave que foi copiada do portal. Você pode obtê-las novamente no projeto **visão geral** página > **descobrir máquinas**.  

    ![Conecte-se ao Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. Em **exibir o progresso da coleta** Contoso pode monitorar a descoberta e verifique que os metadados coletados de VMs em escopo. O coletor fornece um tempo aproximado de descoberta.

    ![Coleção em andamento](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Verifique as VMs no portal

Após a conclusão da coleta, a Contoso verifica se as VMs aparecem no portal.

1. No projeto de migração do Azure > **gerenciar** > **máquinas**, eles verificam se as VMs que você deseja descobrir aparecem.

    ![Computadores descobertos](./media/contoso-migration-assessment/discovery-complete.png)

3. Observe que as máquinas atualmente não têm os agentes das Migrações para Azure instalados. A Contoso deve instalá-los em ordem para exibir dependências.

    ![Computadores descobertos](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Etapa 5: preparar a análise de dependências

Para exibir as dependências entre as VMs que a Contoso deseja acessar, eles baixar e instalar agentes em VMs do aplicativo. A Contoso faz isso em todas as máquinas virtuais para seus aplicativos, Windows e Linux.

### <a name="take-a-snapshot"></a>Tirar um instantâneo

Eles manter uma cópia da máquina virtual antes de modificá-las, obtendo um instantâneo antes que os agentes sejam instalados.

![Instantâneo da máquina](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Fazer o download e instalar os agente de VM

1. No **máquinas** página, eles selecionam a máquina e, em seguida, **requer instalação** no **dependências** coluna.
2. Sobre o **descobrir máquinas** página fazem o seguinte:
    - Baixe o agente MMA e de dependência para cada VM do Windows
    - Baixe o agente MMA e de dependência para cada VM do Linux
3. Agora eles copiar a ID do espaço de trabalho e a chave. Que eles precisam ao instalar o MMA.

    ![Download do agente](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Instalar os agentes em máquinas virtuais do Windows

Eles executarem a instalação em cada máquina virtual.

#### <a name="install-the-mma-on-windows-vms"></a>Instale o MMA em VMs do Windows

1. Eles clicam duas vezes no agente baixado.
2. Em **Pasta de Destino**, eles mantêm a pasta de instalação padrão> **Próximo**.
2. Em **Opções de Instalação do Agente**, eles selecionam **Conectar o agente ao Azure Log Analytics** > **Avançar**.

    ![Instalação do MMA](./media/contoso-migration-assessment/mma-install.png)

5. No **Azure Log Analytics**, eles colam o ID do espaço de trabalho e a chave que você copiou do portal.

    ![Instalação do MMA](./media/contoso-migration-assessment/mma-install2.png)

6. Em **pronto para instalar**, eles agora podem instalar o MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Instale o agente de dependência em VMs do Windows

1. Eles clicam duas vezes no agente de dependência baixado.
2. Eles aceitam os termos de licença e aguarde a conclusão da instalação.

    ![Agente de dependência](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Instalar os agentes em VMs do Linux

Eles executarem a instalação em cada máquina virtual.

#### <a name="install-the-mma-on-linux-vms"></a>Instale o MMA em VMs do Linux

1. Instalar a biblioteca do python ctypes sobre como usar cada VM: **apt sudo get instalar python ctypeslib**.
2. Ele deve ser executado o comando para instalar o agente MMA como raiz.  Para se tornar raiz executar o seguinte comando e digite a senha raiz: **sudo -i**.
3. Agora, instalar o agente MMA.
    - Insira seu ID do espaço de trabalho correto e a chave para o comando.
    - Os comandos são para a versão de 64 bits.
    - O **ID do Espaço de Trabalho** e a **Chave Primária** podem ser encontrados dentro do Portal do OMS> **Configurações**, na guia **Fontes Conectadas**.
    - Execute os seguintes comandos para baixar o agente do OMS, validar a soma de verificação e instalar / integrar o agente.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instale o agente de dependência em VMs do Linux

Depois de instalar o MMA, a Contoso pode instalar o agente de dependência nas VMs do Linux.

1. O agente de dependência é instalado em computadores Linux usando InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração automática. Pode executar o arquivo usando sh ou adicionar permissões de execução para o arquivo propriamente dito.

2. Instalar o agente de dependência do Linux como raiz:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Etapa 6: executar e analisar a avaliação de VMs

Agora, a Contoso pode verificar dependências de máquina e criar um grupo. Em seguida, executarem a avaliação para o grupo.

### <a name="verify-dependencies-and-create-a-group"></a>Verifique as dependências e crie um grupo


1. Para as máquinas analisar, clicarem **exibir dependências**.

    ![Exibir dependências de máquina](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Para a SQLVM, o mapa de dependências mostra os seguintes detalhes:

    - Processos e grupos de processo com conexões de rede ativas em execução na SQLVM durante o período de tempo especificado (uma hora, por padrão)
    - Conexões TCP de entrada (cliente) e de saída (servidor) de e para todas as máquinas dependentes.
    - Máquinas dependentes com agentes das Migrações para Azure instalados são exibidas como caixas separadas
    - Máquinas sem os agentes instalados mostram informações de endereço IP e porta.

3. Para computadores com o agente instalado (WEBVM), ele clicar na caixa de máquina para exibir mais informações, incluindo o FQDN, o sistema operacional e o endereço MAC.

    ![Exibir dependências de grupo](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Agora, eles selecionam as VMs para adicionar ao grupo (SQLVM e WEBVM).  Eles podem usar CTRL + clique para selecionar várias VMs.
5. Clicar **criar grupo**e especifique um nome (smarthotelapp).

> [!NOTE]
    > Para exibir dependências mais granulares, você pode expandir o intervalo de tempo. Você pode selecionar uma duração específica ou datas de início e término.


### <a name="run-an-assessment"></a>Ler uma avaliação


1. Sobre o **grupos** página, que abrir o grupo (smarthotelapp) e clique em **criar avaliação**.

    ![Criar uma avaliação](./media/contoso-migration-assessment/run-vm-assessment.png)

2. A avaliação é exibida na página **Gerenciar** > **Avaliações**.

A Contoso usou as configurações de avaliação padrão, mas você pode personalizar as configurações. [Saiba mais](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analisar a avaliação de VMs

Uma avaliação de migração do Azure inclui informações sobre compatibilidade de máquinas virtuais no local do Azure, sugerida dimensionamento da direita para a VM do Azure e estimado mensais custos do Azure.

![Relatório de avaliação](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Revisar classificação de confiança

![Exibição de avaliação](./media/contoso-migration-assessment/assessment-display.png)

Uma avaliação obtém uma classificação de confiança de estrelas de 1 a 5 estrelas (1 estrela, sendo o mais baixo e 5 estrelas, sendo o mais alto).
- A classificação de confiança é atribuída a uma avaliação com base na disponibilidade de pontos de dados necessários para calcular a avaliação.
- A classificação ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pelas Migrações para Azure.
- Classificação de confiança é útil quando você estiver fazendo *baseada em desempenho dimensionamento* como migrar do Azure não pode ter pontos de dados suficientes para fazer o dimensionamento com base em utilização. Para *como dimensionamento local*, a classificação de confiança é sempre de 5 estrelas, uma vez que as Migrações para Azure têm todos os pontos de dados necessários para dimensionar a VM.
- Dependendo do percentual dos pontos de dados disponíveis, é fornecida a classificação de confiança para a avaliação:

   **Disponibilidade dos pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 estrela
   21%-40% | 2 estrelas
   41%-60% | 3 estrelas
   61%-80% | 4 estrelas
   81%-100% | 5 estrelas

#### <a name="verify-readiness"></a>Verificar a preparação

![Preparação para avaliação](./media/contoso-migration-assessment/azure-readiness.png)  

O relatório de avaliação mostra as informações resumidas na tabela. Observe que, para mostrar o dimensionamento com base no desempenho, as Migrações para Azure precisam das informações a seguir. Se essas informações não puderem ser coletadas, a avaliação de dimensionamento poderá não ser exata.

- Dados de utilização de CPU e memória.
- IOPS de leitura/gravação e a taxa de transferência para cada disco anexado à VM.
- Informações de entrada/saída de rede para cada adaptador de rede conectado à VM.


**Configuração** | **Indicação** | **Detalhes**
--- | --- | ---
**Preparação da VM do Azure** | Indica se a VM está pronta para a migração | Possíveis estados:</br><br/>- Pronto para o Azure<br/><br/>- Pronto com condições <br/><br/>- Não pronto para o Azure<br/><br/>- Preparação desconhecida<br/><br/> Se uma VM não estiver pronta, vamos mostrar alguns passos de correção.
**Tamanho da VM do Azure** | Para VMs prontas, recomendamos um tamanho de VM do Azure. | As recomendações de dimensionamento dependem das propriedades da avaliação:<br/><br/>- Se você usou o dimensionamento com base no desempenho, o dimensionamento considerará o histórico de desempenho das VMs.<br/><br/>- Se você usou 'como a local', o dimensionamento será baseado no tamanho da VM local e os dados de utilização não serão usados.
**Ferramenta sugerida** | Como nossos computadores estão executando os agentes, as Migrações para Azure analisam os processos em execução no computador e identificam se ele é um computador de banco de dados ou não.
**Informações da VM** | O relatório mostra as configurações para a VM local, incluindo o sistema operacional, o tipo de inicialização e informações de disco e armazenamento.


#### <a name="review-monthly-cost-estimates"></a>Revisar estimativas de custo mensal

Essa exibição mostra o custo total de computação e armazenamento da execução das máquinas virtuais no Azure, junto com os detalhes de cada máquina.

![Preparação para avaliação](./media/contoso-migration-assessment/azure-costs.png)

- As estimativas de custo são calculadas usando as recomendações de tamanho da máquina.
- Os custos mensais estimados de computação e armazenamento são agregados para todas as VMs no grupo.


## <a name="clean-up-after-assessment"></a>Limpeza após a avaliação

- Após a conclusão da avaliação, a Contoso mantém o utilitário de migração do Azure para avaliações futuras.
- Eles desativam a máquina virtual VMware. Eles irão iniciá-lo novamente ao avaliar VMs adicionais.
- Eles manterão o projeto de migração do Contoso no Azure.  Atualmente, ele é implantado no grupo de recursos ContosoFailoverRG, em nós do Azure região Leste.
-  O coletor de VM tem uma licença de avaliação de 180 dias. Se esse limite expirar, será necessário baixar e configurar o coletor novamente.


## <a name="conclusion"></a>Conclusão

Neste cenário, a Contoso avaliou o banco de dados do aplicativo SmartHotel usando a ferramenta DMA e as VMs locais usando o serviço de migração do Azure. Em seguida, eles revisaram as avaliações para garantir que os recursos locais estivessem prontos para migração para o Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo desta série, a Contoso rehosts seu aplicativo SmartHotel no Azure com uma migração de elevação e deslocamento. A Contoso migra o WEBVM front-end do aplicativo usando o Azure Site Recovery e o banco de dados de aplicativos para uma Instância Gerenciada do Azure SQL, usando o Serviço de Migração de Banco de Dados. [Introdução](contoso-migration-rehost-vm-sql-managed-instance.md) com essa implantação.
