---
title: Bancos de dados fornecida pelo adaptador SQL RP na pilha do Azure | Microsoft Docs
description: Como criar e gerenciar bancos de dados SQL provisionados usando o provedor de recursos do adaptador de SQL
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2808847642639069e60102b195ac97957c8593f0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="create-sql-databases"></a>Criar bancos de dados SQL
Autoatendimento bancos de dados são fornecidos através da experiência do portal de usuário. Um usuário precisa de uma assinatura que tenha uma oferta, que contém o serviço de banco de dados.

1. Entrar para o [Azure pilha](azure-stack-poc.md) portal do usuário (administradores de serviço também podem usar o portal de administração).

2. Clique em **+ novo** &gt; **dados + armazenamento "** &gt; **o banco de dados do SQL Server** &gt; **adicionar**.

3. Preencha o formulário com os detalhes do banco de dados, incluindo um **nome do banco de dados**, **tamanho máximo**e altere os outros parâmetros conforme necessário. Você será solicitado a escolher uma SKU para seu banco de dados. Como servidores de hospedagem são adicionados, eles serão atribuídos a uma SKU. Bancos de dados são criados nesse pool de servidores que compõem o SKU de hospedagem.

  ![Novo banco de dados](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > O tamanho do banco de dados deve ser pelo menos de 64 MB. Ele pode ser aumentado usando as configurações.

4. Preencha as configurações de logon: **logon de banco de dados**, e **senha**. Essas configurações são as credenciais de autenticação do SQL que é criada para o acesso a somente esse banco de dados. O nome de usuário de logon deve ser globalmente exclusivo. Crie uma nova configuração de logon ou selecione um existente. Você pode reutilizar as configurações de logon para outros bancos de dados usando o mesmo SKU.

    ![Criar um novo logon de banco de dados](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Enviar o formulário e aguarde até que a implantação fosse concluída.

    Na folha de resultante, observe o campo "Cadeia de caracteres de Conexão". Você pode usar essa cadeia de caracteres em qualquer aplicativo que requer acesso ao SQL Server (por exemplo, um aplicativo web) na pilha do Azure.

    ![Recuperar a cadeia de caracteres de conexão](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Excluir bancos de dados SQL AlwaysOn
Quando um banco de dados AlwaysOn do SQL é excluído do provedor de recursos, é excluída com êxito do primário e o grupo de disponibilidade do AlwaysOn, mas por Design, SQL AG coloca o banco de dados na restauração de estado em cada réplica e não descarta o banco de dados, a menos que o disparou. Se um banco de dados não for descartado, as réplicas secundárias vai para estado não sincronizando. Adicionar um novo banco de dados novamente para o grupo de disponibilidade com o mesmo por meio de RP ainda funciona.

## <a name="verify-sql-alwayson-databases"></a>Verifique se os bancos de dados SQL AlwaysOn
Bancos de dados AlwaysOn devem mostrar que sincronizadas e estão disponíveis em todas as instâncias e no grupo de disponibilidade. Após o failover, o banco de dados deve se conectar diretamente. Você pode usar o SQL Server Management Studio para verificar a sincronização de um banco de dados:

![Verifique se o AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Próximas etapas

[Manter o provedor de recursos do SQL Server](azure-stack-sql-resource-provider-maintain.md)
