---
title: "Início rápido: criar servidor do Banco de Dados do Azure para MySQL – Portal do Azure | Microsoft Docs"
description: Este artigo o orienta a usar o portal do Azure para criar rapidamente um servidor de Banco de Dados do Azure para MySQL de exemplo em aproximadamente cinco minutos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 3e0aa1ecccdb55b75dd332818e09b21431716fdc
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure
Este artigo o orienta a usar o portal do Azure para criar um servidor de Banco de Dados do Azure para MySQL em aproximadamente cinco minutos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Primeiro, abra seu navegador da Web e navegue até o [portal do Microsoft Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL
1. Clique no botão **Novo** no canto superior esquerdo do portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para MySQL** na página **Bancos de Dados**. Você também pode digitar **MySQL** na caixa de pesquisa Nova página para localizar o serviço.
![Portal do Azure – novo – banco de dados – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Preencha o formulário de detalhes do novo servidor com as informações abaixo, conforme mostrado na imagem anterior:

| **Configuração** | **Valor sugerido** | **Descrição do Campo** |
|---|---|---|
| *Nome do servidor* | myserver4demo  | O nome do servidor precisa ser exclusivo globalmente. |
| *Assinatura* | mysubscription | Selecione sua assinatura na lista suspensa. |
| *Grupo de recursos* | myresourcegroup | Crie um Grupo de recursos ou use um grupo existente. |
| *Logon de administrador do servidor* | myadmin | Especifique um nome de conta para ser administrador no mecanismo do MySQL. |
| *Senha* |  | Defina uma senha de conta de administrador de alta segurança. |
| *Confirmar senha* |  | Confirme a senha da conta do administrador. |
| *Localidade* |  | Selecione uma região disponível. |
| *Versão* | 5.7 | Escolha a versão mais recente. |
| *Tipo de preço* | Básico, 50 unidades de computação, 50 GB de armazenamento  | Escolha **Tipo de preço**, **Unidades de Computação**, **Armazenamento (GB)**e clique em **OK**. |
| *Fixar no painel* | Verificação | Recomenda-se marcar essa caixa de seleção para poder encontrar o servidor facilmente no futuro |

   Clique em **Tipo de Preço** para especificar o nível de desempenho e o tipo de preço do novo banco de dados. Para esse início rápido, selecione a camada Básica, 50 Unidades de Computação e 50 GB de armazenamento incluído. Em seguida, clique em **OK** para salvar o tipo de preço.
   
   ![Portal do Azure – crie MySQL, fornecendo a entrada de formulário necessária](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   Em seguida, clique em **Criar**. Em um ou dois minutos, um novo Banco de Dados para servidor MySQL estará em execução na nuvem. Clique no botão **Notificações** (ícone de sino) para monitorar o processo de implantação.

## <a name="configure-the-firewall"></a>Configurar o firewall
Antes de conectar-se pela primeira vez ao Banco de Dados do Azure para MySQL, configure o firewall e adicione o endereço IP da rede pública do cliente (ou intervalo) à lista de permissões.

1. Após a implantação ser concluída, clique em **Todos os Recursos** no menu esquerdo e digite o nome **myserver4demo**, para pesquisar o servidor recém-criado. Clique no nome do servidor listado nos resultados da pesquisa. A página Visão geral do servidor é aberta e oferece outras opções de configuração.

2. Na folha do servidor, selecione **Segurança de Conexão**.

3. Clique em **Adicionar Meu IP** para adicionar o endereço IP do computador local ou para configurar um intervalo de endereços IP. Lembre-se de clicar em **Salvar** depois de criar as regras.
  ![Portal do Azure – Adicionar regra de firewall e salvar](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha o nome de domínio totalmente qualificado para o servidor do Azure MySQL no portal do Azure. Use o nome de domínio totalmente qualificado para se conectar ao servidor usando a ferramenta de linha de comando **mysql.exe**.

1.  No [portal do Azure](https://portal.azure.com/), clique em **Todos os recursos** no menu à esquerda e clique no servidor de Banco de Dados do Azure para MySQL.

2.  Clique em **Propriedades**. Anote **NOME DO SERVIDOR** e **LOGON DE ADMINISTRADOR DO SERVIDOR**.
Neste exemplo, o nome do servidor é *myserver4demo.mysql.database.azure.com* e o logon de administrador do servidor é  *myadmin@myserver4demo* .

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando mysqlexe
Use a [ferramenta de linha de comando do mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para estabelecer uma conexão com seu Banco de Dados do Azure para servidor MySQL. Você pode executar a ferramenta de linha de comando do mysql no navegador usando o Azure Cloud Shell ou iniciá-la no seu próprio computador usando as ferramentas do mysql instaladas localmente. Para iniciar o Azure Cloud Shell, clique no botão `Try It` em um bloco de código neste artigo ou visite o [portal do Azure](https://portal.azure.com) e clique no ícone `>_` na barra de ferramentas superior direita. 

1. Digite o comando para se conectar:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Exibir o status do servidor para garantir que a conexão está funcionando. Digite `status` no prompt do mysql > depois de conectado.
```sql
status
```

   ![Prompt de comando – exemplo de linha de comando mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

3. Crie um banco de dados vazio digitando o comando `CREATE DATABASE` no prompt do mysql >.

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Dentro de um banco de dados do Azure para o servidor MySQL, você pode ter um ou mais bancos de dados. Você pode optar por criar um banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Não há nenhum limite para o número de bancos de dados que podem ser criados, mas vários bancos de dados compartilham os mesmos recursos de servidor.  

4. Liste os bancos de dados digitando `SHOW DATABASES` no prompt do mysql >.

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conectar-se ao servidor usando a ferramenta GUI do MySQL Workbench
1.  Inicie o aplicativo MySQL Workbench no computador cliente. É possível baixar e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   ![configurar nova conexão](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **Configuração** | **Valor sugerido** | **Descrição do Campo** |
|---|---|---|
|   *Nome da Conexão* | Conexão de demonstração| Especifique um rótulo para essa conexão. |
| *Método de Conexão* | Padrão (TCP/IP) | Padrão (TCP/IP) é suficiente. |
| *Nome do host* | myserver4demo.mysql.database.azure.com | Use o nome totalmente qualificado para seu servidor. |
| *Porta* | 3306 | Use a porta padrão 3306. |
| *Nome de Usuário* | myadmin@myserver4demo  | Use o logon de administrador de servidor que você anotou anteriormente com um caractere @ e o nome do servidor. |
| *Senha* | sua senha | Clique no botão Armazenar no cofre... para salvar a senha. |

Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente. Clique em OK para salvar a conexão. 

> [!NOTE]
> O SSL é imposto por padrão no servidor e requer configuração adicional para se conectar com êxito. Consulte [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md).  Se você deseja desabilitar o SSL para este início rápido, visite o portal do Azure e clique na página Segurança de conexão para desabilitar o botão de alternância Impor conexão SSL.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe todos os recursos que você criou no início rápido, excluindo o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1.  No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresourcegroup**.
2.  Em sua página de grupo de recursos, clique em **Excluir**, digite **myresourcegroup** na caixa de texto e, em seguida, clique em Excluir.

Se você deseja excluir o servidor recém-criado:
1.  No menu à esquerda no Portal do Azure, clique em servidores PostgreSQL e, em seguida, pesquise o servidor que você acabou de criar
2.  Na página Visão geral, clique no botão Excluir no painel superior ![Banco de Dados do Azure para MySQL – Excluir servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Confirme o nome do servidor que deseja excluir e exiba sob ele os bancos de dados que são afetados. Digite **myserver4demo** na caixa de texto e clique em Excluir.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar seu primeiro Banco de Dados do Azure para o banco de dados do MySQL](./tutorial-design-database-using-portal.md)


