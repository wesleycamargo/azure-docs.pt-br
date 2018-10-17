---
title: Oferecer bancos de dados SQL altamente disponíveis no Azure Stack | Microsoft Docs
description: Saiba como criar um provedor de recursos do SQL Server computador host e bancos de dados altamente disponíveis do AlwaysOn do SQL com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: cb659a79f374b7a48cd3cc29f9f46bc6598c04dc
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49366563"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Tutorial: Oferecer bancos de dados SQL altamente disponíveis

Como um operador de pilha do Azure, você pode configurar as VMs de servidor para hospedar bancos de dados do SQL Server. Depois de um SQL server de hospedagem com êxito é criado e gerenciado pelo Azure Stack, os usuários que se inscreveu para serviços do SQL podem criar facilmente bancos de dados SQL.

Este tutorial mostra como usar um modelo de início rápido do Azure Stack para criar uma [grupo de disponibilidade do AlwaysOn do SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), adicioná-lo como um servidor de hospedagem do SQL do Azure Stack e, em seguida, criar um banco de dados SQL altamente disponível.

O que você aprenderá:

> [!div class="checklist"]
> * Criar um grupo de disponibilidade do AlwaysOn do SQL Server de um modelo
> * Criar um servidor de hospedagem de SQL do Azure Stack
> * Criar um banco de dados SQL altamente disponível

Neste tutorial, um grupo de disponibilidade de VM do SQL Server AlwaysOn duas será criado e configurado usando os itens disponíveis do marketplace do Azure Stack. 

Antes de iniciar as etapas neste tutorial, certifique-se de que o [provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) foi instalado com êxito e os seguintes itens disponíveis no marketplace do Azure Stack:

> [!IMPORTANT]
> Todos os itens a seguir são necessários para o modelo de início rápido do Azure Stack a ser usado.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) imagem do marketplace.
- SQL Server 2016 SP1 ou SP2 (Enterprise, Standard ou desenvolvedor) na imagem de servidor do Windows Server 2016. Este tutorial usa o [SQL Server 2016 SP2 Enterprise no Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) imagem do marketplace.
- [Extensão de IaaS do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) versão 1.2.30 ou superior. A extensão SQL IaaS instala componentes necessários que são necessários para os itens do SQL Server do Marketplace para todas as versões do Windows. Ele permite que as configurações específicas do SQL a ser configurado em máquinas virtuais do SQL. Se a extensão não estiver instalada no mercado local, o provisionamento do SQL irá falhar.
- [Extensão de script personalizado para Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) versão 1.9.1 ou superior. Extensão de Script personalizado é uma ferramenta que pode ser usada para iniciar automaticamente as tarefas de personalização de VM de pós-implantação.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) versão 2.76.0.0 ou superior. DSC é uma plataforma de gerenciamento do Windows PowerShell que permite implantar e gerenciar dados de configuração para serviços de software e gerenciar o ambiente no qual esses serviços são executados.

Para saber mais sobre como adicionar itens para o Azure Stack marketplace, consulte o [visão geral do Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Criar um grupo de disponibilidade do AlwaysOn do SQL Server
Use as etapas nesta seção para implantar o grupo de disponibilidade do AlwaysOn do SQL Server usando o [modelo de início rápido do Azure Stack de alwayson do sql 2016](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Esse modelo implanta duas instâncias de SQL Server Enterprise, Standard ou desenvolvedor em um grupo de disponibilidade AlwaysOn. Ele cria os seguintes recursos:

- Um grupo de segurança de rede
- Uma rede virtual
- Quatro contas de armazenamento (uma para o Active Directory (AD), um para SQL, para a testemunha de compartilhamento de arquivo e outra para diagnóstico da VM)
- Quatro endereços IP públicos (um para o AD, dois para cada VM do SQL e outra para o balanceador de carga público associado ao ouvinte AlwaysOn do SQL)
- Externo de um balanceador de carga para VMs do SQL com IP público associado ao ouvinte de AlwaysOn do SQL
- Uma VM (Windows Server 2016) configurada como controlador de domínio para uma nova floresta com um único domínio
- Duas VMs (Windows Server 2016) configurado com o SQL Server 2016 SP1 ou SP2 Enterprise, Standard ou Developer Edition e clusterizados. Eles devem ser imagens do marketplace.
- Uma VM (Windows Server 2016) configurada como a testemunha de compartilhamento de arquivo para o cluster
- Um conjunto que contém a testemunha de compartilhamento SQL e o arquivo de VMs de disponibilidade  

1. Entrar no portal de administração:
    - Para uma implantação de sistema integrado, o endereço do portal variará com base na região de sua solução e o nome de domínio externo. Ele será no formato https://adminportal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
    - Se você estiver usando o Azure Stack desenvolvimento ASDK (Kit), o endereço do portal de usuário está [ https://adminportal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selecione **\+** **criar um recurso** > **personalizado**e então **implantação de modelo**.

   ![Implantação de modelo personalizado](media/azure-stack-tutorial-sqlrp/custom-deployment.png)


3. Sobre o **implantação personalizada** folha, selecione **Editar modelo** > **modelo de início rápido** e, em seguida, use a lista suspensa de modelos personalizados disponíveis para Selecione o **alwayson do sql 2016** modelo, clique em **Okey**e então **salvar**.

   ![Selecione o modelo de início rápido](./media/azure-stack-tutorial-sqlrp/quickstart-template.png)


4. Sobre o **implantação personalizada** folha, selecione **Editar parâmetros** e examine os valores padrão. Modifique os valores conforme o necessário para fornecer todas as informações de parâmetro necessário e, em seguida, clique em **Okey**.<br><br> No mínimo:

    - Forneça senhas complexas para os parâmetros ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD e SQLAUTHPASSWORD.
    - Insira o sufixo DNS para a pesquisa inversa todas as letras minúsculas para o parâmetro DNSSUFFIX (**azurestack.external** para instalações de ASDK).
    
    ![Parâmetros de implantação personalizada](./media/azure-stack-tutorial-sqlrp/edit-parameters.png)

5. Sobre o **implantação personalizada** folha, escolha a assinatura para usar e criar um novo grupo de recursos ou selecione um grupo de recursos para a implantação personalizada.<br><br> Em seguida, selecione o local do grupo de recursos (**local** para instalações de ASDK) e, em seguida, clique em **criar**. As configurações de implantação personalizado serão validadas e, em seguida, a implantação será iniciada.

    ![Parâmetros de implantação personalizada](./media/azure-stack-tutorial-sqlrp/create-deployment.png)


6. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que você criou para a implantação personalizada (**grupo de recursos** neste exemplo). Exiba o status da implantação para garantir que todas as implantações foram concluídas com êxito.<br><br>Em seguida, examine os itens do grupo de recursos e selecione o **SQLPIPsql\<nome do grupo de recursos\>**  item do endereço IP público. Registre o endereço IP público e o FQDN completo do IP público do balanceador de carga. Você precisará passar essa informação para um operador de pilha do Azure, para que possam criar um servidor de hospedagem SQL aproveitando a esse grupo de disponibilidade do AlwaysOn do SQL.

   > [!NOTE]
   > A implantação de modelo levará várias horas para ser concluída.

   ![Parâmetros de implantação personalizada](./media/azure-stack-tutorial-sqlrp/deployment-complete.png)

### <a name="enable-automatic-seeding"></a>Habilitar a propagação automática
Depois que o modelo foi implantado e configurado o grupo de disponibilidade do AlwaysON do SQL com êxito, você deve habilitar [a propagação automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada instância do SQL Server no grupo de disponibilidade. 

Quando você cria um grupo de disponibilidade com propagação automática, SQL Server cria automaticamente as réplicas secundárias para cada banco de dados no grupo sem qualquer outra intervenção manual necessário para garantir a alta disponibilidade dos bancos de dados AlwaysOn.

Use estes comandos SQL para configurar a propagação automática para o grupo de disponibilidade do AlwaysOn.

Na instância SQL primária (substitua <InstanceName> com o nome do SQL Server de instância principal):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Script de instância principal do SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

Em instâncias do SQL secundárias (substitua < availability_group_name > pelo nome do grupo de disponibilidade AlwaysOn):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Script de instância SQL secundário](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configurar a autenticação de banco de dados independente
Antes de adicionar um banco de dados independente a um grupo de disponibilidade, verifique se a opção de servidor de autenticação de banco de dados independente é definida como 1 em cada instância de servidor que hospeda uma réplica de disponibilidade para o grupo de disponibilidade. Para obter mais informações, consulte [autenticação de banco de dados independente](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Use estes comandos para definir a opção de servidor de autenticação de banco de dados independente para cada instância do SQL Server no grupo de disponibilidade:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Conjunto de autenticação de banco de dados](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Criar um servidor de hospedagem de SQL do Azure Stack
Depois que o grupo de disponibilidade do AlwaysOn do SQL Server foi criado e configurado corretamente, um operador de pilha do Azure deve criar uma pilha de SQL Server do Azure de hospedagem para disponibilizar a capacidade adicional para que os usuários criem bancos de dados. 

Certifique-se de usar o IP público ou o FQDN completo para o IP público do balanceador de carga SQL registrados anteriormente ao grupo de recursos do grupo de disponibilidade AlwaysOn do SQL foi criado (**SQLPIPsql\<nome do grupo de recursos\>** ). Além disso, você precisa saber as credenciais de autenticação usadas para acessar as instâncias do SQL no grupo de disponibilidade AlwaysOn do SQL Server.

> [!NOTE]
> Esta etapa deve ser executada a partir do portal de administração do Azure Stack por um operador do Azure Stack.

Com o ouvinte do balanceador de carga do grupo de disponibilidade AlwaysOn do SQL informações de logon de autenticação de IP público e o SQL, um operador do Azure Stack pode agora [criar um servidor de hospedagem do SQL usando o grupo de disponibilidade do AlwaysOn do SQL](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Também Certifique-se de que você criar planos e ofertas para disponibilizar a criação de banco de dados do SQL AlwaysOn para os usuários. O operador será necessário adicionar o **Microsoft.SqlAdapter** para um plano de serviço e criar uma nova cota especificamente para bancos de dados altamente disponíveis. Para obter mais informações sobre a criação de planos, consulte [visão geral do plano, oferta, cotas e assinatura](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> O **Microsoft.SqlAdapter** serviço não estará disponível para adicionar a planos até que o [provedor de recursos do SQL Server tiver sido implantado](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Criar um banco de dados SQL altamente disponível
Após o SQL AlwaysOn grupo de disponibilidade foi criado, configurado e adicionado como um servidor de hospedagem SQL do Azure Stack por um operador de pilha do Azure, um usuário de locatário com uma assinatura, incluindo recursos de banco de dados do SQL Server pode criar bancos de dados SQL que dão suporte a Funcionalidade AlwaysOn, seguindo as etapas nesta seção. 

> [!NOTE]
> Execute estas etapas de portal do usuário do Azure Stack como um usuário de locatário com uma assinatura do fornecimento de recursos do SQL Server (Microsoft.SQLAdapter service).

1. Entrar no portal do usuário:
    - Para uma implantação de sistema integrado, o endereço do portal variará com base na região de sua solução e o nome de domínio externo. Ele será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
    - Se você estiver usando o Azure Stack desenvolvimento ASDK (Kit), o endereço do portal de usuário está [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selecione **\+** **criar um recurso** > **dados \+ armazenamento**e então **banco de dados SQL**.<br><br>Forneça as informações de propriedade de banco de dados necessário incluindo nome, agrupamento, tamanho máximo e a assinatura, grupo de recursos e local a ser usado para a implantação. 

   ![Criar banco de dados SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Selecione **SKU** e, em seguida, escolha a SKU adequada SQL de servidor de hospedagem para usar. Neste exemplo, o operador de pilha do Azure criou a **Enterprise HA** SKU para oferecer suporte a alta disponibilidade para grupos de disponibilidade do AlwaysOn do SQL.

   ![Selecione a SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Selecione **Login** > **criar um novo logon** e, em seguida, forneça as credenciais de autenticação do SQL a ser usado para o novo banco de dados. Quando terminar, clique em **Okey** e, em seguida **criar** para iniciar o processo de implantação de banco de dados.

   ![Criar logon](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Quando a implantação do banco de dados SQL é concluída com êxito, examine as propriedades de banco de dados para descobrir a cadeia de caracteres de conexão a ser usado para conectar-se ao novo banco de dados altamente disponível. 

   ![Exibir a cadeia de conexão](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um grupo de disponibilidade do AlwaysOn do SQL Server de um modelo
> * Criar um servidor de hospedagem de SQL do Azure Stack
> * Criar um banco de dados SQL altamente disponível

Avance para o próximo tutorial para aprender como:
> [!div class="nextstepaction"]
> [Criar bancos de dados MySQL altamente disponíveis](azure-stack-tutorial-mysql.md)