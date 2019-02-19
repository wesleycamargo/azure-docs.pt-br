---
title: Início Rápido – Instância gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como começar rapidamente a usar o Banco de Dados SQL do Azure – instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 3940c2f239a4354cfb44a499f7375f4ba34f8aa8
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892020"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introdução à instância gerenciada do Banco de Dados SQL do Azure

A opção de implantação de [instância gerenciada](sql-database-managed-instance-index.yml) cria um banco de dados com quase 100% de compatibilidade com o Mecanismo de Banco de Dados (Enterprise Edition) local do SQL Server, fornecendo uma implementação de [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) nativa que resolve preocupações comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes do SQL Server locais. Nesta seção, você aprenderá como configurar e criar rapidamente uma instância gerenciada e como migrar seus bancos de dados.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Os inícios rápidos a seguir permitem que você crie rapidamente uma instância gerenciada, configure uma máquina virtual ou aponte para a conexão VPN do site para o aplicativo cliente e restaure um banco de dados para sua nova instância gerenciada usando um arquivo `.bak`:

- [Criar uma instância gerenciada usando o portal do Azure](sql-database-managed-instance-get-started.md). No portal do Azure, você configura os parâmetros necessários (nome de usuário/senha, número de núcleos, armazenamento máximo) e cria automaticamente o ambiente de rede do Azure sem precisar conhecer os detalhes da rede e os requisitos de infraestrutura. Certifique-se de ter um [tipo de assinatura](sql-database-managed-instance-resource-limits.md#supported-subscription-types) com permissão para criar uma instância gerenciada. Se você tiver uma rede própria que deseja usar ou se desejar personalizar a rede, confira como configurar o ambiente de rede para uma instância gerenciada.
- Uma instância gerenciada é criada na própria VNet sem nenhum ponto de extremidade público. Para ter acesso a aplicativos cliente, é possível criar uma VM na mesma VNet (sub-rede diferente) ou criar uma conexão VPN ponto a site com a VNet do computador cliente usando um desses inícios rápidos.
  - Crie uma [máquina virtual do Azure na VNet de instância gerenciada](sql-database-managed-instance-configure-vm.md) para obter conectividade de aplicativo cliente, incluindo o SQL Server Management Studio.
  - Configure a [conexão VPN ponto a site com sua instância gerenciada](sql-database-managed-instance-configure-p2s.md) do computador cliente no qual você tem o SQL Server Management Studio e outros aplicativos de conectividade de cliente. Esta é a outra das duas opções para conectividade com sua instância gerenciada e com sua VNet.

  > [!NOTE]
  > Também é possível usar a rota expressa ou a conexão site a site da rede local, mas essas abordagens estão fora do escopo desses inícios rápidos.

Após você criar uma instância gerenciada e configurar o acesso, será possível começar a migrar os bancos de dados colocados no SQL Server local ou em VMs do Azure. A migração falhará se você tiver alguns recursos sem suporte no banco de dados de origem que você deseja migrar. Para evitar falhas e verificar a compatibilidade, você pode instalar o [AMD (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595), que analisa seus bancos de dados no SQL Server e encontra qualquer problema que possa bloquear a migração para uma instância gerenciada, como a existência de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou de vários arquivos de log. Se você resolver esses problemas, seus bancos de dados estarão prontos para migrar para a instância gerenciada. O [Assistente para Experimentos de Banco de Dados](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) é outra ferramenta útil que pode registrar a carga de trabalho no SQL Server e reproduzi-la em uma instância gerenciada para que você possa determinar se haverá problemas de desempenho se você migrar para uma instância gerenciada.

Após ter a certeza de que você pode migrar o banco de dados para uma instância gerenciada, será possível usar as funcionalidades nativas de restauração do SQL Server para restaurar um banco de dados em uma instância gerenciada de um arquivo `.bak`. Para um início rápido, confira [Restore from backup to a managed instance](sql-database-managed-instance-get-started-restore.md) (Restaurar do backup para uma instância gerenciada). Neste início rápido, você restaurará de um arquivo `.bak` armazenado no Armazenamento de Blobs do Azure usando o comando `RESTORE` Transact-SQL. 

> [!TIP]
> Para usar o comando `BACKUP` Transact-SQL para criar um backup do banco de dados no Armazenamento de Blobs do Azure, confira [Backup do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estes inícios rápidos permitem que você crie, configure e restaure um backup de banco de dados rapidamente para uma instância gerenciada. Em alguns cenários, você precisaria personalizar ou automatizar a implantação de instâncias gerenciadas e do ambiente de rede necessário. Esses cenários serão descritos abaixo.

## <a name="customize-network-environment"></a>Personalizar ambiente de rede

Embora a VNet/sub-rede possa ser configurada automaticamente quando a instância é criada usando o portal do Azure, talvez convenha criar a VNet/sub-rede antes de começar a criar instâncias gerenciadas para você poder configurar os parâmetros da VNet e da sub-rede. A maneira mais fácil de criar e configurar o ambiente de rede é usar um modelo de [implantação do Azure Resource](sql-database-managed-instance-create-vnet-subnet.md) para criar e configurar a rede e a sub-rede da instância gerenciada. Basta pressionar o botão de implantação do Azure Resource Manager e preencher o formulário com os parâmetros. 

Como alternativa, também é possível usar este [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se você já tiver uma VNet e uma sub-rede em que você gostaria de implantar sua instância gerenciada, será necessário garantir que a VNet e a sub-rede satisfaçam os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Use este [script do PowerShell para verificar se sua sub-rede foi configurada corretamente](sql-database-managed-instance-configure-vnet-subnet.md). Este script valida sua rede, relata quaisquer problemas e informa o que deve ser alterado e, em seguida, se oferece para fazer as alterações necessárias em sua VNet/sub-rede. Execute este script se você não desejar configurar sua VNet/sub-rede manualmente. Também será possível executá-lo após qualquer importante reconfiguração da infraestrutura de rede. Se desejar criar e configurar sua própria rede, leia [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md) e este[guia definitivo para criar e configurar um ambiente de instância gerenciada](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-a-managed-instance"></a>Automatizando a criação de uma instância gerenciada

 Se você não tiver criado o ambiente de rede conforme descrito na etapa anterior, o portal do Azure poderá fazer isso para você – a única desvantagem é o fato de que ele o configura com alguns parâmetros padrão que você não pode alterar posteriormente. Como alternativa, é possível usar:

- [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
- [PowerShell com modelo do Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [CLI do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>Migrando para uma instância gerenciada com tempo de inatividade mínimo

Os artigos nestes inícios rápidos permitem que você configure uma instância gerenciada rapidamente e mova seus bancos de dados usando a funcionalidade `RESTORE` nativa. No entanto, com o `RESTORE` nativo, é necessário aguardar até que os bancos de dados sejam restaurados (e copiados para o Armazenamento de Blobs do Azure caso ainda não estejam armazenados lá). Isso causa algum tempo de inatividade do seu aplicativo, principalmente para bancos de dados maiores. Para mover o banco de dados de produção, use o [DMS (Serviço de Migração de Dados)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar o banco de dados com tempo de inatividade mínimo. O DMS faz isso enviando por push as alterações feitas no banco de dados de origem em incrementos para o banco de dados da instância gerenciada que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

## <a name="next-steps"></a>Próximas etapas

- Encontre uma [lista de alto nível dos recursos com suporte na Instância Gerenciada aqui](sql-database-features.md) e [detalhes e problemas conhecidos aqui](sql-database-managed-instance-transact-sql-information.md).
- Saiba mais sobre as [características técnicas da Instância Gerenciada](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
- Encontre instruções mais avançadas em [como usar uma instância gerenciada no Banco de Dados SQL do Azure](sql-database-howto-managed-instance.md). 
