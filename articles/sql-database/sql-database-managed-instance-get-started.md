---
title: 'Portal do Azure: Criar uma Instância Gerenciada do SQL | Microsoft Docs'
description: Crie uma Instância Gerenciada do SQL, o ambiente de rede e a VM do cliente para acesso.
keywords: tutorial de banco de dados sql, criar uma instância gerenciada do sql
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42023476"
---
# <a name="create-an-azure-sql-managed-instance"></a>Criar uma Instância Gerenciada do SQL Azure

Este início rápido mostra como criar uma Instância Gerenciada do SQL no Azure. Instância Gerenciada do Banco de Dados SQL do Azure é uma Instância de Mecanismo de Banco de Dados do SQL Server de Plataforma como um serviço (PaaS) que permite que você execute e dimensione bancos de dados altamente disponíveis do SQL Server na nuvem do Azure. Este guia rápido mostra como começar por meio da criação de uma Instância Gerenciada do SQL.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="prepare-network-environment"></a>Preparar o ambiente de rede

Instância Gerenciada do SQL é um serviço seguro que é colocado em sua própria Rede Virtual do Azure (VNet). Para criar uma Instância Gerenciada, você precisa preparar o ambiente de rede do Azure, que inclui:
 - VNet do Azure onde a Instância Gerenciada será colocada.
 - Sub-rede na sua VNet do Azure onde as Instâncias Gerenciadas serão colocadas.
 - Rota definida pelo usuário que permitirá à Instância Gerenciada se comunicar com os serviços do Azure que controlam e gerenciam a instância.

A sub-rede é dedicada para Instâncias Gerenciadas, e você não pode criar outros recursos (por exemplo, Máquinas Virtuais do Azure) nessa sub-rede. Neste início rápido você criará duas sub-redes na VNet do Azure para que você possa colocar Instâncias Gerenciadas na sub-rede dedicadas para Instâncias Gerenciadas e outros recursos na sub-rede padrão.

1. Implante o ambiente de rede do Azure preparado para a Instância Gerenciada do Banco de Dados SQL do Azure clicando no botão a seguir:

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    Esse botão abrirá um formulário no portal do Azure onde você pode configurar seu ambiente de rede antes de implantá-lo.

2. É possível alterar os nomes da VNet e das sub-redes e ajustar os intervalos de IP associados aos recursos de rede. Após pressionar o botão “Comprar”, esse formulário criará e configurará o ambiente:

    ![criar ambiente de instância gerenciada](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > Esta implantação do Azure Resource Manager criará duas sub-redes na VNet - uma para as Instâncias Gerenciadas chamada **ManagedInstances**, e outra chamada **Padrão** para outros recursos, como máquina virtual cliente que pode ser usada para se conectar à Instância Gerenciada. Se você não precisa de duas sub-redes, você pode excluir o padrão mais tarde; no entanto, nesse caso, você não conseguiria concluir a etapa 3 deste guia de início rápido - [preparar o computador cliente](#prepare-client-machine).

    > [!Note]
    > Se você alterar os nomes da VNet e das sub-redes, certifique-se de que você lembra dos novos nomes porque eles serão necessários nas seções a seguir. No restante do tutorial será assumido que você criou a VNet chamada **MyNewVNet**, a sub-rede **ManagedInstances** para Instâncias Gerenciadas do SQL e a sub-rede **Padrão** para Máquinas virtuais e outros recursos.

## <a name="create-a-managed-instance"></a>Criar uma Instância Gerenciada

As etapas a seguir mostram como criar sua Instância Gerenciada depois que a versão prévia é aprovada.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Instância Gerenciada** e selecione **Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia)**.
3. Clique em **Criar**.

   ![Criar Instância Gerenciada](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. Selecione sua assinatura e verifique se os termos da versão prévia mostram a opção **Aceito**.

   ![managed instance preview accepted](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. Preencha o formulário de Instância Gerenciada com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   |**Nome da Instância Gerenciada**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Logon de administrador da Instância Gerenciada**|Um nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não use “serveradmin”, que é uma função de nível de servidor reservada.| 
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 16 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupo de recursos**|O grupo de recursos criado anteriormente||
   |**Localidade**|O local selecionado anteriormente|Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Rede virtual**|A rede virtual que você criou anteriormente| Escolha o item **MyNewVNet/ManagedInstances** se você não alterou os nomes na etapa anterior. Caso contrário, escolha o nome da VNet e sub-rede da instância gerenciada que você inseriu na seção anterior. **Não use a sub-rede padrão porque ela não está configurada para Instâncias Gerenciadas do host**. |

   ![managed instance create form](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. Clique em **Tipo de preço** para dimensionar os recursos de computação e armazenamento e revisar as opções de tipo de preço. Por padrão, a instância possui 32 GB de espaço de armazenamento sem custo adicional, **que podem não ser suficientes para seus aplicativos**.
7. Use os controles deslizantes ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. 
   ![tipo de preço de instância gerenciada](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. Ao concluir, clique em **Aplicar** para salvar sua seleção.  
9. Clique em **Criar** para implantar a Instância Gerenciada.
10. Clique no ícone **Notificações** para exibir o status da implantação.
 
   ![deployment progress](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. Clique em **Implantação em andamento** para abrir a janela Instância Gerenciada e monitorar melhor o progresso da implantação.
 
   ![deployment progress 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Enquanto ocorre a implantação, continue no próximo procedimento.

> [!IMPORTANT]
> Para a primeira instância em uma sub-rede, o tempo de implantação é normalmente muito maior do que no caso de instâncias subsequentes; às vezes, mais de 24 horas até a conclusão. Não cancele a operação de implantação porque ela dura mais que o esperado. Esse período de tempo para implantar a primeira instância é uma situação temporária. Espere uma redução significativa do tempo de implantação logo após o início da versão prévia pública. Criar a segunda Instância Gerenciada na sub-rede levará alguns minutos.

## <a name="prepare-client-machine"></a>Preparar o computador cliente

Uma vez que a Instância Gerenciada do SQL é colocada na sua Rede Virtual privada, você precisa criar uma VM do Azure com uma ferramenta de cliente SQL instalada como o SQL Server Management Studio ou o SQL Operations Studio para se conectar à Instância Gerenciada e executar consultas.

> [!Note]
> Em vez da máquina virtual do Azure cliente, você pode configurar a conexão [Ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) e conectar-se à Instância Gerenciada a partir do computador local.

A maneira mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é usar os modelos do Azure Resource Manager.

1. Clique no botão a seguir (certifique-se de que você está conectado ao portal do Azure em outra guia do navegador):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. No formulário que será aberto, insira o nome da máquina virtual, o nome de usuário administrador e a senha que você usará para se conectar à VM.

    ![criar VM cliente](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    Se você não tiver alterado o nome da VNet e a sub-rede padrão, você não precisa alterar os dois últimos parâmetros, caso contrário, você deve alterar esses valores para os valores que você inseriu ao configurar o ambiente de rede.

3. Clique no botão “Comprar” e a VM do Azure será implantada na rede que você preparou.

4. Conecte-se à sua VM usando a conexão de Área de Trabalho Remota e encontre o SQL Server Management Studio ou o SQL Operation Studio que são instalados automaticamente na VM.

5. Abra o SSMS e digite o **nome do host** da sua Instância Gerenciada na caixa **Nome do servidor**, selecione **Autenticação do SQL Server**, forneça o logon e a senha na caixa de diálogo **Conectar-se ao servidor** e, em seguida, clique em **Conectar**.

    ![conexão ssms](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

Depois de se conectar, você pode exibir seus bancos de dados do sistema e do usuário no nó Bancos de Dados e vários objetos nos nós Segurança, Objetos do Servidor, Replicação, Gerenciamento, SQL Server Agent e XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

 - [Conectar seus aplicativos à Instância Gerenciada](sql-database-managed-instance-connect-app.md).
 - [Migrar seus bancos de dados do local para a Instância Gerenciada](sql-database-managed-instance-migrate.md).


