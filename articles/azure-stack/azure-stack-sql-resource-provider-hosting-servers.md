---
title: Servidores na pilha do Azure de hospedagem SQL | Microsoft Docs
description: Como adicionar instâncias do SQL para o provisionamento por meio do provedor de recursos do adaptador de SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265221"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos do SQL

Você pode hospedar uma instância do SQL em uma máquina virtual (VM) em [Azure pilha](azure-stack-poc.md), ou em uma máquina virtual fora de seu ambiente de pilha do Azure, desde que o provedor de recursos do SQL pode se conectar à instância.

## <a name="overview"></a>Visão geral

Os requisitos gerais para servidores de hospedagem SQL são:

* A instância do SQL deve ser dedicada para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância do SQL que está sendo usada por qualquer outro consumidor. Essa restrição também se aplica a serviços de aplicativos.
* O provedor de recursos do SQL VM não está ingressado no domínio e só pode se conectar usando a autenticação do SQL.
* Você deve configurar uma conta com privilégios apropriados para uso pelo provedor de recursos.
* O provedor de recursos e os usuários, como aplicativos Web, usam a rede do usuário, para que conectividade com a instância do SQL nesta rede seja necessária. Esse requisito geralmente significa que o IP de suas instâncias do SQL deve estar em uma rede pública.
* Gerenciamento das instâncias de SQL e seus hosts fica a seu critério. Por exemplo, o provedor de recursos não aplicar atualizações, lidar com backups ou manipular rotação de credencial.
* Você pode usar os SKUs que dão suporte a classes diferentes de recursos do SQL, como desempenho e alta disponibilidade usando o AlwaysOn.

### <a name="sql-server-virtual-machine-images"></a>Imagens de máquina virtual do SQL Server

Imagens de máquinas virtuais IaaS do SQL estão disponíveis por meio do recurso de gerenciamento do Marketplace. Essas imagens são o mesmo que as VMs do SQL que estão disponíveis no Azure.

Certifique-se de sempre baixar a versão mais recente do **extensão SQL IaaS** antes de implantar uma VM usando um item do Marketplace.  A extensão do IaaS e o portal correspondente aprimoramentos fornecem recursos adicionais, como a aplicação de patch automática e de backup.

Há outras opções para a implantação de VMs do SQL, incluindo modelos de [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Quaisquer servidores de hospedagem instalados em uma pilha de vários nós do Azure devem ser criados de uma assinatura de usuário. Eles não não possível criar a assinatura de provedor padrão. Eles devem ser criados no portal de usuário ou uma sessão do PowerShell com um logon adequado. Todos os servidores de hospedagem são faturáveis VMs e devem ter as licenças apropriadas do SQL. O administrador do serviço _pode_ ser o proprietário da assinatura.

### <a name="required-privileges"></a>Privilégios necessários

Você pode criar um usuário administrativo com menos privilégios sysadmin do SQL. O usuário precisa apenas de permissões para as seguintes operações:

- Banco de dados: Criar, alterar com contenção (para sempre em apenas), Drop, Backup
- O grupo de disponibilidade: Alter, Join, adicionar ou remover banco de dados
- Logon: Criar, selecionar, Alter, Drop, revogar
- Selecionar operações: \[mestre\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[mestre\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[mestre\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fornecem a capacidade de conexão com um autônomo que hospeda o SQL server

Você pode usar autônomo (não-HA) servidores SQL usando qualquer edição do SQL Server 2014 ou SQL Server 2016. Verifique se que você tem as credenciais para uma conta com privilégios de administrador do sistema.

Para adicionar um servidor de hospedagem autônomo que já está configurado, siga estas etapas:

1. Entrar no portal de operador de pilha do Azure como um administrador de serviço.

2. Selecione **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL**.

   ![Servidores de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Em **servidores de hospedagem SQL**, você pode conectar o provedor de recursos do SQL para instâncias do SQL Server que servem como back-end do provedor de recursos.

   ![Painel do adaptador de SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Preencha o formulário com os detalhes de conexão da instância do SQL Server.

   ![Adicionar um servidor de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, você pode incluir um nome de instância e especificar um número de porta, se a instância não está atribuída à porta padrão 1433.

   > [!NOTE]
   > Como a instância do SQL pode ser acessada pelo usuário e administrador do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do SQL __deve__ ser atribuídos exclusivamente para o provedor de recursos.

4. Como adicionar servidores, você deve atribuí-los a uma SKU de nova ou existente para diferenciar as ofertas de serviço. Por exemplo, você pode ter uma instância de SQL Enterprise fornece:
  
   - capacidade do banco de dados
   - backup automático
   - reserva de servidores de alto desempenho para departamentos individuais

   Todos os servidores de hospedagem em um SKU devem ter os mesmos recursos. O **nome** deve refletir as propriedades do SKU para que os usuários podem implantar seus bancos de dados para a SKU apropriada.

   > [!IMPORTANT]
   > Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você cria uma SKU para os provedores de recursos do SQL e MySQL.

   Por exemplo: 

   ![Criar SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKUs podem demorar até uma hora para ser visível no portal. Os usuários não é possível criar um banco de dados até que o SKU totalmente seja criado.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornecer alta disponibilidade usando SQL grupos de disponibilidade AlwaysOn

A configuração de instâncias do SQL Always On requer etapas adicionais e requer um mínimo de três máquinas virtuais (ou máquinas físicas). Este artigo pressupõe que você já tem uma compreensão sólida dos grupos de disponibilidade AlwaysOn. Para obter mais informações, consulte:

* [Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Sempre em grupos de disponibilidade (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> O provedor de recursos do adaptador SQL _somente_ oferece suporte a SQL 2016 SP1 Enterprise ou posterior instâncias para AlwaysOn. Essa configuração de adaptador requer novos recursos do SQL, como a propagação automática.

Além da lista anterior de requisitos, você deve habilitar [a propagação automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada grupo de disponibilidade para cada instância do SQL Server.

Para habilitar a propagação automática em todas as instâncias, editar e, em seguida, execute o seguinte comando SQL para cada instância do:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Sobre as instâncias secundárias, editar e, em seguida, execute o seguinte comando SQL para cada instância do:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Para adicionar SQL sempre nos servidores de hospedagem

1. Entrar no portal de administração de pilha do Azure como um administrador de serviço.

2. Selecione **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL** &gt; **+ adicionar**.

   Em **servidores de hospedagem SQL** o provedor de recursos do SQL Server pode se conectar a instâncias do SQL Server reais que servem como back-end do provedor de recursos.

3. Preencha o formulário com os detalhes de conexão para a instância do SQL Server. Certifique-se de que você use o endereço FQDN do ouvinte sempre em (e número da porta opcional.) Forneça as informações para a conta configurada com privilégios de administrador do sistema.

4. Marque a caixa de grupo de disponibilidade AlwaysOn para habilitar o suporte para instâncias de SQL sempre no grupo de disponibilidade.

   ![Habilitar AlwaysOn](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adicione a instância do SQL Always On a uma SKU.

   > [!IMPORTANT]
   > Você não pode misturar servidores autônomos com instâncias de AlwaysOn no SKU do mesmo. Tentativa de combinar tipos depois de adicionar os primeiros resultados de servidor de hospedagem em um erro.

## <a name="make-the-sql-databases-available-to-users"></a>Disponibilizar os bancos de dados SQL para os usuários

Crie planos e ofertas para disponibilizar os bancos de dados SQL para os usuários. Adicionar o **Microsoft.SqlAdapter** para o plano de serviço e adicionar a cota padrão ou criar uma nova cota.

![Criar planos e ofertas para incluir bancos de dados](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Próximas etapas

[Adicionar bancos de dados](azure-stack-sql-resource-provider-databases.md)
