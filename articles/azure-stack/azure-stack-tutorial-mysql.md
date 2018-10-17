---
title: Oferecer bancos de dados MySQL altamente disponíveis no Azure Stack | Microsoft Docs
description: Saiba como criar um provedor de recursos do servidor MySQL computador host e bancos de dados MySQL altamente disponíveis com o Azure Stack.
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
ms.openlocfilehash: 2197d197e68528866c892cc51323bc61a208bcc0
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49366566"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Tutorial: Oferecer bancos de dados MySQL altamente disponíveis

Como um operador de pilha do Azure, você pode configurar as VMs de servidor para hospedar bancos de dados do servidor MySQL. Após um MySQL cluster com êxito é criado e gerenciado pelo Azure Stack, os usuários que se inscreveu para serviços do MySQL podem criar facilmente bancos de dados MySQL altamente disponíveis.

Este tutorial mostra como usar os itens do marketplace do Azure Stack para criar uma [MySQL com o cluster de replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Esta solução usa várias VMs para replicar os bancos de dados do nó mestre para um número configurável de réplicas. Depois de criado, o cluster pode, em seguida, ser adicionado como um servidor de hospedagem MySQL do Azure Stack e, em seguida, os usuários podem criar um banco de dados MySQL altamente disponível.

> [!IMPORTANT]
> O **MySQL com a replicação** item do marketplace do Azure Stack pode não estar disponível para todos os ambientes de assinatura de nuvem do Azure. Verifique se que o item do marketplace está disponível em sua assinatura antes de tentar seguir o restante nesse tutoral.

O que você aprenderá:

> [!div class="checklist"]
> * Criar um cluster de servidor MySQL de itens do marketplace
> * Criar um servidor de hospedagem MySQL do Azure Stack
> * Criar um banco de dados MySQL altamente disponível

Neste tutorial, um cluster de servidor MySQL de VM três será criado e configurado usando os itens disponíveis do marketplace do Azure Stack. 

Antes de iniciar as etapas neste tutorial, certifique-se de que o [provedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) foi instalado com êxito e que os itens a seguir estão disponíveis no marketplace do Azure Stack:

> [!IMPORTANT]
> Todos os itens a seguir são necessários para criar o cluster do MySQL.

- [MySQL com a replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Este é o modelo de solução de Bitnami que será usado para a implantação de cluster do MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/credativ.Debian8backports?tab=Overview). Debian 8 "Jessie" com o kernel de backports do Microsoft Azure fornecido pela empresa. O Debian GNU/Linux é uma das distribuições mais populares do Linux.
- [Script personalizado para linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Extensão de Script personalizado é uma ferramenta para executar sua VM personalização tarefas post VM provisionar. Quando essa extensão é adicionada a uma máquina Virtual, pode baixar os scripts do armazenamento do Azure e executá-los na VM. Tarefas de extensão de Script personalizadas também podem ser automatizadas usando os cmdlets de PowerShell do Azure e a Interface de linha de comando de plataforma cruzada do Azure (xPlat CLI).
- Acesso à VM para Linux extensão 1.4.7. A extensão VM Access permite que você redefina a senha, chave SSH ou as configurações de SSH, portanto, você pode recuperar o acesso à sua VM. Você também pode adicionar um novo usuário com senha ou chave SSH ou excluir um usuário usando essa extensão. Essa extensão destina-se as VMs do Linux.

Para saber mais sobre como adicionar itens para o Azure Stack marketplace, consulte o [visão geral do Azure Stack Marketplace](azure-stack-marketplace.md).

Você também precisará de um cliente SSH como [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) para fazer logon em VMs do Linux após elas serem implantadas.

## <a name="create-a-mysql-server-cluster"></a>Criar um cluster de servidor MySQL 
Use as etapas nesta seção para implantar o servidor MySQL cluster usando o [MySQL com a replicação](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) item do marketplace. Esse modelo implanta três instâncias do servidor MySQL configuradas em um cluster do MySQL altamente disponível. Por padrão, ele cria os seguintes recursos:

- Uma rede virtual
- Um grupo de segurança de rede
- Uma conta de armazenamento
- Um conjunto de disponibilidade
- Três interfaces (uma para cada uma das VMs padrão) de rede
- Um endereço IP público (para o cluster do MySQL primário VM)
- Três VMs do Linux para hospedar o cluster do MySQL

1. Entrar no portal de administração:
    - Para uma implantação de sistema integrado, o endereço do portal variará com base na região de sua solução e o nome de domínio externo. Ele será no formato https://adminportal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
    - Se você estiver usando o Azure Stack desenvolvimento ASDK (Kit), o endereço do portal está [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selecione **\+** **criar um recurso** > **computação**e então **MySQL com a replicação**.

   ![Implantação de modelo personalizado](media/azure-stack-tutorial-mysqlrp/createcluster1.png)

3. Fornecer informações de implantação básica sobre o **Noções básicas de** página. Examine os valores padrão e alterar conforme necessário e clique em **Okey**.<br><br>No mínimo, forneça o seguinte:
   - Nome da implantação (o padrão é o mysql)
   - Senha de raiz do aplicativo. Forneça uma senha alfanumérica de 12 caracteres com **sem caracteres especiais**
   - Nome do banco de dados de aplicativo (o padrão é bitnami)
   - Número de VMs para criar de réplica de banco de dados do MySQL (o padrão é 2)
   - Selecione a assinatura para usar
   - Selecione o grupo de recursos para usar ou criar um novo
   - Selecione o local (o padrão é o local para ASDK)

   ![Noções básicas da implantação](media/azure-stack-tutorial-mysqlrp/createcluster2.png)

4. Sobre o **configuração do ambiente** página, forneça as seguintes informações e, em seguida, clique em **Okey**: 
   - Chave pública SSH ou senha a ser usado para autenticação de SSH (secure shell). Se usar uma senha, ela deve conter letras, números e **pode** conter caracteres especiais
   - Tamanho da VM (o padrão é Standard D1 v2 VMs)
   - Tamanho em clique em GB do disco de dados **Okey**

   ![Configuração do ambiente](media/azure-stack-tutorial-mysqlrp/createcluster3.png)

5. Examine a implantação **resumo**. Opcionalmente, você pode baixar o modelo personalizado e os parâmetros e, em seguida, clique em **Okey**.

   ![Resumo](media/azure-stack-tutorial-mysqlrp/createcluster4.png)

6. Clique em **Create** sobre o **comprar** página para iniciar a implantação.

   ![Comprar](media/azure-stack-tutorial-mysqlrp/createcluster4.png)

    > [!NOTE]
    > A implantação levará cerca de uma hora. Certifique-se de que a implantação for concluída e o cluster do MySQL foi configurado completamente antes de continuar. 

7. Depois que todas as implantações foram concluídas com êxito, examine os itens do grupo de recursos e selecione o **mysqlip** item do endereço IP público. Registre o endereço IP público e o FQDN completo do IP público para o cluster.<br><br>Você precisará passar essa informação para um operador de pilha do Azure, para que possam criar um servidor de hospedagem MySQL aproveitando este cluster do MySQL.


### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede
Por padrão, nenhum acesso público é configurado para MySQL para o host VM. Para o provedor de recursos MySQL do Azure Stack para se conectar e gerenciar o cluster do MySQL, uma regra de NSG (grupo) de segurança de rede de entrada precisa ser criado.

1. No portal do administrador, navegue até o grupo de recursos criado quando implantar o cluster do MySQL e selecione o grupo de segurança de rede (**padrão de subrede de sg**):

   ![Abrir](media/azure-stack-tutorial-mysqlrp/nsg1.png)

2. Selecione **regras de segurança de entrada** e, em seguida, clique em **Add**.<br><br>Insira **3306** na **intervalo de porta de destino** e, opcionalmente, forneça uma descrição no **nome** e **descrição** campos. Clique em Adicionar para fechar a caixa de diálogo de regra de segurança de entrada.

   ![Abrir](media/azure-stack-tutorial-mysqlrp/nsg2.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Configurar o acesso externo ao cluster do MySQL
Antes do cluster do MySQL pode ser adicionado como um host do MySQL Server do Azure Stack, acesso externo deve ser habilitado.

1. Usando um cliente SSH, este exemplo usa [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), faça logon na máquina primária MySQL de um computador que possa acessar o IP público. O nome da VM de MySQL principal geralmente termina com **0** e tem um IP público atribuído a ele.<br><br>Use o IP público e o log na VM com o nome de usuário **bitnami** e a senha de aplicativo que você criou anteriormente sem caracteres especiais.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. Na janela de cliente SSH, use o seguinte comando para garantir que o serviço de bitnami está ativo e em execução. Forneça uma senha bitnami novamente quando solicitado:

   `sudo service bitnami status`

   ![Verifique o serviço](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Crie uma conta de usuário de acesso remoto a ser usado pelo servidor de hospedagem de MySQL do Azure Stack para se conectar ao MySQL e sair, em seguida, o cliente SSH.<br><br>Execute os seguintes comandos para fazer logon no MySQL como raiz, usando a senha raiz criada anteriormente e criar um novo usuário de administrador, substitua *\<nome de usuário\>* e *\<senha\>* conforme necessário para seu ambiente. Neste exemplo, o usuário a ser criado é chamado **sqlsa** e uma senha forte é usada:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Criar um usuário administrador](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Registre as novas informações de usuário do MySQL.<br><br>Você precisará fornecer esse nome de usuário e senha, junto com o endereço IP público ou o FQDN completo do IP público para o cluster, para um operador de pilha do Azure para que possam criar um servidor de hospedagem MySQL usando o cluster do MySQL.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Criar um servidor de hospedagem MySQL do Azure Stack
Depois que o cluster de servidor MySQL foi criado e configurado corretamente, um operador de pilha do Azure deve criar uma pilha de servidor MySQL do Azure de hospedagem para disponibilizar a capacidade adicional para que os usuários criem bancos de dados. 

Certifique-se de usar o IP público ou o FQDN completo para o IP público do cluster MySQL VM primária registrado anteriormente ao grupo de recursos do cluster do MySQL foi criado (**mysqlip**). Além disso, o operador será preciso saber que o servidor MySQL credenciais de autenticação que você criou para acessar remotamente o banco de dados de cluster do MySQL.

> [!NOTE]
> Esta etapa deve ser executada a partir do portal de administração do Azure Stack por um operador do Azure Stack.

Usando informações de logon de autenticação de IP público e o MySQL de MySQL do cluster, um operador do Azure Stack pode agora [criar um servidor de hospedagem MySQL usando o novo cluster do MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Também Certifique-se de que você criou os planos e ofertas para disponibilizar a criação de banco de dados MySQL para que os usuários. Um operador será necessário adicionar o **Microsoft.MySqlAdapter** para um plano de serviço e criar uma nova cota especificamente para bancos de dados altamente disponíveis. Para obter mais informações sobre a criação de planos, consulte [visão geral do plano, oferta, cotas e assinatura](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> O **Microsoft.MySqlAdapter** serviço não estará disponível para adicionar a planos até que o [provedor de recursos do servidor MySQL foi implantado](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Criar um banco de dados MySQL altamente disponível
Depois que o cluster do MySQL foi criado, configurado e adicionado como um servidor de hospedagem MySQL do Azure Stack por um operador de pilha do Azure, um usuário de locatário com uma assinatura, incluindo recursos de banco de dados do servidor MySQL pode criar bancos de dados MySQL altamente disponíveis por seguindo as etapas nesta seção. 

> [!NOTE]
> Execute estas etapas de portal do usuário do Azure Stack como um usuário de locatário com uma assinatura, fornecendo recursos de servidor MySQL (Microsoft.MySQLAdapter service).

1. Entrar no portal do usuário:
    - Para uma implantação de sistema integrado, o endereço do portal variará com base na região de sua solução e o nome de domínio externo. Ele será no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
    - Se você estiver usando o Azure Stack desenvolvimento ASDK (Kit), o endereço do portal de usuário está [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

2. Selecione **\+** **criar um recurso** > **dados \+ armazenamento**e então **banco de dados MySQL** .<br><br>Forneça as informações de propriedade de banco de dados necessário incluindo nome, o agrupamento, a assinatura a ser usada e o local a ser usado para a implantação. 

   ![Criar banco de dados MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Selecione **SKU** e, em seguida, escolha a SKU adequada MySQL de servidor de hospedagem para usar. Neste exemplo, o operador de pilha do Azure criou a **MySQL-HA** SKU para dar suporte a alta disponibilidade para bancos de dados do MySQL cluster.

   ![Selecione a SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Selecione **Login** > **criar um novo logon** e, em seguida, forneça as credenciais de autenticação de MySQL a ser usado para o novo banco de dados. Quando terminar, clique em **Okey** e, em seguida **criar** para iniciar o processo de implantação de banco de dados.

   ![Adicionar logon](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Quando a implantação de banco de dados MySQL é concluída com êxito, examine as propriedades de banco de dados para descobrir a cadeia de caracteres de conexão a ser usado para conectar-se ao novo banco de dados altamente disponível. 

   ![Exibir a cadeia de conexão](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um cluster de servidor MySQL de itens do marketplace
> * Criar um servidor de hospedagem MySQL do Azure Stack
> * Criar um banco de dados MySQL altamente disponível

Avance para o próximo tutorial para aprender como:
> [!div class="nextstepaction"]
> [Oferecer aplicativos web](/azure-stack-tutorial-app-service.md)
