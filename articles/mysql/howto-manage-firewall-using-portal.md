---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure | Microsoft Docs
description: Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 744f3503d2abc618095351e1b018cab22adcf091
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-the-azure-portal"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para MySQL usando o Portal do Azure
As regras de firewall no nível de servidor permitem que os administradores acessem um servidor de Banco de Dados SQL do Azure para MySQL de um endereço IP específico ou intervalo de endereços IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

1. Na folha do servidor MySQL, no título Configurações, clique em **Segurança de Conexão** para abrir a folha Segurança de Conexão para o Banco de Dados do Azure para MySQL.
![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Clique em **Adicionar Meu IP** na barra de ferramentas. Isso criará automaticamente uma regra com o endereço IP de seu computador, como visto pelo sistema do Azure.
![Portal do Azure - clique em Adicionar Meu IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verifique seu endereço IP antes de salvar a configuração. Em algumas situações, o endereço IP observado pelo Portal do Azure é diferente do endereço IP usado ao acessar a Internet e os servidores do Azure. Portanto, talvez seja necessário alterar o IP inicial e o IP final para fazer a regra funcionar conforme o esperado.

Use um mecanismo de pesquisa ou outra ferramenta online para verificar seu próprio endereço IP (por exemplo, pesquise "Qual é meu endereço IP").
![Bing para Qual é meu IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall no nível de servidor. Aguarde a confirmação de que a atualização das regras de firewall foi bem-sucedida.
![Portal do Azure - clique em Salvar](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

> [!NOTE]
> Nas regras do firewall de Banco de Dados do Azure para MySQL, você pode especificar um único endereço IP ou um intervalo de endereços. Se você quiser limitar a regra para um único endereço IP, digite o mesmo endereço nos campos IP inicial e IP Final. Abrir o firewall permite que os administradores e os usuários façam logon em qualquer banco de dados no servidor MySQL ao qual eles têm credenciais válidas.
![Portal do Azure - regras de firewall](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerenciar regras de firewall existentes no nível de servidor pelo Portal do Azure
Repita as etapas para gerenciar as regras de firewall.
* Para adicionar o computador atual, clique em **+ Adicionar Meu IP**.
* Para adicionar mais endereços IP, digite o **NOME DA REGRA**, o **IP INICIAL** e o **IP FINAL**.
* Para modificar uma regra existente, clique em qualquer um dos campos na regra e modifique.
* Para excluir uma regra existente, clique nas reticências [...] e clique em **Excluir**.
* Clique em **Salvar** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas
- Para obter ajuda com a conexão com um Banco de Dados para servidor MySQL, veja [Bibliotecas de conexão para o Banco de Dados para MySQL](./concepts-connection-libraries.md)
