---
title: Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure | Microsoft Docs
description: "Este artigo descreve como você pode criar rapidamente um novo servidor de Banco de Dados do Azure para MySQL e gerenciar o servidor usando o Portal do Azure."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure
Este artigo descreve como você pode criar rapidamente um novo servidor de Banco de Dados do Azure para MySQL e gerenciar o servidor usando o Portal do Azure. O gerenciamento de servidor inclui a exibição de detalhes do servidor e bancos de dados, redefinição de senha e exclusão do servidor.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL
Execute estas etapas para criar um servidor de Banco de Dados do Azure para MySQL chamado "mysqlserver4demo"

1- Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2- Selecione **Bancos de Dados** na página Novo e selecione **Banco de Dados do Azure para MySQL** na página Bancos de Dados.

> Um Banco de Dados do Azure para MySQL é criado com um conjunto definido de recursos de [computação e armazenamento](./concepts-compute-unit-and-storage.md). O banco de dados é criado dentro de um grupo de recursos do Azure e em um servidor de Banco de Dados do Azure para MySQL.

![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3- Preencha o formulário do Banco de Dados do Azure para MySQL com as seguintes informações:

| **Campo de Formulário** | **Descrição do Campo** |
|----------------|-----------------------|
| *Nome do servidor* | azure-mysql (o nome do servidor é globalmente exclusivo) |
| *Assinatura* | MySQLaaS (selecione na lista suspensa) |
| *Grupo de recursos* | myresource (crie um novo grupo de recursos ou use um existente) |
| *Logon de administrador do servidor* | myadmin (nome da conta do administrador de configuração) |
| *Senha* | configurar senha da conta do administrador |
| *Confirmar senha* | confirmar senha da conta do administrador |
| *Localidade* | Europa Setentrional (escolha entre o Europa Setentrional e Oeste dos EUA) |
| *Versão* | 5.6 (escolha uma versão do servidor de Banco de Dados do Azure para MySQL) |

4- Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo servidor. Unidade de Computação pode ser configurada entre 50 e 100 no Tipo básico, 100 e 200 no Tipo Standard, e o armazenamento pode ser adicionado com base na quantidade incluída. Para este guia de instruções, vamos escolher 50 Unidades de Computação e 50 GB. Clique em **OK** para salvar sua seleção.
![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- Clique em **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.

> Marque a opção **Fixar no painel** para permitir o controle fácil de suas implantações.
> [!NOTE]
> Embora exista suporte para até 1000 GB na camada Basic e 10000 GB na camada Standard para armazenamento, para Visualização Pública, o armazenamento máximo ainda está limitado temporariamente a 1000 GB. 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar um servidor de Banco de Dados do Azure para MySQL
Após o provisionamento do novo servidor, o usuário terá duas opções para editar um servidor existente: redefinir a senha de administrador ou escalar verticalmente o servidor alterando as unidades de computação.

### <a name="change-the-administrator-user-password"></a>Alterar a senha de usuário do administrador
1- Na folha **Visão geral** do servidor, clique em **Redefinir senha** para preencher uma janela de entrada e confirmação de senha.

2- Insira a nova senha e confirme a senha na janela, conforme mostrado a seguir: ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3- Clique em **OK** para salvar a nova senha.

### <a name="scale-updown-by-changing-compute-units"></a>Escalar verticalmente alterando as Unidades de Computação

1 - na folha do servidor, em **Configurações**, clique em **Tipo de preço** para abrir a folha Tipo de preço para o servidor de Banco de Dados do Azure para MySQL.

2- Execute a Etapa 4 de **Criar um servidor de Banco de Dados para MySQL** para alterar as Unidades de Computação no mesmo Tipo de preço.

## <a name="delete-an-azure-database-for-mysql-server"></a>Excluir um servidor de Banco de Dados do Azure para MySQL

1 - Na folha **Visão geral** do servidor, clique no botão de comando **Excluir** para abrir a folha de Confirmação de exclusão.

2- Digite o nome correto do servidor na caixa de entrada da folha para confirmar duas vezes.

3- Clique no botão **Excluir** novamente para confirmar a ação de exclusão e aguarde o pop-up "Exclusão bem-sucedida" na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Listar os bancos de dados do Banco de Dados do Azure para MySQL
Na folha **Visão geral** do servidor, role para baixo até ver o bloco do banco de dados na parte inferior. Todos os bancos de dados estarão listados na tabela. clique no botão de comando **Excluir** para abrir a folha de Confirmação de exclusão.

![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de um servidor de Banco de Dados do Azure para MySQL
Clique em **Propriedades** em **Configurações** na folha do servidor para abrir a folha **Propriedades**. Veja todas as informações detalhadas sobre o servidor.

## <a name="next-steps"></a>Próximas etapas

[Início rápido: criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)

