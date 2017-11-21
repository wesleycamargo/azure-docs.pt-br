---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure | Microsoft Docs
description: Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 96e917d1ea147e3b53b00002675ed16facb69255
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure
As regras de firewall no nível de servidor permitem que os administradores acessem um servidor de Banco de Dados SQL do Azure para PostgreSQL de um endereço IP específico ou intervalo de endereços IP. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um servidor [Criar um servidor de Banco de Dados do Azure para o PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure
1. Na página do servidor PostgreSQL, no título Configurações, clique em **Segurança de Conexão** para abrir a página Segurança de Conexão para o Banco de Dados do Azure para PostgreSQL.

  ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar Meu IP** na barra de ferramentas. Essa ação cria automaticamente uma regra de firewall com o endereço IP público do seu computador, como visto pelo sistema do Azure.

  ![Portal do Azure - clique em Adicionar Meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.
Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP (por exemplo, pesquise no Bing "Qual é meu IP").

  ![Pesquisa do Bing para Qual é meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Adicionar outros intervalos de endereço. Nas regras de firewall do Banco de Dados do Azure para PostgreSQL, é possível especificar um único endereço IP ou um intervalo de endereços. Se você desejar limitar a regra a um único endereço IP, digite o mesmo endereço no campo IP inicial e IP final. Abrir o firewall permite que administradores, usuários e aplicativos façam logon em qualquer banco de dados no servidor PostgreSQL para o qual eles têm credenciais válidas.

  ![Portal do Azure - regras de firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.

  ![Portal do Azure - clique em Salvar](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique no botão + **Adicionar Meu IP**. Clique em **Salvar** para salvar as alterações.
* Para adicionar mais endereços IP, digite o Nome da Regra, o Endereço IP Inicial e o Endereço IP Final. Clique em **Salvar** para salvar as alterações.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique. Clique em **Salvar** para salvar as alterações.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **Excluir** para remover a regra. Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível gerar um script para [Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-firewall-using-cli.md).
- Para obter ajuda com a conexão com um servidor do Banco de Dados do Azure para PostgreSQL, consulte [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
