---
title: Servidores na pilha do Azure de hospedagem MySQL | Microsoft Docs
description: Como adicionar instâncias do MySQL para o provisionamento por meio do provedor de recursos de adaptador do MySQL
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938545"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos MySQL

Você pode hospedar uma instância do MySQL em uma máquina virtual (VM) em [Azure pilha](azure-stack-poc.md), ou em uma máquina virtual fora de seu ambiente de pilha do Azure, desde que o provedor de recursos do MySQL pode se conectar à instância.

## <a name="connect-to-a-mysql-hosting-server"></a>Conecte-se ao servidor de hospedagem MySQL

Verifique se que você tem as credenciais para uma conta com privilégios de administrador do sistema. Para adicionar um servidor de hospedagem, siga estas etapas:

1. Entrar no portal de operador de pilha do Azure como um administrador de serviço.
2. Escolha **Mais serviços**.
3. Selecione **recursos ADMINISTRATIVOS** > **servidores de hospedagem MySQL** > **+ adicionar**. Isso abre o **adicionar um servidor de hospedagem MySQL** caixa de diálogo, mostrada na captura de tela a seguir.

   ![Configurar um servidor de hospedagem](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Forneça os detalhes de conexão da sua instância do MySQL Server.

   * Para **nome do servidor de hospedagem MySQL**, forneça o nome de domínio totalmente qualificado (FQDN) ou um endereço IPv4 válido. Não use o nome curto de VM.
   * Uma instância do MySQL padrão não for fornecida, portanto, você deve especificar o **tamanho do servidor host GB**. Insira um tamanho próximo a capacidade do servidor de banco de dados.
   * Mantenha a configuração padrão para **assinatura**.
   * Para **grupo de recursos**, crie um novo ou use um grupo existente.

   > [!NOTE]
   > Se a instância do MySQL pode ser acessada por locatário e o administrador do Azure Resource Manager, você pode colocá-lo sob o controle do provedor de recursos. Mas, a instância do MySQL **deve** ser atribuídos exclusivamente para o provedor de recursos.

5. Selecione **SKUs** para abrir o **criar SKU** caixa de diálogo.

   ![Criar uma SKU do MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   O SKU **nome** deve refletir as propriedades do SKU para que os usuários podem implantar seus bancos de dados para a SKU apropriada.

   >[!IMPORTANT]
   >Caracteres especiais, incluindo espaços e pontos, não têm suporte no **nome** ou **camada** quando você cria uma SKU para o provedor de recursos do MySQL.

6. Selecione **Okey** para criar o SKU.
7. Em **adicionar um servidor de hospedagem MySQL**, selecione **criar**.

Como adicionar servidores, você deve atribuí-los a uma SKU de nova ou existente para diferenciar as ofertas de serviço. Por exemplo, você pode ter uma instância do MySQL empresarial que oferece maior banco de dados e backups automáticos. Você pode reservar este servidor de alto desempenho para diferentes departamentos na sua organização.

## <a name="increase-backend-database-capacity"></a>Aumentar a capacidade do banco de dados de back-end

Você pode aumentar a capacidade do banco de dados de back-end ao implantar mais servidores de MySQL no portal do Azure pilha. Adicione esses servidores para um SKU de novo ou existente. Se você adicionar um servidor a um SKU existente, certifique-se de que as características de servidor são os mesmos que os outros servidores na SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Disponibilizar os servidores de banco de dados MySQL para seus usuários

Crie planos e ofertas para disponibilizar os servidores de banco de dados MySQL para os usuários. Adicione o serviço de Microsoft.MySqlAdapter para o plano e, em seguida, adicionar a cota padrão ou criar uma nova cota.

![Criar planos e ofertas para bancos de dados](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Próximas etapas

[Criar um banco de dados MySQL](azure-stack-mysql-resource-provider-databases.md)