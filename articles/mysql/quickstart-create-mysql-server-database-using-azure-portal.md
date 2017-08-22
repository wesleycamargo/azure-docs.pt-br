---
title: "Início rápido: criar servidor do Banco de Dados do Azure para MySQL – Portal do Azure | Microsoft Docs"
description: Este artigo o orienta a usar o portal do Azure para criar rapidamente um servidor de Banco de Dados do Azure para MySQL de exemplo em aproximadamente cinco minutos.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure
O Banco de Dados do Azure para MySQL é um serviço gerenciado que permite executar, gerenciar e dimensionar bancos de dados altamente disponíveis do MySQL na nuvem. Este Guia de Início Rápido mostra como criar um Banco de Dados do Azure para o servidor MySQL usando o Portal do Azure em aproximadamente cinco minutos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Primeiro, abra seu navegador da Web e navegue até o [portal do Microsoft Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

## <a name="create-azure-database-for-mysql-server"></a>Criar um servidor de Banco de Dados do Azure para MySQL
Um Banco de Dados do Azure para o servidor MySQL é criado com um conjunto definido de recursos de [computação e armazenamento](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estas etapas para criar um Banco de Dados do Azure para o servidor MySQL:

1. Clique no botão **Novo** (+) encontrado à esquerda superior do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para MySQL** na página **Bancos de Dados**. Você também pode digitar **MySQL** na caixa de pesquisa Nova página para localizar o serviço.
![Portal do Azure – novo – banco de dados – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Preencha o formulário de detalhes do novo servidor com as informações abaixo, conforme mostrado na imagem anterior:

    **Configuração** | **Valor sugerido** | **Descrição do Campo** 
    ---|---|---
    Nome do servidor | myserver4demo | Escolha um nome exclusivo que identifica o Banco de Dados do Azure para o servidor MySQL. O nome de domínio *mysql.database.azure.com* é anexado ao nome do servidor fornecido para os aplicativos conectarem. O nome do servidor pode conter apenas letras minúsculas, números e hífen (-), e deve conter de 3 a 63 caracteres.
    Assinatura | Sua assinatura | A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a que for adequada para a cobrança do recurso.
    Grupo de recursos | myresourcegroup | Você pode criar um novo nome do grupo de recursos ou usar um existente de sua assinatura.
    Logon de administrador do servidor | myadmin | Crie sua própria conta de logon para uso ao se conectar com o servidor. O nome de logon do administrador não pode ser 'azure_superusuário', 'admin', 'administrador', 'root', 'convidado' ou 'público'.
    Senha | *Sua escolha* | Crie uma nova senha para a conta do administrador do servidor. Deve conter de 8 a 128 caracteres. A senha deve conter caracteres de três das categorias a seguir – letras maiúsculas, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #, % etc.).
    Confirmar senha | *Sua escolha*| Confirme a senha da conta do administrador.
    Local | *A região mais próxima de seus usuários*| Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure.
    Versão | *Escolha a versão mais recente*| Escolha a versão mais recente, a menos que você tem requisitos específicos.
    Camada de preços | **Básico**, **50 Unidades de Computação** **50 GB** | Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Escolha a camada **Básico** na guia na parte superior. Clique na extremidade esquerda do controle deslizante **Unidades de Computação** para ajustar o valor para a quantidade mínima disponível para este Guia de Início Rápido. Clique em **OK** para salvar a seleção do tipo de preço. Consulte a seguinte captura de tela.
    Fixar no painel | Verificação | Marque a opção **Fixar no painel** para permitir o controle fácil do seu servidor na página do painel frontal do portal do Azure.

    > [!IMPORTANT]
    > O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste Guia de Início Rápido. Lembre-se ou registre essas informações para o uso posterior.
    > 

    ![Portal do Azure – crie MySQL, fornecendo a entrada de formulário necessária](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Clique em **Criar** para provisionar o servidor. O provisionamento leva alguns minutos, até o máximo de 20 minutos.
   
5.  Na barra de ferramentas, clique em **Notificações** (ícone de sino) para monitorar o processo de implantação.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O serviço do Banco de Dados do Azure para MySQL cria um firewall no nível do servidor. Esse firewall impede que os aplicativos e ferramentas externos se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. 

1.  Localize seu servidor após a conclusão da implantação. Se necessário, você pode pesquisar. Por exemplo, clique em **Todos os Recursos** no menu à esquerda e digite o nome do servidor (como o exemplo *myserver4demo* para procurar o servidor recém-criado. Clique no nome do servidor listado no resultado da pesquisa. A página **Visão geral** do servidor é aberta e oferece outras opções de configuração.

2. Na página do servidor, selecione **Segurança da conexão**.

3.  No cabeçalho **Regras de firewall**, clique na caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Para este Guia de Início Rápido, vamos permitir todos os endereços IP no servidor preenchendo a caixa de texto em cada coluna com os seguintes valores:

    Nome da Regra | IP Inicial | IP Final 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página **Segurança da conexão**, clique em **Salvar**. Aguarde alguns instantes e observe a notificação mostrando que a atualização de segurança de conexão foi concluída com êxito antes de continuar.

    > [!NOTE]
    > As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 3306 poderá não ser permitido pelo firewall de sua rede. Se isso acontecer, você não conseguirá se conectar ao servidor, a menos que o departamento de TI abra a porta 3306.
    > 

## <a name="get-the-connection-information"></a>Obter informações de conexão
Para se conectar ao servidor de banco de dados, você precisa se lembrar do nome do servidor completo e das credenciais de logon do administrador. Você pode ter anotado esses valores anteriormente no artigo do Guia de Início Rápido. Caso não tenha anotado, você pode encontrar facilmente o nome do servidor e as informações de logon na página **Visão Geral** ou na página **Propriedades do servidor** no portal do Azure.

1. Abra a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon de administrador do servidor**. 
    Passe o cursor sobre cada campo e o ícone de cópia aparecerá à direita do texto. Clique no ícone de cópia conforme necessário para copiar os valores.

    Neste exemplo, o nome do servidor é *myserver4demo.mysql.database.azure.com* e o logon de administrador do servidor é  *myadmin@myserver4demo* .

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Conectar-se ao MySQL usando a ferramenta de linha de comando mysql
Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para MySQL. Primeiro, usaremos a ferramenta de linha de comando [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) para ilustrar como se conectar ao servidor.  Você pode usar um navegador da web e o Azure Cloud Shell conforme descrito aqui sem precisar instalar nenhum software adicional. Se você tiver o utilitário mysql instalado localmente em seu próprio computador, poderá conectar também.

1. Inicie o Azure Cloud Shell pelo ícone de terminal (> _) no canto superior direito da página da Web do portal do Azure.

2. O Azure Cloud Shell será aberto no navegador, permitindo que você digite os comandos shell do bash.

    ![Prompt de comando – exemplo de linha de comando mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. No prompt do Cloud Shell, conecte-se ao Banco de Dados do Azure para servidor MySQL digitando a linha de comando mysql no prompt verde.

    O formato a seguir é usado para conectar-se a um Banco de Dados do Azure para servidor MySQL com o utilitário mysql:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Por exemplo, o comando abaixo faz a conexão com nosso servidor de exemplo:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    parâmetro mysql |Valor sugerido|Descrição
    ---|---|---
    --host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para MySQL anteriormente. Nosso servidor de exemplo mostrado é myserver4demo.mysql.database.azure.com. Use o nome de domínio totalmente qualificado (\*.mysql.database.azure.com) conforme mostrado no exemplo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do seu nome do servidor. 
    --user | *nome de logon do administrador do servidor* |Digite o nome de usuário de logon do administrador do servidor fornecido quando criou o Banco de Dados do Azure para MySQL anteriormente. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do nome do usuário.  O formato é *username@servername*.
    --password | *aguarde até a solicitação* | Você verá a solicitação"Digite a senha" depois de inserir o comando. Quando solicitado, digite a mesma senha fornecida quando você criou o servidor.  Observe que os caracteres da senha digitados não são mostrados no prompt do bash quando você os digita. Pressione enter após digitar todos os caracteres para autenticar e conectar.

   Uma vez conectado, o utilitário mysql exibe um prompt `mysql>` para você digitar comandos. 

    Exemplo de saída de mysql:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Se o firewall não está configurado para permitir o endereço IP do Azure Cloud Shell, ocorre o seguinte erro:
    >
    > ERRO 2003 (28000): o cliente com o endereço IP 123.456.789.0 não tem permissão para acessar o servidor.
    >
    > Para resolver o erro, verifique se a configuração do servidor corresponde às etapas na seção *Configurar uma regra de firewall de nível de servidor* do artigo.

4. Exibir o status do servidor para garantir que a conexão está funcionando. Digite `status` no prompt do mysql > depois de conectado.
    ```sql
    status
    ```

   > [!TIP]
   > Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Crie um banco de dados em branco no prompt mysql> digitando o seguinte comando:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    O comando pode levar alguns minutos para ser concluído. 

    Dentro de um banco de dados do Azure para o servidor MySQL, você pode criar um ou mais bancos de dados. Você pode optar por criar um banco de dados por servidor para utilizar todos os recursos ou criar vários bancos de dados para compartilhar os recursos. Não há nenhum limite para o número de bancos de dados que podem ser criados, mas vários bancos de dados compartilham os mesmos recursos de servidor. 

6. Liste os bancos de dados no prompt mysql> digitando o seguinte comando:

    ```sql
    SHOW DATABASES;
    ```

7.  Digite `\q` e pressione ENTER para fechar a ferramenta mysql. Quando terminar, você poderá fechar o Azure Cloud Shell.

Agora, você conectou o Banco de Dados do Azure para MySQL e criou um banco de dados do usuário em branco. Continue para a próxima seção para repetir um exercício semelhante e se conectar ao mesmo servidor usando outra ferramenta comum, o MySQL Workbench.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Conectar-se ao servidor usando a ferramenta GUI do MySQL Workbench
Para se conectar ao servidor MySQL do Azure usando a ferramenta de GUI MySQL Workbench:

1.  Inicie o aplicativo MySQL Workbench no computador cliente. É possível baixar e instalar o MySQL Workbench [aqui](https://dev.mysql.com/downloads/workbench/).

2.  Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

    ![configurar nova conexão](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Configuração** | **Valor sugerido** | **Descrição do Campo** |
    |---|---|---|
    |   Nome da Conexão | Conexão de demonstração | Especifique um rótulo para essa conexão. |
    | Método de Conexão | Padrão (TCP/IP) | Padrão (TCP/IP) é suficiente. |
    | Nome do host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para MySQL anteriormente. Nosso servidor de exemplo mostrado é myserver4demo.mysql.database.azure.com. Use o nome de domínio totalmente qualificado (\*.mysql.database.azure.com) conforme mostrado no exemplo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do seu nome do servidor.  |
    | Porta | 3306 | Sempre use a porta 3306 ao conectar o Banco de Dados do Azure para MySQL. |
    | Nome de Usuário |  *nome de logon do administrador do servidor* | Digite o nome de usuário de logon do administrador do servidor fornecido na criação do Banco de Dados do Azure para MySQL anteriormente. Nosso nome de usuário de exemplo é myadmin@myserver4demo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do nome do usuário. O formato é *username@servername*.
    | Senha | sua senha | Clique no botão Armazenar no cofre... para salvar a senha. |

    Clique em **Testar Conexão** para testar se todos os parâmetros estiverem configurados corretamente. Clique em OK para salvar a conexão. 

    > [!NOTE]
    > O SSL é imposto por padrão no servidor e requer configuração adicional para se conectar com êxito. Confira [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md).  Se você deseja desabilitar o SSL para este Guia de Início Rápido, visite o portal do Azure e clique na página Segurança de conexão para desabilitar o botão de alternância Impor conexão SSL.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no início rápido excluindo o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos, ou excluindo o recurso de servidor se quiser manter os outros recursos.

> [!TIP]
> Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.
>

Para excluir o grupo de recursos inteiro, incluindo o servidor criado recentemente:
1.  Encontre o grupo de recursos no portal do Azure. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado, como nosso exemplo **myresourcegroup**.
2.  Na página do seu grupo de recursos, clique em **Excluir**. Em seguida, digite o nome do grupo de recursos, como o nosso exemplo **myresourcegroup**, na caixa de texto para confirmar a exclusão e clique em **Excluir**.

Ou, em vez disso, para excluir o servidor criado recentemente:
1.  Encontre seu servidor no portal do Azure, se não estiver com ele aberto. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e pesquise pelo servidor que você criou.
2.  Na página **Visão Geral**, clique no botão **Excluir** no painel superior.
![Banco de Dados do Azure para MySQL - Excluir servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Confirme o nome do servidor que deseja excluir e exiba sob ele os bancos de dados que são afetados. Digite o nome do servidor na caixa de texto, como o nosso exemplo **myserver4demo**, e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar seu primeiro Banco de Dados do Azure para o banco de dados do MySQL](./tutorial-design-database-using-portal.md)


