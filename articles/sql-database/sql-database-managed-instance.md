---
title: Visão geral da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve uma Instância Gerenciada do Banco de Dados SQL do Azure e explica como funciona e se difere de um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: e89245a946848e46f3c7c502b6cd0e8017327e07
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419887"
---
# <a name="use-sql-database-managed-instance-with-virtual-networks-and-near-100-compatibility"></a>Use a Instância Gerenciada do Banco de Dados SQL com redes virtuais e quase 100% de compatibilidade

A Instância Gerenciada do Banco de Dados SQL do Azure é um novo modelo de implantação do Banco de Dados SQL do Azure, fornecendo quase 100% de compatibilidade com o mais recente Mecanismo de Banco de Dados do SQL Server (Enterprise Edition) local, fornecendo uma implementação de [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) nativa que aborda questões de segurança comuns e um [modelo corporativo](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do SQL Server local. A Instância Gerenciada permite que os clientes do SQL Server existentes façam lift-and-shift dos aplicativos locais para a nuvem com alterações mínimas do banco de dados e aplicativo. Ao mesmo tempo, a Instância Gerenciada preserva todos os recursos de PaaS (aplicação automática de patches e atualizações de versão, [backups automatizados](sql-database-automated-backups.md), [alta disponibilidade](sql-database-high-availability.md) ), reduzindo drasticamente a sobrecarga de gerenciamento e TCO.

> [!IMPORTANT]
> Para obter uma lista de regiões nas quais a Instância Gerenciada está disponível no momento, consulte [regiões suportadas](sql-database-managed-instance-resource-limits.md#supported-regions).

O diagrama a seguir apresenta os principais recursos da Instância Gerenciada:

![principais recursos](./media/sql-database-managed-instance/key-features.png)

A Instância Gerenciada do Banco de Dados SQL do Azure foi desenvolvida para clientes que desejam migrar um grande número de aplicativos do ambiente local ou IaaS, autocompilados, ou fornecido pelo ISV para o ambiente de nuvem de PaaS totalmente gerenciado, com o menor esforço de migração possível. Usando o [Serviço de Migração de Dados (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) totalmente automatizado no Azure, os clientes podem elevar e deslocar seu SQL Server local para uma Instância Gerenciada que oferece compatibilidade com o SQL Server local e isolamento completo de instâncias do cliente com suporte nativo VNet.  Com o Software Assurance, você pode trocar suas licenças existentes por tarifas com desconto em uma Instância Gerenciada do Banco de Dados SQL usando o [Benefício Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  A Instância Gerenciada do Banco de Dados SQL é o melhor destino da migração na nuvem para instâncias do SQL Server que exigem alta segurança e uma superfície de programação avançada.

Por Disponibilidade Geral, a Instância Gerenciada visa entregar aproximadamente 100% de compatibilidade de área de superfície com a última versão do SQL Server local por meio de um plano de lançamento em etapas.

Para decidir entre o Banco de Dados Individual do Banco de Dados SQL do Azure, a Instância Gerenciada do Banco de Dados SQL do Azure e o IaaS do SQL Server hospedado em Máquina Virtual, consulte [como escolher a versão correta do SQL Server na nuvem do Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Principais recursos e capacidades

A Instância Gerenciada do Banco de Dados SQL do Azure combina os melhores recursos disponíveis no Banco de Dados SQL do Azure e no Mecanismo de BD do SQL Server.

> [!IMPORTANT]
> Uma Instância Gerenciada executa com todos os recursos da versão mais recente do SQL Server, incluindo operações online, correções de plano automático e outros aprimoramentos de desempenho do enterprise. Uma comparação dos recursos disponíveis é explicada em [Comparação de recursos: Banco de Dados SQL do Azure versus SQL Server](sql-database-features.md).

| **Benefícios de PaaS** | **Continuidade dos negócios** |
| --- | --- |
|Sem gerenciamento e compra de hardware <br>Sem sobrecarga de gerenciamento para gerenciar infraestrutura subjacente <br>Rápido provisionamento e dimensionamento de serviço <br>Aplicação de patch automatizado e atualização da versão <br>Integração com outros serviços de dados PaaS |99,99% do SLA de tempo de atividade  <br>Compilado em [alta disponibilidade](sql-database-high-availability.md) <br>Dados protegidos com [backups automatizados](sql-database-automated-backups.md) <br>Período de retenção de backup configurável pelo cliente (corrigido para 7 dias na Visualização Pública) <br>[Backups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) iniciados pelo usuário <br>Capacidade de [restauração pontual do banco de dados](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado ([Integração de VNet](sql-database-managed-instance-vnet-configuration.md), serviço de locatário único, computação e armazenamento dedicados) <br>[Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure AD](sql-database-aad-authentication.md), suporte de logon único <br>Cumpre os padrões de conformidade assim como o Banco de Dados SQL do Azure <br>[Auditoria do SQL](sql-database-managed-instance-auditing.md) <br>[Detecção de Ameaças](sql-database-managed-instance-threat-detection.md) |API do Azure Resource Manager para automatizar o dimensionamento e provisionamento do serviço <br>Funcionalidade do Portal do Azure para dimensionamento e provisionamento manual do serviço <br>Serviço de Migração de Dados

Os principais recursos de instância gerenciada são mostrados na tabela a seguir:

|Recurso | DESCRIÇÃO|
|---|---|
| Compilação/versão do SQL Server | Mecanismo de BD do SQL Server (estável mais recente) |
| Backups automatizados gerenciados | SIM |
| Métricas e monitoramento de banco de dados e instância interna | SIM |
| Aplicação automática de patches de software | SIM |
| Os recursos mais recentes do mecanismo de banco de dados | SIM |
| Número de arquivos de dados (LINHAS) por banco de dados | Vários |
| Número de arquivos de log (LOG) por banco de dados | 1 |
| VNet - Implantação do Azure Resource Manager | SIM |
| VNet - Modelo de implantação clássico | Não  |
| Suporte do Portal | SIM|
| Serviço de Integração Integrado (SSIS) | Não - o SSIS faz parte da [PaaS do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Serviço de análise interna (SSAS) | Não - SSAS é separado [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Serviço interno de relatório (SSRS) | Não - usar o Power BI ou no IaaS do SSRS |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) na Instância Gerenciada oferece flexibilidade, controle, transparência e uma maneira direta de traduzir os requisitos de carga de trabalho local para a nuvem. Este modelo permite que você altere computação, memória e armazenamento com base em suas necessidades de carga de trabalho. O modelo vCore também é elegível para economias de até 30% com o [benefício híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

No modelo vCore, você pode escolher entre gerações de hardware.

- **Gen 4** As CPUs lógicas são baseadas nos processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD conectado, núcleos físicos, 7 GB de RAM por núcleo e tamanhos de computação entre 8 e 24 vCores.
- **Gen 5** As CPUs lógicas são baseadas nos processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz, no rápido eNVM SSD, no núcleo lógico de hyper-threaded e nos tamanhos de computação entre 8 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware em [limites de recursos da Instância Gerenciada](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Camadas de serviço de Instância Gerenciada

A instância gerenciada está disponível em duas camadas de serviço:

- **Uso Geral**: projetada para aplicativos com desempenho típico e requisitos de latência de E/S.
- **Comercialmente Crítico (versão prévia)**: projetada para aplicativos com baixos requisitos de latência de E/S e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambas as camadas de serviço garantem 99,99% de disponibilidade e permitem que você selecione de maneira independente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade do Banco de Dados SQL do Azure, consulte [Alta Disponibilidade e Banco de Dados SQL do Azure](sql-database-high-availability.md).

> [!IMPORTANT]
> Não há suporte para alterar sua camada de serviço de uso geral para Comercialmente Crítico ou vice-versa em visualização pública. Se você quiser migrar seus bancos de dados para uma instância na camada de serviço diferentes, pode criar a nova instância, restaurar os bancos de dados com recuperação pontual da instância original e, em seguida, descarte a instância original se não for mais necessária. No entanto, você pode dimensionar seu número de vCores e armazenamento para cima ou para baixo em uma camada de serviço sem tempo de inatividade.

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral

A lista a seguir apresenta a principal característica da camada de serviço de Uso Geral:

- Design para a maioria dos aplicativos de negócios com requisitos de desempenho típicos
- Armazenamento Premium do Azure de alto desempenho (8 TB)
- [Alta disponibilidade](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) integrada, baseada no confiável Armazenamento do Azure Premium e no [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Para obter mais informações, consulte [Camada de armazenamento em Camada de propósitos gerais](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e [práticas recomendadas de desempenho de armazenamento e considerações para a Instância gerenciada do banco de dados SQL do Azure (finalidade geral)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Encontre mais informações sobre a diferença entre níveis de serviço em [limites de recursos da instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier-preview"></a>Camada de serviço Comercialmente Crítico (versão prévia)

Camada de serviço Comercialmente Crítico desenvolvida para aplicativos com altos requisitos de E/S. Ele oferece maior resiliência a falhas usando várias réplicas isoladas.

A lista a seguir apresenta as características principais da camada Comercialmente Crítico:

- Projetada para aplicativos de negócios com requisitos de alta disponibilidade e desempenho mais alto
- Vem com armazenamento SSD super rápido (até 1 TB no Gen 4 e até 4 TB no Gen 5)
- Alta disponibilidade [integrada](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) com base nos [Grupos sempre disponíveis de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e no [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Built-in adicional [somente leitura réplica de banco de dados](sql-database-read-scale-out.md) que pode ser usado para relatórios e outras cargas de trabalho somente leitura
- [OLTP In-Memory](sql-database-in-memory.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

> [!IMPORTANT]
> A camada de serviço **Comercialmente Crítico** está em versão prévia.

Encontre mais informações sobre a diferença entre níveis de serviço em [limites de recursos da instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Instância Gerenciada do Banco de Dados SQL do Azure combina os recursos de segurança avançados fornecidos pela nuvem do Azure e pelo Mecanismo de BD do SQL Server.

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Isolamento de segurança de Instância Gerenciada na nuvem do Azure

A Instância Gerenciada fornece isolamento de segurança adicional de outros locatários na nuvem do Azure. O isolamento de segurança inclui:

- [Implementação e conectividade de rede virtual nativa](sql-database-managed-instance-vnet-configuration.md) com o ambiente local usando a Rota Expressa do Azure ou Gateway de VPN
- O ponto de extremidade do SQL é exposto apenas por meio de um endereço IP privado, permitindo conectividade segura a partir de redes híbridas ou privadas do Azure
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento)

O diagrama a seguir descreve várias opções de conectividade para seus aplicativos:

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração VNet e a imposição de políticas de rede no nível da sub-rede, consulte [Configurar uma Instância Gerenciada do VNet para Banco de Dados SQL do Azure](sql-database-managed-instance-vnet-configuration.md) e [Conecte seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Coloque várias instâncias gerenciadas na mesma sub-rede, onde quer que o permitido pela seus requisitos de segurança, como o que trará benefícios adicionais. Disposição das instâncias na mesma sub-rede será significativamente simplificar a manutenção de infraestrutura de rede e reduzir o tempo, o provisionamento, pois o provisionamento de longa duração está associado ao custo de implantação de primeira instância gerenciada em uma sub-rede de instância.

### <a name="azure-sql-database-security-features"></a>Recursos de segurança do Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece um conjunto de recursos de segurança avançados que podem ser usados para proteger os dados.

- A [auditoria de Instância Gerenciada](sql-database-managed-instance-auditing.md) rastreia eventos de banco de dados e grava-os em um arquivo de log de auditoria colocado na conta de armazenamento do Azure. A auditoria pode ajudar a manter conformidade com as normas, entender a atividade do banco de dados e ter ideia das discrepâncias e anomalias que podem gerar preocupações comerciais ou violações suspeitas de seguranças.
- Criptografia de dados em movimento - A Instância Gerenciada protege os dados, fornecendo criptografia para dados em movimento usando TLS. Além do protocolo TLS, a Instância Gerenciada do Banco de Dados SQL oferece proteção de dados confidenciais em trânsito, em repouso e durante processamento de consulta com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é pioneiro na indústria, oferecendo segurança de dados incomparável contra violações envolvendo o roubo de dados críticos. Por exemplo, com o Always Encrypted, números de cartão de crédito são armazenados sempre criptografados no banco de dados, mesmo durante a consulta de processamento, permitindo a descriptografia no ponto de uso por pessoal autorizado ou aplicativos que precisam processar os dados.
- A [Detecção de Ameaças](sql-database-managed-instance-threat-detection.md) complementa a [auditoria de Instância Gerenciada](sql-database-managed-instance-auditing.md), fornecendo uma camada adicional de inteligência de segurança compilada para o serviço que detecta tentativas incomuns e potencialmente perigosas de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas da Detecção de Ameaças podem ser exibidos no [Azure Security Center](https://azure.microsoft.com/services/security-center/) e fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.  
- [Dynamic data masking](/sql/relational-databases/security/dynamic-data-masking) limits sensitive data exposure by masking it to non-privileged users. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados.
- A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). A RLS (Segurança em Nível de Linha) simplifica o design e codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários possam acessar apenas as linhas de dados pertinentes ao seu departamento ou restringindo o acesso a dados apenas para dados relevantes.
- A [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) criptografa arquivos de dados de Instância Gerenciada do Banco de Dados SQL do Azure, conhecido como dados de criptografia em repouso. A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos arquivos de log e de dados. A criptografia usa uma chave de criptografia de banco de dados (DEK), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação. Você pode proteger todos os seus bancos de dados na instância gerenciada com a criptografia transparente de dados. A TDE é a tecnologia de criptografia ociosa comprovada da SQL exigida por vários padrões de conformidade para proteger contra roubo de mídia de armazenamento. Durante a visualização pública, o modelo de gerenciamento automático de chaves é compatível (realizada pela plataforma de PaaS).

Há suporte para a migração de um banco de dados criptografado para a instância gerenciada do SQL por meio do serviço de migração de banco de dados do Azure (DMS) ou restauração nativa. Se você planeja migrar o banco de dados criptografado usando restauração nativa, a migração do certificado TDE existente do SQL Server no local ou VM do SQL Server para instância gerenciada é uma etapa necessária. Para obter mais informações sobre as opções de migração consulte [migração da instância do SQL Server à Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integração do Active Directory do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure dá suporte a logons e logons de mecanismo de banco de dados do SQL Server integrados ao AAD (Azure Active Directory). Logons do AAD são versão de nuvem do Azure de logons de banco de dados do Windows que você está usando no ambiente local.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

A Instância Gerenciada permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com [MFA](sql-database-ssms-mfa-authentication-configure.md) (autenticação multifator) para aumentar a segurança de aplicativos e dados e dá suporte a um processo de logon único.

### <a name="authentication"></a>Autenticação

A Instância Gerenciada refere-se a como os usuários comprovam a identidade ao conectarem-se ao banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:  

- **Autenticação do SQL**:

  Este método de autenticação usa um nome de usuário e senha.
- **Autenticação do Active Directory do Azure**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um Banco de Dados SQL do Azure e é controlado pelas associações a uma função de banco de dados e pelas permissões no nível de objeto da conta de usuário. A Instância Gerenciada possui os mesmos recursos de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de banco de dados

A Instância Gerenciada direciona cenários de usuários com migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS. A Instância Gerenciada oferece suporte a várias opções de migração de banco de dados:

### <a name="backup-and-restore"></a>Backup e restauração  

A abordagem de migração aproveita backups do SQL para Azure Storage Blob. Os backups armazenados no blob de armazenamento do Azure podem ser restaurados diretamente na Instância Gerenciada usando o [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Para obter um início rápido mostrando como restaurar a Wide World Importers – Arquivo de backup do banco de dados padrão, consulte [Restaurar um arquivo de backup para uma Instância Gerenciada](sql-database-managed-instance-get-started-restore.md). Este início rápido mostra que você precisa carregar um arquivo de backup para o armazenamento de blog do Azure e o proteger usando uma chave de SAS (assinatura de acesso compartilhado).
- Para obter informações sobre restauração de URL, consulte [Restauração nativa de URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Backups de uma Instância Gerenciada só podem ser restaurados para outra Instância Gerenciada. Eles não podem ser restaurados para um SQL Server local ou para um servidor lógico do Banco de Dados SQL do Azure único ou em banco de dados em pool.

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e SQL Server existentes para o Azure. As opções de implantação incluem Banco de Dados SQL do Azure, Instance Gerenciada e SQL Server na VM do Azure em Visualização Pública. Consulte [Como migrar o banco de dados local para a Instância Gerenciada usando DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

A Instância Gerenciada visa entregar aproximadamente 100% de compatibilidade de área de superfície com o SQL Server local em etapas, até a disponibilidade geral do serviço. Para uma lista de recursos e comparação, consulte [Comparação de recursos do Banco de Dados SQL](sql-database-features.md) e para uma lista de diferenças de T-SQL em Instâncias Gerenciadas em comparação com o SQL Server, consulte [Diferenças do T-SQL da Instância Gerenciada do SQL Server](sql-database-managed-instance-transact-sql-information.md).

A Instância Gerenciada tem suporte para compatibilidade com versões anteriores para Bancos de Dados do SQL 2008. A migração direta dos servidores do Banco de Dados do SQL 2005 tem suporte, o nível de compatibilidade para Bancos de Dados do SQL 2005 migrados é atualizado para o SQL 2008.
  
O diagrama a seguir apresenta a compatibilidade da área de superfície na Instância Gerenciada:  

![migração](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Principais diferenças entre SQL Server local e Instância Gerenciada

A Instância Gerenciada se beneficia de estar sempre atualizada na nuvem, o que significa que alguns recursos no SQL Server local podem estar obsoletos, desativados ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que um recurso particular funciona de forma ligeiramente diferente ou que o serviço não está executando em um ambiente que não totalmente controlado:

- Alta disponibilidade baseia-se e pré-configurado usando a tecnologia semelhante [grupos de disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Backups automatizados e restauração pontual. O cliente pode iniciar backups `copy-only` que não interferem na cadeia de backup automático.
- A Instância Gerenciada não permite especificar caminhos físicos completos, então, todos os cenários correspondentes devem ter suporte de forma diferente: RESTORE DB não dá suporte para WITH MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona apenas com Blobs do Azure e etc.
- A Instância Gerenciada dá suporte para [Autenticação do Azure AD](sql-database-aad-authentication.md) como alternativa de nuvem para autenticação do Windows.
- A Instância Gerenciada gerencia automaticamente o grupo de arquivos XTP e os arquivos para bancos de dados que contêm objetos OLTP na memória
- Instância gerenciada suporta SSIS (SQL Server Integration Services) e pode hospedar SSIS catalog (SSISDB) que armazena pacotes SSIS, mas eles são executados em um Azure-ISR (Azure-SSIS Integration Runtime) no Azure Data Factory (ADF), consulte [ Crie o IR do Azure-SSIS no ADF ](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Para comparar os recursos do SSIS no Banco de Dados SQL e na Instância Gerenciada, confira [Comparar o servidor lógico do Banco de Dados SQL e a Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance).

### <a name="managed-instance-administration-features"></a>Recursos de administração de Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada permite que o administrador do sistema concentre-se no que é mais importante para os negócios. Muitas atividades DBA/administrador do sistema não são necessárias ou são simples. Por exemplo, instalação do RDBMS/SO e aplicação de patch, redimensionamento de instância dinâmica e configuração, backups, [replicação de banco de dados](replication-with-sql-database-managed-instance.md) (incluindo bancos de dados do sistema), configuração de alta disponibilidade e configuração de fluxos de dados de [monitoramento de desempenho](../log-analytics/log-analytics-azure-sql.md) e integridade.

> [!IMPORTANT]
> Para obter uma lista de recursos com suporte, suporte parcial e sem suporte, consulte [Recursos do Banco de Dados SQL](sql-database-features.md). Para obter uma lista de diferenças T-SQL em Instâncias Gerenciadas em comparação com SQL Server, consulte [Diferenças T-SQL de Instância Gerenciada do SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Como identificar programaticamente uma Instância Gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact-SQL que podem ser utilizadas para detectar se o aplicativo está trabalhando com a Instância Gerenciada e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 07/03/2018 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente a Instância Gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS da instância completo no seguinte formato:`<instanceName>`.`<dnsPrefix>`.database.windows.net, onde `<instanceName>` é o nome fornecido pelo cliente, enquanto `<dnsPrefix>` é a parte gerada automaticamente do nome, garantindo exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira Instância Gerenciada, consulte o [Guia de início rápido](sql-database-managed-instance-get-started.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para saber mais sobre a configuração de rede virtual, confira [Configuração de VNet de Instância Gerenciada](sql-database-managed-instance-vnet-configuration.md).
- Para obter um início rápido que cria uma Instância Gerenciada e restaura um banco de dados de um arquivo de backup, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Para obter um tutorial usando o DMS (Serviço de Migração de Banco de Dados do Azure) para a migração, confira [Migração de Instância Gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Para monitoramento avançado de desempenho de banco de dados de Instância Gerenciada com inteligência de solução de problemas, confira [Monitorar Banco de Dados SQL do Azure usando a Análise de SQL do Azure ](../log-analytics/log-analytics-azure-sql.md) 
- Para saber mais sobre preços, consulte [Preços do Banco de Dados SQL gerenciados](https://azure.microsoft.com/pricing/details/sql-database/managed/).
