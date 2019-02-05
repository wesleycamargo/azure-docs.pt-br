---
title: Início Rápido – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como começar rapidamente a usar a Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468165"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Comece a usar a Instância Gerenciada do Banco de Dados SQL do Azure

A [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-index.yml) é uma versão de PaaS totalmente gerenciada do SQL Server hospedada na nuvem do Azure e colocada em sua própria rede virtual com o endereço IP privado. Nesta seção, você aprenderá como configurar e criar rapidamente uma Instância Gerenciada e como migrar seus bancos de dados.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Nesta seção, você terá uma visão geral dos artigos disponíveis que podem ajudá-lo a se familiarizar rapidamente com as Instâncias Gerenciadas. A maneira mais fácil de criar sua primeira Instância Gerenciada é usar o [portal do Azure](sql-database-managed-instance-get-started.md), no qual você pode configurar os parâmetros necessários (nome de usuário/senha, número de núcleos, armazenamento máximo) e criar automaticamente o ambiente de rede do Azure sem precisar conhecer os detalhes da rede e os requisitos de infraestrutura. Apenas certifique-se de ter um [tipo de assinatura](sql-database-managed-instance-resource-limits.md#supported-subscription-types) com permissão para criar a instância.

Se você tiver sua própria rede que deseja usar ou se quiser personalizar a rede, veja como [configurar o ambiente de rede](#configure-network-environment) para a Instância Gerenciada.

Quando cria sua Instância Gerenciada, você precisa se conectar à instância usando uma das seguintes abordagens:

* Crie uma [Máquina Virtual do Azure](sql-database-managed-instance-configure-vm.md) com o SQL Server Management Studio instalado e com outros aplicativos que podem ser usados para acessar a Instância Gerenciada em uma sub-rede dentro da mesma rede virtual em que a Instância Gerenciada for colocada. A máquina virtual não pode estar na mesma sub-rede que suas Instâncias Gerenciadas.
* Configure a [Conexão ponto a site](sql-database-managed-instance-configure-p2s.md) em seu computador, que permitirá que você una seu computador à rede virtual na qual a Instância Gerenciada está e use a Instância Gerenciada como qualquer outro SQL Server na rede.

Como alternativa, você pode usar o ExpressRoute ou a conexão site a site de sua rede local, mas essas abordagens estão fora do escopo destes inícios rápidos.

Quando cria uma Instância Gerenciada e configura o acesso, você pode começar a migrar seus bancos de dados colocados no SQL Server local ou em máquinas virtuais do Azure. Observe que a migração falhará se você tiver alguns recursos sem suporte no banco de dados de origem que deseja migrar. Para evitar falhas e verificar a compatibilidade, você pode instalar o [AMD (Assistente de Migração de Dados)](https://www.microsoft.com/download/details.aspx?id=53595), que analisará seus bancos de dados no SQL Server e encontrará qualquer problema que possa bloquear a migração para a Instância Gerenciada, como a existência de FileStream ou de vários arquivos de log. Se você resolver esses problemas, seus bancos de dados estarão prontos para ir para a Instância Gerenciada. O [Assistente para Experimentos de Banco de Dados](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) é outra ferramenta útil que pode gravar sua carga de trabalho do SQL Server e reproduzi-la na Instância Gerenciada para que você possa determinar se haverá problemas de desempenho se você migrar para a Instância Gerenciada.

Depois que tiver certeza de que pode migrar seu banco de dados para a Instância Gerenciada, você poderá usar a funcionalidade [RESTAURAR nativa](sql-database-managed-instance-get-started-restore.md), que permite que você crie um backup do banco de dados usando o comando Transact-SQL, carregue-o em um armazenamento de blobs do Azure e RESTAURE o banco de dados do armazenamento de blobs usando o comando Transact-SQL.

Esses guias de início rápido permitem que você configure, crie e coloque rapidamente seus bancos de dados nas Instâncias Gerenciadas. Em alguns cenários, você precisaria personalizar ou automatizar a implantação da Instância Gerenciada e do ambiente de rede necessário. Esses cenários serão descritos abaixo.

## <a name="customizing-network-environment"></a>Personalizar o ambiente de rede

Embora a rede virtual/sub-rede possa ser configurada automaticamente quando a instância é criada usando o [portal do Azure](sql-database-managed-instance-get-started.md), talvez seja bom criá-la antes de começar a criar as Instâncias Gerenciadas, porque é possível configurar os parâmetros da rede virtual e da sub-rede. A maneira mais fácil de criar e configurar o ambiente de rede é usar o modelo de [implantação de Recursos do Azure](sql-database-managed-instance-create-vnet-subnet.md), que criará e configurará sua rede e a sub-rede onde a instância será colocada. Basta pressionar o botão de implantação do Azure Resource Manager e preencher o formulário com os parâmetros. Como alternativa, você pode usar o [script do PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar a criação da rede.

Se já tiver uma rede virtual e uma sub-rede onde deseja implantar a Instância Gerenciada, verifique se sua rede virtual e sua sub-rede satisfazem os [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Você deverá usar esse [script do PowerShell para verificar se sua sub-rede está configurada corretamente](sql-database-managed-instance-configure-vnet-subnet.md). Esse script não apenas validará sua rede e relatará os problemas – ele informará o que precisa ser alterado e se oferecerá para fazer as alterações necessárias em sua rede virtual/sub-rede. Execute este script se você não quiser configurar a rede virtual/sub-rede manualmente, e execute-o também após qualquer reconfiguração de grande porte em sua infraestrutura de rede. Se você quiser criar e configurar sua própria rede, leia a [Documentação da Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md) e [este guia](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automatizando a criação da Instância Gerenciada

 Se você não tiver criado o ambiente de rede conforme descrito na etapa anterior, o portal do Azure poderá fazer isso para você – a única desvantagem é o fato de que ele o configura com alguns parâmetros padrão que você não pode alterar posteriormente. Como alternativa, você pode usar o [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), o [PowerShell com modelo do Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) ou a [CLI do Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migrando para a Instância Gerenciada com tempo de inatividade mínimo

Os artigos nesses inícios rápidos permitem que você configure a Instância Gerenciada e mova seus bancos de dados rapidamente. No entanto, com a restauração nativa, você precisaria aguardar os bancos de dados serem restaurador, o que causaria algum tempo de inatividade de seu aplicativo, especialmente se o banco de dados for maior. Se estiver movendo seu banco de dados de produção, você provavelmente precisará de uma forma melhor de migração, que garanta um tempo de inatividade mínimo. O [Serviço de Migração de Dados](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) é um serviço de migração que pode migrar seu banco de dados com tempo de inatividade mínimo enviando por push incrementalmente as alterações feitas no banco de dados de origem para um banco de dados que você está restaurando para a Instância Gerenciada. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

## <a name="next-steps"></a>Próximas etapas

* Encontre uma [lista de alto nível dos recursos com suporte na Instância Gerenciada aqui](sql-database-features.md) e [detalhes e problemas conhecidos aqui](sql-database-managed-instance-transact-sql-information.md). 
* Saiba mais sobre as [características técnicas da Instância Gerenciada](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Encontre tutoriais mais avançados [na seção de instruções](sql-database-howto-managed-instance.md). 
