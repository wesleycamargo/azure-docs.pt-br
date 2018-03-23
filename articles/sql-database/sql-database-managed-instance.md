---
title: Visão geral da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve uma Instância Gerenciada do Banco de Dados SQL do Azure e explica como funciona e se difere de um banco de dados individual no Banco de Dados SQL do Azure.
services: sql-database
documentationcenter: na
author: bonova
ms.reviewer: carlrab
manager: cguyer
editor: ''
ms.assetid: ''
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: dc3c93a1a13f3e10f9159d26411d6337c0269722
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="what-is-a-managed-instance-preview"></a>O que é uma Instância Gerenciada (versão prévia)?

A Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia) é um novo recurso do Banco de Dados SQL do Azure que proporciona quase 100% de compatibilidade com o SQL Server local, fornecendo uma implementação de [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) nativa que aborda questões de segurança comuns e um [modelo corporativo](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do Cliente do Microsoft SQL Server local. A Instância Gerenciada permite que os clientes do SQL Server existentes façam lift-and-shift dos aplicativos locais para a nuvem com alterações mínimas do banco de dados e aplicativo. Ao mesmo tempo, a Instância Gerenciada preserva todos os recursos de PaaS (aplicação automática de patches e atualizações de versões, backup, alta disponibilidade), que reduz drasticamente a sobrecarga de gerenciamento e o TCO.
 
O diagrama a seguir apresenta os principais recursos da Instância Gerenciada:

![principais recursos](./media/sql-database-managed-instance/key-features.png)

A Instância Gerenciada é idealizada como plataforma preferencial para os seguintes cenários: 

- Usuários do SQL Server local/IaaS que procuram migrar os aplicativos para um serviço totalmente gerenciado com alterações de design mínimas.
- ISVs baseados em Bancos de Dados SQL, que querem habilitar seus clientes a migrarem para a nuvem e, desse modo, obterem uma vantagem competitiva substancial ou alcance no mercado global. 

Por Disponibilidade Geral, a Instância Gerenciada visa entregar aproximadamente 100% de compatibilidade de área de superfície com a última versão do SQL Server local por meio de um plano de lançamento em etapas. 

A tabela a seguir apresenta as principais diferenças e os cenários de uso previstos entre SQL IaaS, Banco de Dados SQL do Azure e Instância Gerenciada:

| | Cenário de uso | 
| --- | --- | 
|Instância Gerenciada |Para clientes que procuram migrar uma grande quantidade de aplicativos locais ou IaaS, auto-compilados, ou ISV fornecidos, com o menor esforço de migração possível, propõe-se a Instância Gerenciada. Utilizando o [DMS (Serviço de Migração de Dados) ](/sql/dma/dma-overview) totalmente automatizado no Azure, os clientes podem fazer lift-and-shift do SQL Server local para uma Instância Gerenciada que oferece compatibilidade com o SQL Server local e isolamento completo de instâncias do cliente com suporte nativo de VNET.  Com o Software Assurance, é possível trocar suas licenças existentes por tarifas com desconto em uma Instância Gerenciada do Banco de Dados SQL usando o [Benefício de uso híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  A Instância Gerenciada do Banco de Dados SQL é o melhor destino da migração na nuvem para instâncias do SQL Server que exigem alta segurança e uma superfície de programação avançada. |
|Banco de Dados SQL do Azure |Para os clientes que desenvolvem novos aplicativos multilocatários SaaS ou intencionalmente transformando seus aplicativos locais existentes em um aplicativo multilocatário SaaS, propõe-se pools elásticos. Os benefícios desse modelo são: <br><ul><li>Conversão do modelo de negócios da venda de licenças para venda de assinaturas de serviços (para ISVs)</li></ul><ul><li>Isolamento de locatário fácil e à prova de marcador</li></ul><ul><li>Um modelo de programação centrada em banco de dados simplificado</li></ul><ul><li>O potencial para escalar horizontalmente atingir um limite rígido</li></ul>Para clientes que desenvolvem novos aplicativos diferentes do multilocatário SaaS, cuja carga de trabalho é estável e previsível, propõe-se bancos de dados individuais. Os benefícios desse modelo são:<ul><li>Um modelo de programação centrada em banco de dados simplificado</li></ul>  <ul><li>Desempenho previsível para cada banco de dados</li></ul>|
|SQL IaaS |Para clientes que necessitam personalizar o sistema operacional ou o servidor de banco de dados, assim como clientes que possuem requisitos específicos em termos de execução de aplicativos de terceiros junto com SQL Server (na mesma VM), propõe-se IaaS/VMs do SQL como a solução ideal|
|||

![posicionamento](./media/sql-database-managed-instance/positioning.png)

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Como identificar programaticamente uma Instância Gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact-SQL que podem ser utilizadas para detectar se o aplicativo está trabalhando com a Instância Gerenciada e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 07/03/2018 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente a Instância Gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS da instância completo no seguinte formato:<instanceName>.<dnsPrefix>.database.windows.net, onde <instanceName> é o nome fornecido pelo cliente, enquanto <dnsPrefix> é a parte gerada automaticamente do nome, garantindo exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Principais recursos e capacidades de uma Instância Gerenciada 

| **Benefícios de PaaS** | **Continuidade dos negócios** |
| --- | --- |
|Sem gerenciamento e compra de hardware <br>Sem sobrecarga de gerenciamento para gerenciar infraestrutura subjacente <br>Rápido provisionamento e dimensionamento de serviço <br>Aplicação de patch automatizado e atualização da versão <br>Integração com outros serviços de dados PaaS |99,99% do SLA de tempo de atividade  <br>Compilado em alta disponibilidade <br>Dados protegidos com backups automatizados <br>Período de retenção de backup configurável pelo cliente (corrigido para 7 dias na Visualização Pública) <br>Backups iniciados pelo usuário <br>Capacidade de restauração pontual do banco de dados |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado (integração de VNet, serviço de locatário único, computação dedicada e armazenamento <br>Criptografia dos dados em trânsito <br>Autenticação do Microsoft Azure AD, suporte de logon único <br>Cumpre os padrões de conformidade assim como o Banco de Dados SQL do Azure <br>Auditoria do SQL <br>Detecção de ameaças |API do Azure Resource Manager para automatizar o dimensionamento e provisionamento do serviço <br>Funcionalidade do Portal do Azure para dimensionamento e provisionamento manual do serviço <br>Serviço de Migração de Dados 

![logon único](./media/sql-database-managed-instance/sso.png) 

## <a name="managed-instance-service-tier"></a>Camada de serviço de Instância Gerenciada

Inicialmente, a Instância Gerenciada está disponível em uma única camada de serviço - Uso Geral - projetada para aplicativos com disponibilidade típica e requisitos de latência de E/S comuns.

A lista a seguir apresenta a principal característica da camada de serviço de Uso Geral: 

- Design para a maioria dos aplicativos de negócios com desempenho típico e requisitos de HA 
- Armazenamento Premium do Azure de alto desempenho (8 TB) 
- 100 bancos de dados/instâncias 

Nessa camada, é possível selecionar de forma independente a capacidade de computação e o armazenamento. 

O diagrama a seguir ilustra a computação ativa e os nós redundantes nessa camada de serviço.
 
![Camada de serviço de Uso Geral](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

A seguir, são descritos os principais recursos da camada de serviço de Uso Geral:

|Recurso | DESCRIÇÃO|
|---|---|
| Número de vCores* | 8, 16, 24|
| Compilação/versão do SQL Server | SQL Server (mais recente disponível) |
| Tamanho mínimo de armazenamento | 32 GB |
| Tamanho máximo de armazenamento | 8 TB |
| IOPS de armazenamento esperado | 500-7500 IOPS por arquivo de dados (depende do arquivo de dados). Consulte [Armazenamento Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Número de arquivos de dados (LINHAS) por banco de dados | Vários | 
| Número de arquivos de log (LOG) por banco de dados | 1 | 
| Backups automatizados gerenciados | sim |
| HA | Com base em armazenamento remoto e [Microsoft Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Métricas e monitoramento de banco de dados e instância interna | sim |
| Aplicação automática de patches de software | sim |
| VNet - Implantação do Azure Resource Manager | sim |
| VNet - Modelo de implantação clássico | Não  |
| Suporte do Portal | sim|
|||

\* Um núcleo virtual representa a CPU lógica oferecida com uma opção para escolher entre gerações de hardware. As CPUs Lógicas Geração 4 são baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz e as CPUs Lógicas Geração 5 são baseadas em processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz.  

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas 

### <a name="managed-instance-security-isolation"></a>Isolamento de segurança da Instância Gerenciada 

A Instância Gerenciada fornece isolamento de segurança adicional de outros locatários na nuvem do Azure. O isolamento de segurança inclui: 

- Implementação e conectividade de rede virtual nativa com o ambiente local usando a Rota Expressa do Azure ou Gateway de VPN 
- O ponto de extremidade do SQL é exposto apenas por meio de um endereço IP privado, permitindo conectividade segura a partir de redes híbridas ou privadas do Azure
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento)

O diagrama a seguir apresenta o design de isolamento: 

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Auditoria de segurança e conformidade 

A [auditoria](sql-database-auditing.md) de Instância Gerenciada rastreia eventos de banco de dados e os grava em um log de auditoria na conta de armazenamento do Azure. A auditoria pode ajudar a manter conformidade com as normas, entender a atividade do banco de dados e ter ideia das discrepâncias e anomalias que podem gerar preocupações comerciais ou violações suspeitas de seguranças. 

### <a name="data-encryption-in-motion"></a>Criptografia dos dados em trânsito 

A Instância Gerenciada protege os dados, fornecendo criptografia para dados em movimentação usando o protocolo TLS.

Além do protocolo TLS, a Instância Gerenciada do Banco de Dados SQL oferece proteção de dados confidenciais em trânsito, em repouso e durante processamento de consulta com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted é pioneiro na indústria, oferecendo segurança de dados incomparável contra violações envolvendo o roubo de dados críticos. Por exemplo, com o Always Encrypted, números de cartão de crédito são armazenados sempre criptografados no banco de dados, mesmo durante a consulta de processamento, permitindo a descriptografia no ponto de uso por pessoal autorizado ou aplicativos que precisam processar os dados. 

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos 

A [máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) do Banco de Dados SQL limita a exposição de dados confidenciais por meio da máscara dos dados para usuários sem privilégios. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados. 

### <a name="row-level-security"></a>Segurança em nível de linha 

A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). A RLS (Segurança em Nível de Linha) simplifica o design e codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários possam acessar apenas as linhas de dados pertinentes ao seu departamento ou restringindo o acesso a dados apenas para dados relevantes. 

### <a name="threat-detection"></a>Detecção de ameaças 

A [Detecção de Ameaças](sql-database-threat-detection.md) do Banco de Dados SQL do Azure complementa a auditoria, fornecendo uma camada adicional de inteligência de segurança compilada para o serviço que detecta tentativas incomuns e potencialmente perigosas de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas da Detecção de Ameaças podem ser exibidos no [Azure Security Center](https://azure.microsoft.com/services/security-center/) e fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator 

O Banco de Dados SQL permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](sql-database-aad-authentication.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com [MFA](sql-database-ssms-mfa-authentication-configure.md) (autenticação multifator) para aumentar a segurança de aplicativos e dados e dá suporte a um processo de logon único. 

### <a name="authentication"></a>Autenticação 
A autenticação do Banco de Dados SQL refere-se a como os usuários comprovam a identidade ao conectarem-se ao banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:  

- Autenticação do SQL, que usa um nome de usuário e senha.
- Autenticação do Azure Active Directory, que usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados.  

### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um Banco de Dados SQL do Azure e é controlado pelas associações a uma função de banco de dados e pelas permissões no nível de objeto da conta de usuário. A Instância Gerenciada possui os mesmos recursos de autorização que o SQL Server 2017. 

## <a name="database-migration"></a>Migração de banco de dados 

A Instância Gerenciada direciona cenários de usuários com migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS.  A Instância Gerenciada oferece suporte a várias opções de migração de banco de dados: 

### <a name="data-migration-service"></a>Serviço de Migração de Dados

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo.   Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e SQL Server existentes para o Azure. As opções de implantação incluem Banco de Dados SQL do Azure, Instance Gerenciada e SQL Server na VM do Azure em Visualização Pública. Consulte [Como migrar o banco de dados local para a Instância Gerenciada usando DMS](https://aka.ms/migratetoMIusingDMS).  

### <a name="backup-and-restore"></a>Backup e restauração  

A abordagem de migração aproveita backups do SQL para Azure Storage Blob. Backups armazenados no Azure Storage Blob podem ser restaurados diretamente na Instância Gerenciada. 

## <a name="sql-features-supported"></a>Recursos do SQL com suporte 

A Instância Gerenciada visa entregar aproximadamente 100% de compatibilidade de área de superfície com o SQL Server local em etapas, até a disponibilidade geral do serviço. Para um recurso e lista de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
 
A Instância Gerenciada tem suporte para compatibilidade com versões anteriores para Bancos de Dados do SQL 2008.  A migração direta dos servidores do Banco de Dados do SQL 2005 tem suporte, o nível de compatibilidade para Bancos de Dados do SQL 2005 migrados é atualizado para o SQL 2008. 
 
O diagrama a seguir apresenta a compatibilidade da área de superfície na Instância Gerenciada:  

![migração](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Principais diferenças entre SQL Server local e Instância Gerenciada 

A Instância Gerenciada se beneficia de estar sempre atualizada na nuvem, o que significa que alguns recursos no SQL Server local podem estar obsoletos, desativados ou ter alternativas.  Há casos específicos em que as ferramentas precisam reconhecer que um recurso particular funciona de forma ligeiramente diferente ou que o serviço não está executando em um ambiente que não totalmente controlado: 

- Alta disponibilidade é compilada e pré-configurada. Alta disponibilidade Always On não são expostos da mesma forma que nas implementações SQL IaaS 
- Backups automatizados e restauração pontual. O cliente pode iniciar backups `copy-only` que não interferem na cadeia de backup automático. 
- A Instância Gerenciada não permite especificar caminhos físicos completos, então, todos os cenários correspondentes devem ter suporte de forma diferente: RESTORE DB não dá suporte para WITH MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona apenas com Blobs do Azure e etc. 
- A Instância Gerenciada dá suporte para [Autenticação do Azure AD](sql-database-aad-authentication.md) como alternativa de nuvem para autenticação do Windows. 
- A Instância Gerenciada gerencia automaticamente o grupo de arquivos XTP e os arquivos para bancos de dados que contêm objetos OLTP na memória
 
### <a name="managed-instance-administration-features"></a>Recursos de administração de Instância Gerenciada do Banco de Dados SQL do Azure  

A Instância Gerenciada permite que o administrador do sistema concentre-se no que é mais importante para os negócios. Muitas atividades DBA/administrador do sistema não são necessárias ou são simples. Por exemplo, instalação do RDBMS/SO e aplicação de patch, redimensionamento de instância dinâmica e configuração, backups, replicação de banco de dados (incluindo bancos de dados do sistema), configuração de alta disponibilidade e configuração de fluxos de dados de monitoramento de desempenho e integridade.  

> [!IMPORTANT]
> Para obter uma lista de recursos com suporte, suporte parcial e sem suporte, consulte [Recursos do Banco de Dados SQL](sql-database-features.md). Para obter uma lista de diferenças T-SQL em Instâncias Gerenciadas em comparação com SQL Server, consulte [Diferenças T-SQL de Instância Gerenciada do SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Próximas etapas

- Para um recurso e lista de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para obter um tutorial que cria uma Instância Gerenciada e restaura um banco de dados de um arquivo de backup, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-tutorial-portal.md).
- Para obter um tutorial usando o DMS (Serviço de Migração de Banco de Dados do Azure) para migração, consulte [Migração de Instância Gerenciada usando DMS](../dms/tutorial-sql-server-to-managed-instance.md).
