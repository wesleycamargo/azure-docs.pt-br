---
title: DNS personalizado da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258160"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Uma Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia) deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Há alguns cenários, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrida, que exigem nomes de hosts privados para serem resolvidos da Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. Uma vez que a Instância Gerenciada usa o mesmo DNS para seu funcionamento interno, a configuração de DNS da rede virtual deverá ser compatível com a Instância Gerenciada. 

Para tornar uma configuração personalizada do DNS compatível com a Instância Gerenciada, é necessário concluir as seguintes etapas: 
- Configurar o DNS personalizado para encaminhar solicitações para o DNS do Azure 
- Configurar o DNS personalizado como primário e do Azure como secundário para a rede virtual 
- Registrar o DNS personalizado como primário e do Azure como secundário

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Configurar o DNS personalizado para encaminhar solicitações para o DNS do Azure 

Para configurar o encaminhamento de DNS no Windows Server 2016, siga estas etapas: 

1. No **Gerenciador do Servidor**, clique em **Ferramentas** e, em seguida, clique em **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Clique duas vezes em **Encaminhadores**.

   ![Encaminhadores](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Clique em **Editar**. 

   ![Forwarders-list](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Insira endereço IP de resolvedores recursivos do Azure, como 168.63.129.16.

   ![Endereço de IP de resolvedores recursivos](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Configurar o DNS personalizado como primário e do Azure como secundário 
 
Configuração de DNS em uma rede virtual do Azure requer que você insira os endereços IP, para configurar a VM do Azure que hospeda o servidor DNS com um endereço IP estático usando as próximas etapas a seguir: 

1. No portal do Azure, abra a interface de rede de VM do DNS personalizado.

   ![network-interface](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. Na seção Configurações de IP. selecione configuração de IP 

   ![configuração de IP](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Defina o endereço IP privado como estático. Anote o endereço IP (10.0.1.5 na captura de tela) 

   ![estático](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Registrar o DNS personalizado como primário e do Azure como secundário 

1. No portal do Azure, encontre opção de DNS personalizado para sua rede virtual.

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Alterne para personalizado e insira o endereço IP do servidor DNS personalizado, bem como endereço IP de resolvedores recursivos do Azure, como 168.63.129.16. 

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Não configurar o resolvedor recursivo do Azure na lista do DNS faz com que a Instância Gerenciada entre em estado com falha. A recuperação desse estado pode exigir a criação de uma nova instância em uma VNet com as políticas de rede compatíveis, recriar dados em nível de instância e restaurar os bancos de dados. Consulte [Configuração de VNET](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criando uma Instância Gerenciada](sql-database-managed-instance-create-tutorial-portal.md).
- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de Instâncias Gerenciadas](sql-database-managed-instance-vnet-configuration.md)
