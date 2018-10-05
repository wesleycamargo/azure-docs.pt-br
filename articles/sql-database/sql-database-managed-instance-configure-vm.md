---
title: Conectar-se à VM cliente – Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Conecte-se a uma Instância Gerenciada do Banco de Dados SQL do Azure usando o SQL Server Management Studio de uma máquina virtual do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159509"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Configurar a VM do Azure para se conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure

Este início rápido demonstra como configurar uma máquina virtual do Azure para se conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure usando o SSMS (SQL Server Management Studio). Para ver um início rápido que mostra como se conectar de um computador cliente local usando uma conexão ponto a site, confira [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md) 

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido usa como ponto de partida os recursos criados neste início rápido: [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Criar uma nova sub-rede na VNet de Instância Gerenciada

As seguintes etapas criam uma nova sub-rede na VNet da Instância Gerenciada para uma máquina virtual do Azure conectar-se à Instância Gerenciada. A sub-rede da Instância Gerenciada é dedicada para Instâncias Gerenciadas, e você não pode criar outros recursos (por exemplo, Máquinas Virtuais do Azure) nessa sub-rede. 

1. Abra o grupo de recursos para a Instância Gerenciada que você criou no início rápido [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md) e clique na rede virtual para sua Instância Gerenciada e, em seguida, clique em **Sub-redes**.

   ![Recursos da Instância Gerenciada](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Clique no sinal **+** ao lado de **Sub-rede** para criar uma nova sub-rede.

   ![Sub-redes de Instância Gerenciada](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Preencha o formulário com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- | 
   | **Nome** | Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Intervalo de endereços (bloco CIDR)** | Um intervalo válido | O valor padrão é bom para este início rápido.|
   | **Grupo de segurança de rede** | Nenhum | O valor padrão é bom para este início rápido.|
   | **Tabela de rotas** | Nenhum | O valor padrão é bom para este início rápido.|
   | **Pontos de extremidade de serviço ** | 0 selecionado | O valor padrão é bom para este início rápido.|
   | **Delegação de sub-rede** | Nenhum | O valor padrão é bom para este início rápido.|
 
   ![Nova sub-rede de Instância Gerenciada para a VM do cliente](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Clique em **OK** para criar essa sub-rede adicional na VNet de Instância Gerenciada.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Criar uma máquina virtual na nova sub-rede na rede virtual

As etapas a seguir mostram como criar uma máquina virtual na nova sub-rede para conexão à Instância Gerenciada. 

## <a name="prepare-the-azure-virtual-machine"></a>Preparar a máquina virtual do Azure

Uma vez que a Instância Gerenciada do SQL é colocada na sua Rede Virtual privada, você precisa criar uma VM do Azure com uma ferramenta de cliente SQL instalada como o SQL Server Management Studio ou o Azure Data Studio para se conectar à Instância Gerenciada e executar consultas. Este início rápido usa o SQL Server Management Studio.

A maneira mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é usar os modelos do Azure Resource Manager.

1. Clique no botão abaixo para criar uma máquina virtual de cliente e instalar o SQL Server Management Studio (verifique se você entrou no portal do Azure em outra guia do navegador):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Preencha o formulário com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ---------------- | ----------------- | ----------- |
   | **Assinatura** | Uma assinatura válida | Deve ser uma assinatura na qual você tem permissão para criar novos recursos |
   | **Grupo de recursos** |O grupo de recursos que você especificou no início rápido [Criar Instância Gerenciada](sql-database-managed-instance-get-started.md).|Deve ser o grupo de recursos no qual existe a VNet.|
   | **Localidade** | O local para o grupo de recursos | Esse valor é preenchido com base no grupo de recursos selecionado | 
   | **Nome da máquina virtual**  | Qualquer nome válido | Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Nome de Usuário do Administrador**|Um nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não use “serveradmin”, que é uma função de nível de servidor reservada.| 
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   | **Tamanho da Máquina Virtual** | Qualquer tamanho válido | O padrão neste modelo de **Standard_B2s é suficiente para este início rápido. |
   | **Localidade**|[resourceGroup().location].| Não altere esse valor |
   | **Nome da Rede Virtual**|O local selecionado anteriormente|Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   | **Nome da sub-rede**|O nome da sub-rede que você criou no procedimento anterior| Não escolha a sub-rede na qual você criou a Instância Gerenciada|
   | **Localização de artefatos** | [deployment().properties.templateLink.uri] Não altere esse valor |
   | **token SAS de Localização de artefatos** | deixar em branco | Não altere esse valor |

   ![criar VM cliente](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Se você tiver usado o nome sugerido da VNet e a sub-rede padrão em [Como criar sua Instância Gerenciada](sql-database-managed-instance-get-started.md), não precisará alterar dois últimos parâmetros. Caso contrário, você deverá alterar esses valores para os valores inseridos quando você configurou o ambiente de rede.

3. Marque a caixa de seleção **Concordo com os termos e condições declarados acima**.
4. Clique em **Comprar** para implantar a VM do Azure em sua rede.
5. Clique no ícone **Notificações** para exibir o status da implantação.
   
   Não continue até que a máquina virtual do Azure tenha sido criada. 

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

As etapas a seguir mostram como se conectar à sua máquina virtual recém-criada usando uma conexão de área de trabalho remota.

1. Após a conclusão da implantação, vá para o recurso da máquina virtual.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Clique em **Conectar**. 
   
   Um formulário de Protocolo RDP (arquivo .rdp) é exibido com o IP endereço IP público e o número da porta para a máquina virtual. 

   ![Formulário do RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Clique em **Baixar arquivo RDP**.
 
   > [!NOTE]
   > Você também pode usa SSH para se conectar à VM.

4. Feche o formulário **Conectar-se à máquina virtual**.
5. Para se conectar à sua VM, abra o arquivo RDP baixado. 
6. Se solicitado, clique em **Conectar**. Em um Mac, você precisa de um cliente RDP, como este [Cliente de Área de Trabalho Remota](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) da Mac App Store.

6. Insira o nome de usuário e a senha especificados na criação da máquina virtual e clique em **Ok**.

7. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

Você está conectado à máquina virtual no painel do Gerenciador do Servidor.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Usar o SSMS para se conectar à Instância Gerenciada

1. Na máquina virtual, abra o SSMS (SQL Server Management Studio).
 
   Ele levará alguns instantes para abrir, já que precisa concluir sua configuração, pois essa é a primeira vez em que SSMS está sendo iniciado.
2. Na caixa de diálogo **Conectar ao Servidor**, insira o **nome do host** totalmente qualificado da Instância Gerenciada na caixa **Nome do servidor**, selecione **Autenticação do SQL Server**, forneça o logon e a senha e, em seguida, clique em **Conectar**.

    ![conexão ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Depois de se conectar, você pode exibir seus bancos de dados do sistema e do usuário no nó Bancos de Dados e vários objetos nos nós Segurança, Objetos do Servidor, Replicação, Gerenciamento, SQL Server Agent e XEvent Profiler.

## <a name="next-steps"></a>Próximas etapas

- Para ver um início rápido que mostra como se conectar de um computador cliente local usando uma conexão ponto a site, confira [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada](sql-database-managed-instance-connect-app.md).
- Para restaurar um banco de dados do SQL Server existente do local para uma Instância Gerenciada, use o [DMS (Serviço de Migração de Banco de Dados do Azure) para realizar a migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar de um arquivo de backup do banco de dados ou o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup do banco de dados.
