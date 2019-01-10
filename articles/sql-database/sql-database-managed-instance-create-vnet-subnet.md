---
title: Instância Gerenciada do Banco de Dados SQL do Azure, Criar VNet | Microsoft Docs
description: Este tópico descreve como criar uma VNet (rede virtual) em que você pode implantar uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: a8000fb26ce5496a9c62ba475b862f8f80adf6b7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041757"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Configurar VNet para Instância Gerenciada do Banco de Dados SQL do Azure

Este tópico explica como criar uma VNet válida e uma sub-rede em que você possa implantar Instâncias Gerenciadas do Banco de Dados SQL do Azure.

A Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Essa implantação permite os cenários a seguir:

- Proteger o endereço IP privado.
- Conectar uma Instância Gerenciada diretamente de uma rede local
- Conectar uma Instância Gerenciada ao servidor vinculado ou a outro armazenamento de dados local
- Conectar uma Instância Gerenciada a recursos do Azure  

  > [!Note]
  > Você deve [determinar o tamanho da sub-rede para a Instância Gerenciada](sql-database-managed-instance-determine-size-vnet-subnet.md) antes de implantar a primeira instância porque o sunet não pode ser redimensionado depois que você colocar recursos dentro dele.
  > Se você planeja utilizar uma rede virtual existente, será necessário modificar essa configuração de rede para acomodar sua Instância Gerenciada. Para obter mais informações, consulte [Modificar a rede virtual existente para Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-new-virtual-network"></a>Criar uma rede virtual

A maneira mais fácil de criar e configurar a rede virtual é usar o modelo de implantação do Azure Resource Manager.

1. Entre no Portal do Azure.

2. Use o botão **Implantar no Azure** para implantar a rede virtual na nuvem do Azure:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Este botão abrirá um formulário que poderá ser utilizado para configurar o ambiente de rede onde será possível implantar a Instância Gerenciada.

   > [!Note]
   > Esse modelo do Azure Resource Manager implantará a rede virtual com duas sub-redes. Uma sub-rede chamada **ManagedInstances** é reservada para Instâncias Gerenciadas e tem uma tabela de roteamento pré-configurada, enquanto a outra sub-rede chamada **Padrão** é usada para outros recursos que devem acessar a Instância Gerenciada (por exemplo, Máquinas Virtuais do Microsoft Azure). É possível remover a sub-rede **Padrão** se ela não for necessária.

3. Configure o ambiente de rede. É possível configurar os parâmetros do ambiente de rede, conforme a seguir:

![Configurar rede do Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

É possível alterar os nomes da VNet e sub-redes e ajustar os intervalos de IP associados aos recursos de rede. Após pressionar o botão "Comprar", esse formulário criará e configurará o ambiente. Se você não precisar de duas sub-redes, poderá excluir uma padrão.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar a rede virtual existente para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md)
- Para um tutorial que mostra como criar uma VNet, criar uma Instância Gerenciada e restaurar um banco de dados a partir de um backup de banco de dados, consulte [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md)
