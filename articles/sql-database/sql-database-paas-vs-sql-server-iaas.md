---
title: Banco de Dados SQL (SaaS) versus SQL Server na nuvem em VMs (IaaS) | Microsoft Docs
description: 'Saiba qual opção do SQL Server da nuvem se adapta ao seu aplicativo: Banco de Dados SQL do Azure (PaaS) ou SQL Server na nuvem em Máquinas Virtuais do Azure.'
services: sql-database, virtual-machines
keywords: Nuvem do SQL Server, SQL Server na nuvem, banco de dados PaaS, SQL Server na nuvem, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0b1eda89d2baaa54b17d8a7c73a2e9c987f0f8eb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665978"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)

No Azure, suas cargas de trabalho do SQL Server podem ser executadas em uma infraestrutura hospedada (IaaS) ou como um serviço hospedado ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/): um mecanismo de banco de dados SQL, com base no SQL Server Enterprise Edition, que é otimizado para desenvolvimento de aplicativos modernos. O Banco de Dados SQL do Azure oferece várias opções de implantação:
  - Você pode implementar um único banco de dados em um [servidor lógico](sql-database-logical-servers.md).
  - Você pode implantar em um [conjunto elástico](sql-database-elastic-pool.md) em um [servidor lógico](sql-database-logical-servers.md) para compartilhar recursos e reduzir custos. 

      > [!NOTE]
      > Um Banco de Dados SQL do Azure contendo bancos de dados únicos e agrupados oferece a maioria dos recursos do escopo do banco de dados do SQL Server.

      A ilustração a seguir mostra essas opções de implantação:

      ![Opções de implantação](./media/sql-database-technical-overview/deployment-options.png) 
  - Você pode implantar em um [instâncias de gerenciada de banco de dados (visualização) do Azure SQL](sql-database-managed-instance.md). 

      > [!NOTE]
      > Em ambas as versões, o Banco de Dados SQL do Azure adiciona outros recursos que não estão disponíveis no SQL Server, como gerenciamento e inteligência internos. Com a primeira versão, com a Instância Gerenciada do Banco de Dados SQL do Azure, o Banco de Dados SQL do Azure oferece recursos compartilhados para bancos de dados e recursos adicionais com escopo de instância. A Instância Gerenciada do Banco de Dados SQL do Azure dá suporte à migração de banco de dados com nenhuma alteração no banco de dados ou com uma alteração mínima.
- [SQL Server em Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): o SQL Server instalado e hospedado na nuvem em VMs (máquinas virtuais) do Windows Server ou do Linux, em execução no Azure, também conhecido como IaaS (infraestrutura como serviço). O SQL Server em máquinas virtuais do Azure é uma boa opção para migrar bancos de dados e aplicativos do SQL Server local sem nenhuma alteração no banco de dados. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação em uma máquina virtual de IaaS. A diferença mais significativa do Banco de Dados SQL é que as VMs do SQL Server permitem o controle total sobre o mecanismo de banco de dados. Você pode escolher quando a manutenção/aplicação de patches será iniciada, alterar o modelo de recuperação para simples ou bulk-logged para habilitar o carregamento mais rápido e menos log, pausar ou iniciar o mecanismo quando necessário e personalizar totalmente o mecanismo de banco de dados do SQL Server. Esse controle adicional traz a responsabilidade de gerenciar as máquinas virtuais.

Aprenda como cada opção de implantação se encaixa na plataforma de dados da Microsoft e obtenha ajuda que corresponda à opção correta aos requisitos de sua empresa. Se você priorizar economia ou administração mínima à frente de tudo, este artigo pode ajudá-lo a decidir qual abordagem cumpre os requisitos de negócios com os quais você se preocupa mais.

## <a name="microsofts-sql-data-platform"></a>Plataforma de dados SQL da Microsoft

Uma das primeiras coisas a compreender em qualquer discussão do Azure versus bancos de dados local do SQL Server é que você pode usá-lo. A Plataforma de Dados da Microsoft aproveita a tecnologia do SQL Server e o torna disponível em computadores locais físicos, ambientes de nuvem privada, ambientes de nuvem privada hospedados por terceiros e nuvem pública. O SQL Server nas máquinas virtuais do Azure permite atender a necessidades comerciais exclusivas e diversificadas por meio de uma combinação de implantações locais e hospedadas na nuvem, usando o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e experiência nesses ambientes.

   ![Opções do SQL Server na nuvem: servidor SQL em IaaS ou banco de dados SQL de SaaS na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como visto no diagrama, cada oferta pode ser caracterizada por nível de administração que você tem sobre a infraestrutura (no eixo X) e pelo grau de economia obtida com a consolidação de nível de banco de dados e automação (no eixo Y).

Ao criar um aplicativo, quatro opções básicas estão disponíveis para hospedar a parte do SQL Server do aplicativo:

* SQL Server em computadores físicos não virtualizados
* SQL Server em computadores virtualizados locais (nuvem privada)
* SQL Server na Máquina Virtual do Azure (nuvem pública da Microsoft)
* Banco de Dados SQL do Azure (nuvem pública da Microsoft)

Nas seções a seguir, você aprenderá sobre o SQL Server na nuvem pública da Microsoft: Banco de Dados SQL do Azure e SQL Server nas VMs do Azure. Além disso, irá explorar os motivadores comerciais comuns para determinar qual opção funciona melhor para seu aplicativo.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Uma análise mais aprofundada do Banco de Dados SQL do Azure e do SQL Server em VMs do Azure

O **Banco de Dados SQL do Azure** é um DBaaS (banco de dados relacional como serviço) hospedado na nuvem do Azure que se encaixa na categoria do setor de *PaaS (plataforma como serviço)*. [banco de dados SQL](sql-database-technical-overview.md) baseia-se no hardware e software padronizados da Microsoft, hospedados e mantidos por ela. Com o Banco de Dados SQL, você pode usar recursos e funcionalidades internos que exigem uma configuração abrangente no SQL Server. Quando você usa o Banco de Dados SQL, ele é pré-pago, com opções para escalar verticalmente ou horizontalmente, de modo a ter maior capacidade, sem interrupção. O Banco de Dados SQL do Azure é um ambiente ideal para o desenvolvimento de novos aplicativos na nuvem. E com a [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md), você pode usar sua própria licença. Além disso, essa opção fornece todos os benefícios de PaaS do Banco de Dados SQL do Azure, mas adiciona recursos que, anteriormente, estavam disponíveis somente nas VMs do SQL. Isso inclui uma VNet (rede virtual) nativa e quase 100% de compatibilidade com o SQL Server local. A [Instância Gerenciada](sql-database-managed-instance.md) é ideal para migrações do banco de dados local para o Azure com o mínimo de alterações necessárias. 

**O SQL Server em VMs (Máquinas Virtuais) do Azure** se enquadra na categoria do setor *IaaS (Infraestrutura como Serviço)* e permite executar o SQL Server em uma máquina virtual na nuvem. As [máquinas virtuais do SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) também são executadas no hardware padronizado que pertence à Microsoft e é hospedado e mantido por ela. Ao usar o SQL Server em uma VM, você pode pagar previamente por uma licença do SQL Server já incluída em uma imagem do SQL Server ou usar facilmente uma licença existente. Você também pode interromper ou retomar a VM conforme o necessário.

Em geral, essas duas opções de SQL são otimizadas para finalidades diferentes:

* O **Banco de Dados SQL do Azure** é otimizado para reduzir os custos gerais de gerenciamento ao mínimo para provisionar e gerenciar vários bancos de dados. Ele reduz os custos de administração contínua porque você não precisa gerenciar máquinas virtuais, um sistema operacional nem um software de banco de dados. Você não precisa gerenciar as atualizações, a alta disponibilidade ou os [backups](sql-database-automated-backups.md). Em geral, o Banco de Dados SQL do Azure pode aumentar drasticamente o número de bancos de dados gerenciados por um único recurso de TI ou desenvolvimento. Os [Pools elásticos](sql-database-elastic-pool.md) também dão suporte a arquiteturas de aplicativo multilocatário de SaaS com recursos, como isolamento de locatários e a capacidade de dimensionar para reduzir os custos por meio do compartilhamento de recursos entre os bancos de dados. A [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md) é compatível com recursos com escopo na instância, facilitando a migração de aplicativos existentes e compartilhando recursos entre os bancos de dados.
* **SQL Server em execução nas VMs do Azure** é otimizado para migrar os aplicativos existentes para o Azure ou estender os aplicativos locais existentes para a nuvem em implantações híbridas. Além disso, você pode usar o SQL Server em uma máquina virtual para desenvolver e testar os aplicativos tradicionais do SQL Server. Com o SQL Server em VMs do Azure, você tem os direitos administrativos completos sobre uma instância dedicada do SQL Server e uma VM baseada em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Esses recursos permitem que você crie um sistema altamente personalizado para endereçar os requisitos específicos de desempenho e disponibilidade de seu aplicativo.

A seguinte tabela resume as principais características do Banco de Dados SQL e do SQL Server em VMs do Azure:

| | Banco de Dados SQL do Azure<br>Servidores lógicos, pools elásticos e bancos de dados únicos | Banco de Dados SQL do Azure<br>Instância Gerenciada |Máquina Virtual do Azure<br>SQL Server |
| --- | --- | --- |---|
| **Mais adequado para:** |Novos aplicativos projetados na nuvem que precisam usar os recursos estáveis mais recentes do SQL Server e têm restrições de tempo em relação ao desenvolvimento e ao marketing. | Novos aplicativos ou aplicativos locais existentes que precisam usar os recursos estáveis mais recentes do SQL Server e que são migrados para a nuvem com o mínimo de alterações.  | Aplicativos existentes que requerem uma migração rápida para a nuvem com o mínimo de alterações ou nenhuma alteração. Cenários de rápido desenvolvimento e teste quando você não deseja comprar hardware do SQL Server local de não produção. |
|  | Equipes que precisam da atualização, recuperação de desastres e alta disponibilidade interna para o banco de dados. | Mesmo que o Banco de Dados SQL. | Equipes que podem configurar, ajustar, personalizar e gerenciar a alta disponibilidade, a recuperação de desastre e a aplicação de patch do SQL Server. Alguns recursos automatizados fornecidos simplificam muito isso. | |
|  | Equipes que não desejam gerenciar o sistema operacional subjacente e definições de configuração. | Mesmo que o Banco de Dados SQL. | Você precisa de um ambiente personalizado com direitos administrativos completos. | |
|  | Bancos de dados de até 4 TB ou maiores que podem ser [particionados horizontal ou verticalmente](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) usando um padrão expansão horizontal. | Mesmo que o Banco de Dados SQL. | Instâncias do SQL Server com até 64 TB de armazenamento. A instância pode suportar quantos bancos de dados forem necessários. |
| **Compatibilidade** | É compatível com a maioria dos recursos locais no nível do banco de dados. | É compatível com quase todos os recursos locais no nível da instância e no nível do banco de dados. | É compatível com todos os recursos locais. |
| **Recursos:** | Você não deseja empregar recursos de TI para a configuração e o gerenciamento da infraestrutura subjacente, mas deseja se concentrar na camada do aplicativo. | Mesmo que o Banco de Dados SQL. | Você tem alguns recursos de TI para a configuração e o gerenciamento. Alguns recursos automatizados fornecidos simplificam muito isso. |
| **Custo total de propriedade:** | Elimina os custos de hardware e reduz os custos administrativos. | Mesmo que o Banco de Dados SQL. | Elimina os custos de hardware. |
| **Continuidade dos negócios:** |Além dos [recursos de infraestrutura de tolerância a falhas internos](sql-database-high-availability.md), o Banco de Dados SQL do Azure fornece recursos, como [backups automatizados](sql-database-automated-backups.md), [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore), [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore) e [grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md) para aumentar a continuidade dos negócios. Para saber mais, confira a [Visão geral da continuidade de negócios do Banco de Dados SQL](sql-database-business-continuity.md). | Igual ao Banco de Dados SQL e, além disso, estão disponíveis os backups somente cópia iniciados pelo usuário. | O SQL Server nas VMs do Azure permite que você configure uma solução de alta disponibilidade e recuperação de desastres para as necessidades específicas de seu banco de dados. Portanto, você pode ter um sistema altamente otimizado para seu aplicativo. Você pode testar e executar failovers por conta própria, quando necessário. Para saber mais, consulte [Alta disponibilidade e recuperação de desastre para o SQL Server em máquinas virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Nuvem híbrida:** |Seu aplicativo local pode acessar dados no Banco de Dados SQL do Azure. | [Implementação de rede virtual nativa](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration) e conectividade com seu ambiente local usando o Azure ExpressRoute ou o Gateway de VPN. | Com o SQL Server em VMs do Azure você pode ter aplicativos que são executados parcialmente na nuvem e parcialmente no local. Por exemplo, você pode estender sua rede local e o Domínio do Active Directory para a nuvem por meio da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Além disso, você pode armazenar arquivos de dados locais no Armazenamento do Azure usando os [Arquivos de Dados do SQL Server no Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para saber mais, confira [Introdução à nuvem híbrida do SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Oferece suporte à [replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um assinante para replicar os dados. | A Instância Gerenciada do Banco de Dados SQL do Azure não é compatível com replicação. | É totalmente compatível com a [Replicação transacional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), os [Grupos de Disponibilidade AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), os Integration Services e o envio de logs para replicar dados. Além disso, os backups tradicionais do SQL Server são totalmente suportados | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações de negócios para escolher o Banco de Dados SQL do Azure ou o SQL Server em VMs do Azure
### <a name="cost"></a>Custo
Seja você uma startup sem recursos financeiros ou uma equipe em uma empresa estabelecida que opera com restrições de orçamento apertado, financiamento limitado costuma ser o principal motivador ao decidir como hospedar os bancos de dados. Nesta seção, você aprenderá sobre as noções básicas de cobrança e licenciamento no Azure com relação a estas duas opções de banco de dados relacional: Banco de Dados SQL e SQL Server nas VMs do Azure. Você também aprenderá sobre como calcular o custo total do aplicativo.

#### <a name="billing-and-licensing-basics"></a>Noções básicas de licenciamento e cobrança

Atualmente, o **Banco de Dados SQL** é vendido como um serviço e está disponível em várias camadas de serviço com preços diferentes para recursos, que são todos cobrados por hora a uma taxa fixa com base na camada de serviço e no nível de desempenho escolhido. Com a Instância Gerenciada do Banco de Dados SQL, você também pode usar sua própria licença. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares. Você ajustar as camadas de serviço e os níveis de desempenho dinamicamente para atender às necessidades de produtividade variadas do seu aplicativo. Para obter as informações mais recentes sobre as camadas de serviço atuais com suporte, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). Você também pode criar [pools elásticos](sql-database-elastic-pool.md) para compartilhar recursos entre as instâncias de banco de dados para reduzir os custos e acomodar os picos de uso.

Com o **Banco de Dados SQL**, o software de banco de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, seus recursos de [backup interno](sql-database-automated-backups.md) o ajudam a obter economia significativa, principalmente quando você tem um grande número de bancos de dados. 

Com o **SQL Server nas VMs do Azure**, você pode usar qualquer imagem do SQL Server fornecida pela plataforma (que inclui uma licença) ou utilizar sua licença do SQL Server. Todas as versões com suporte do SQL Server (2008R2, 2012, 2014, 2016) e as edições (Developer, Express, Web, Standard e Enterprise) estão disponíveis. Além disso, as versões BYOL (Traga sua Própria Licença) das imagens estão disponíveis. Ao usar as imagens fornecidas pelo Azure, os custos operacionais dependem do tamanho da VM e da edição do SQL Server escolhida. Independentemente do tamanho da VM ou da edição do SQL Server, você paga o custo de licenciamento por minuto do SQL Server e do Linux ou do Windows Server, juntamente com o custo de Armazenamento do Azure para os discos de VM. A opção de cobrança por minuto permite que você use o SQL Server pelo tempo necessário, sem adquirir licenças adicionais do SQL Server. Se você usar sua própria licença do SQL Server no Azure, serão cobrados apenas os custos de armazenamento e de servidor. Para obter mais informações sobre como utilizar seu próprio licenciamento, consulte [Mobilidade de Licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, você será cobrado pelo tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

#### <a name="calculating-the-total-application-cost"></a>Calculando o custo total do aplicativo
Quando você começar a usar uma plataforma de nuvem, o custo de execução do aplicativo incluirá os custos do novo desenvolvimento e da administração contínua, mais os custos de serviço da plataforma de nuvem pública.

**Ao usar o Banco de Dados SQL do Azure:**

- Custos de administração altamente minimizados
- Custos de desenvolvimento limitados para aplicativos migrados
- Custos de serviço do Banco de Dados SQL
- Não há custos de compra de hardware

**Ao usar o SQL Server em VMs do Azure:**

- Custos de administração mais altos
- Limitado à ausência de custos de desenvolvimento para aplicativos migrados
- Custos de serviço de máquina virtual
- Custos de licença do SQL Server
- Não há custos de compra de hardware

Para obter mais informações sobre preços, consulte os recursos a seguir:

* [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
* [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração
Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem se refere à redução da complexidade de administração, além de seu custo. Com IaaS e PaaS, a Microsoft administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software do banco de dados, gerencia o balanceamento de carga e faz o failover transparente quando há uma falha do servidor em um data center. 

- Com o **Banco de Dados SQL do Azure**, você pode continuar a administrar o banco de dados, mas não é mais necessário gerenciar o mecanismo de banco de dados, o sistema operacional do servidor ou o hardware.  Alguns exemplos de itens que você pode continuar a administrar são bancos de dados e logons, ajuste de índice e consulta e auditoria e segurança. Além disso, a configuração da alta disponibilidade para outro data center requer o mínimo de administração e configuração.
- Com o **SQL Server nas VMs do Azure**, você tem controle total sobre o sistema operacional e a configuração da instância do SQL Server. Em uma VM, cabe a você decidir quando atualizar o sistema operacional e o software do banco de dados, e quando instalar qualquer software adicional, como um antivírus. Alguns recursos automatizados são fornecidos para simplificar muito a aplicação de patches, backup e alta disponibilidade. Além disso, você pode controlar o tamanho da VM, o número de discos e as configurações de armazenamento desses discos. O Azure permite mudar o tamanho de uma VM quando necessário. Para obter informações, consulte [Tamanhos de Máquinas Virtuais e Serviço de Nuvem do Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)
Para vários departamentos de TI, atender às obrigações de tempo de atividade de um SLA (Contrato de Nível de Serviço) é uma grande prioridade. Nesta seção, vamos examinar qual SLA se aplica a cada opção de hospedagem de banco de dados.

Para o **Banco de Dados SQL**, a Microsoft fornece um SLA de 99,99% de disponibilidade. Para obter as últimas informações, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/). 

Para o **SQL Server em execução em VMs do Azure**, a Microsoft fornece um SLA de disponibilidade de 99,95% que abrange apenas a Máquina Virtual. Esse SLA não abrange os processos (como o SQL Server) em execução na VM e exige que você hospede pelo menos duas instâncias de VM em um conjunto de disponibilidade. Para obter as últimas informações, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para HA (alta disponibilidade) de banco de dados em VMs, você deve configurar uma das opções de alta disponibilidade com suporte no SQL Server, como os [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Usar uma opção de alta disponibilidade com suporte não fornece um SLA adicional, mas permite que você atinja > 99,99% de disponibilidade do banco de dados.

### <a name="market"></a>Tempo para mover para o Azure
**Servidores lógicos de Banco de Dados SQL, pools elásticos e bancos de dados individuais** são a solução certa para aplicativos projetados em nuvem, quando a produtividade do desenvolvedor e um tempo de entrega rápido para novas soluções são fatores críticos. Com a funcionalidade de programação como DBA, ele é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciamento do sistema operacional e do banco de dados subjacentes. 

A **Instância Gerenciada do Banco de Dados SQL** simplifica a migração de aplicativos existentes para o Banco de Dados SQL do Azure, permitindo lançar rapidamente no mercado os aplicativos de um banco de dados migrado para o Azure.

O **SQL Server em execução em VMs do Azure** é perfeito se seus aplicativos novos ou existentes exigem bancos de dados grandes ou acessam todos os recursos no SQL Server ou no Windows/Linux e você deseja evitar o tempo e os custos da aquisição de um novo hardware local. Ele também é uma boa opção quando você deseja migrar aplicativos e bancos de dados locais existentes para o Azure no estado em que se encontram, nos casos em que a Instância Gerenciada do Banco de Dados SQL do Azure não é uma boa opção. Já que você não precisa alterar a apresentação, o aplicativo e as camadas de dados, você economiza tempo e orçamento em rearquitetura da solução existente. Em vez disso, você pode se concentrar em migrar todas as soluções para o Azure e fazer algumas otimizações de desempenho que possam ser necessárias para a plataforma Microsoft Azure. Para obter mais informações, veja [Práticas Recomendadas de Desempenho para o SQL Server em Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Resumo
Este artigo explorou o Banco de Dados SQL e o SQL Server em VMs (Máquinas Virtuais) do Azure e discutiu motivadores comerciais comuns que podem afetar sua decisão. Este é um resumo de sugestões a serem consideradas:

Escolha o **Banco de Dados SQL do Azure** se:

* Você está compilando novos aplicativos baseados em nuvem para aproveitar a economia de custos e a otimização do desempenho que os serviços de nuvem fornecem. Essa abordagem oferece os benefícios de um serviço de nuvem totalmente gerenciado, ajuda a diminuir o tempo de colocação no mercado e pode fornecer otimização de custos a longo prazo.
* Você deseja que a Microsoft execute operações de gerenciamento comuns em seus bancos de dados e exija SLAs de maior disponibilidade para bancos de dados.
* Você deseja migrar um aplicativo existente no estado em que se encontra para a Instância Gerenciada do Banco de Dados SQL do Azure e aproveitar a paridade adicional com o SQL Server e/ou a segurança avançada e a rede. A Instância Gerenciada é uma boa opção para aplicativos novos e existentes.

Escolha **SQL Server em VMs do Azure** se:

* Você tem aplicativos locais existentes que deseja migrar ou estender para a nuvem ou se deseja compilar aplicativos empresariais maiores que 4 TB. Essa abordagem oferece o benefício de usar a versão e a edição do SQL Server da sua escolha, uma grande capacidade do banco de dados, o controle total sobre o SQL Server e o Windows/Linux e um túnel seguro para o local. A abordagem reduz os custos de desenvolvimento e modificações dos aplicativos existentes.
* Você tem recursos de TI existentes e pode ter basicamente patches, backups e alta disponibilidade do banco de dados. Observe que alguns recursos automatizados simplificam muito essas operações. 

## <a name="next-steps"></a>Próximas etapas

* Consulte [Seu primeiro Banco de Dados SQL do Azure](sql-database-get-started-portal.md) para uma introdução ao Banco de Dados SQL.
* Confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* Confira [Provisionar uma máquina virtual do SQL Server no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) para obter uma introdução ao SQL Server em VMs do Azure.
