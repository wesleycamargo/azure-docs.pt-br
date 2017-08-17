---
title: 'Portal do Azure: criar um Banco de Dados do Azure para PostgreSQL | Microsoft Docs'
description: "Guia de início rápido para criar e gerenciar o Banco de Dados do Azure para o servidor PostgreSQL usando a interface do usuário do Portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Criar um Banco de Dados do Azure para PostgreSQL no Portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado que permite executar, gerenciar e dimensionar os bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este guia de início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL usando o Portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar um Banco de Dados do Azure para o PostgreSQL

Um Banco de Dados do Azure para PostgreSQL é criado com um conjunto definido de [recursos de computação e armazenamento](./concepts-compute-unit-and-storage.md). O servidor é criado dentro de um [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Siga estas etapas para criar um Banco de Dados do Azure para o servidor PostgreSQL:
1.  Clique no botão **Novo** (+) encontrado à esquerda superior do Portal do Azure.
2.  Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para PostgreSQL** na página **Bancos de Dados**.
 ![Banco de Dados do Azure para PostgreSQL – Criar o banco de dados](./media/quickstart-create-database-portal/1-create-database.png)

3.  Preencha o formulário de detalhes do novo servidor com as informações abaixo, conforme mostrado na imagem anterior:

    Configuração|Valor sugerido|Descrição
    ---|---|---
    Nome do servidor |*mypgserver-20170401*|Escolha um nome exclusivo que identifica o Banco de Dados do Azure para o servidor PostgreSQL. O nome de domínio *postgres.database.azure.com* é anexado ao nome do servidor fornecido para os aplicativos conectarem. O nome do servidor pode conter apenas letras minúsculas, números e hífen (-), e deve conter de 3 a 63 caracteres.
    Assinatura|*Sua assinatura*|A assinatura do Azure que você deseja usar para o servidor. Se você tiver várias assinaturas, escolha a que for adequada para a cobrança do recurso.
    Grupo de recursos|*myresourcegroup*| Você pode criar um novo nome do grupo de recursos ou usar um existente de sua assinatura.
    Logon de administrador do servidor |*mylogin*| Crie sua própria conta de logon para ser usada ao conectar o servidor. O nome de logon do administrador não pode ser 'azure_superuser', 'azure_pg_admin', 'admin', 'administrator', 'root', 'guest' nem 'public', e não pode iniciar com 'pg_'.
    Senha |*Sua escolha* | Crie uma nova senha para a conta do administrador do servidor. Deve conter de 8 a 128 caracteres. A senha deve conter caracteres de três das categorias a seguir – letras maiúsculas, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #, % etc.).
    Local|*A região mais próxima de seus usuários*| Escolha o local mais próximo para seus usuários.
    Versão do PostgreSQL|*Escolha a versão mais recente*| Escolha a versão mais recente, a menos que você tem requisitos específicos.
    Camada de preços | **Básico**, **50 Unidades de Computação** **50 GB** | Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Escolha a camada Básico na guia na parte superior. Clique na extremidade esquerda do controle deslizante Unidades de Computação para ajustar o valor para a quantidade mínima disponível para este início rápido. Clique em **OK** para salvar a seleção do tipo de preço. Consulte a seguinte captura de tela.
    | Fixar no painel | Verificação | Marque a opção **Fixar no painel** para permitir o controle fácil do seu servidor na página do painel frontal do portal do Azure.

  > [!IMPORTANT]
  > O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste início rápido. Lembre-se ou registre essas informações para o uso posterior.

    ![Banco de Dados do Azure para PostgreSQL – escolher tipo de preço](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Clique em **Criar** para provisionar o servidor. O provisionamento leva alguns minutos, até o máximo de 20 minutos.

5.  Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.
 ![Banco de Dados do Azure para PostgreSQL – Ver notificações](./media/quickstart-create-database-portal/3-notifications.png)
   
  Por padrão, o banco de dados **postgres** é criado em seu servidor. O [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado a uso por usuários, utilitários e aplicativos de terceiros. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O serviço do Banco de Dados do Azure para PostgreSQL cria um firewall no nível do servidor. Esse firewall impede que os aplicativos e ferramentas externos se conectem ao servidor e aos bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. 

1.  Localize seu servidor após a conclusão da implantação. Se necessário, você pode pesquisar. Por exemplo, clique em **Todos os Recursos** no menu à esquerda e digite o nome do servidor (como mypgserver-20170401) para procurar o servidor recém-criado. Clique no nome do servidor listado no resultado da pesquisa. A página Visão geral do servidor é aberta e oferece outras opções de configuração.
 
    ![Banco de Dados do Azure para PostgreSQL – Pesquisar o servidor ](./media/quickstart-create-database-portal/4-locate.png)

2.  Na página do servidor, selecione **Segurança da Conexão**. 
    ![Banco de Dados do Azure para PostgreSQL - Criar Regra de Firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  No cabeçalho **Regras de Firewall**, clique na caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Para este início rápido, vamos permitir todos os endereços IP no servidor preenchendo a caixa de texto em cada coluna com os seguintes valores:

    Nome da Regra | IP Inicial | IP Final 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página Segurança da Conexão, clique em **Salvar**. Então, clique em **X** para fechar a página Segurança da Conexão.

    > [!NOTE]
    > O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá se conectar ao servidor de Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
  >

## <a name="get-the-connection-information"></a>Obter informações de conexão

Quando criamos nosso servidor Banco de Dados do Azure para PostgreSQL, um banco de dados padrão chamado **postgres** foi criado. Para conectar o servidor de banco de dados, você precisa lembrar o nome do servidor completo e as credenciais de logon do administrador. Você pode ter anotado esses valores anteriormente no artigo do início rápido. Caso não tenha anotado, localize facilmente o nome do servidor e as informações de logon na página Visão geral do servidor no portal do Azure.

1. Abra a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon de administrador do servidor**.
    Passe o cursor sobre cada campo e o ícone de cópia aparecerá à direita do texto. Clique no ícone de cópia conforme necessário para copiar os valores.

 ![Banco de Dados do Azure para PostgreSQL – Logon de administrador do servidor](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Conectar-se ao banco de dados PostgreSQL usando psql no Cloud Shell

Há vários aplicativos que você pode usar para conectar o servidor Banco de Dados do Azure para PostgreSQL. Primeiro, usaremos o utilitário da linha de comando psql para ilustrar como conectar o servidor.  Você pode usar um navegador da web e o Azure Cloud Shell conforme descrito aqui sem precisar instalar nenhum software adicional. Se você tiver o utilitário psql instalado localmente em seu próprio computador, poderá conectar também.

1. Inicie o Azure Cloud Shell por meio do ícone do terminal no painel de navegação superior.

   ![Banco de Dados do Azure para PostgreSQL – Ícone do terminal do Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. O Azure Cloud Shell será aberto no navegador, permitindo que você digite os comandos shell do bash.

   ![Banco de Dados do Azure para PostgreSQL – Prompt de bash do Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. No prompt do Cloud Shell, conecte um banco de dados no servidor Banco de Dados do Azure para PostgreSQL digitando a linha de comando psql no prompt verde.

    O formato a seguir é usado para conectar-se a um Banco de Dados do Azure para servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Por exemplo, o comando a seguir conecta um servidor de exemplo:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    --host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para PostgreSQL anteriormente. Nosso servidor de exemplo mostrado é mypgserver-20170401.postgres.database.azure.com. Use o nome de domínio totalmente qualificado (\*.postgres.database.azure.com) conforme mostrado no exemplo. Siga a seção anterior para obter as informações da conexão, caso não lembre seu nome do servidor. 
    --port | **5432** | Sempre use a porta 5432 ao conectar o Banco de Dados do Azure para PostgreSQL. 
    --username | *nome de logon do administrador do servidor* |Digite o nome de usuário de logon do administrador do servidor fornecido quando criou o Banco de Dados do Azure para PostgreSQL anteriormente. Siga a seção anterior para obter as informações da conexão, caso não lembre o nome do servidor.  O formato é *username@servername*.
    --dbname | **postgres** | Use o nome do banco de dados padrão gerado pelo sistema *postgres* para a primeira conexão. Depois, você criará seu próprio banco de dados.

    Depois de executar o comando psql, com seus próprios valores do parâmetro, você será solicitado a digitar a senha do administrador do servidor. Esta é a mesma senha fornecida quando você criou o servidor. 

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    Senha | *sua senha do administrador* | Observe que os caracteres da senha digitados não serão mostrados no prompt do bash. Pressione enter após digitar todos os caracteres para autenticar e conectar.

4.  Quando já estiver conectado ao servidor, crie um banco de dados em branco no prompt digitando o comando a seguir:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado.
    ```bash
    \c mypgsqldb
    ```

6.  Digite \q, em seguida, pressione ENTER para sair do psql. Você pode fechar o Azure Cloud Shell.

Agora, você conectou o Banco de Dados do Azure para PostgreSQL e criou um banco de dados do usuário em branco. Continue na próxima seção para conectar usando outra ferramenta comum, pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conectar-se ao banco de dados PostgreSQL usando pgAdmin

Para se conectar ao servidor PostgreSQL do Azure usando a ferramenta GUI _pgAdmin_
1.  Inicie o aplicativo _pgAdmin_ no computador cliente. Você pode instalar o _pgAdmin_ de http://www.pgadmin.org/.
2.  Escolha **Adicionar Novo Servidor** no menu **Links Rápidos**.
3.  Na guia **Geral** da caixa de diálogo **Criar – Servidor**, insira um nome amigável exclusivo para o servidor, como **Servidor PostgreSQL do Azure**.
![ferramenta pgAdmin – criar – servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Na caixa de diálogo **Criar – Servidor**, na guia **Conexão**, use as configurações conforme especificado e clique em **Salvar**.
   ![pgAdmin – Criar – Servidor](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Nome/Endereço de host**: mypgserver-20170401.postgres.database.azure.com 
        - Nome do servidor totalmente qualificado.
    - **Porta:** 5432
        - O número da porta usada por este servidor de banco de dados é 5432.
    - **Banco de Dados de manutenção**: postgres 
        - Nome de banco de dados padrão gerado pelo sistema.
    - **Nome de usuário:** mylogin@mypgserver-20170401 
        - O logon de administrador do servidor (user@mypgserver) obtido anteriormente neste guia de início rápido.
    - **Senha**: a senha que você escolheu ao criar o servidor anteriormente neste guia de início rápido.
    - **Modo SSL**: Exigir
        - Por padrão, todos os servidores PostgreSQL do Azure são criados com a imposição de SSL ligada. Para DESLIGAR a imposição de SSL, consulte os detalhes em [Imposição de SSL](./concepts-ssl-connection-security.md).
5.  Clique em **Salvar**.
6.  No painel esquerdo do Navegador, expanda os **Grupos de Servidores**. Escolha o **Servidor PostgreSQL do Azure**.
7.  Escolha o **Servidor** ao qual você se conectou e, em seguida, escolha **Bancos de Dados** sob ele. 
8.  Clique com o botão direito do mouse em **Bancos de Dados** para criar um banco de dados.
9.  Escolha um nome de banco de dados **mypgsqldb** e o proprietário para ele como o logon de administrador de servidor **mylogin**.
10. Clique em **Salvar** para criar um banco de dados em branco.
11. No **Navegador**, expanda o **Servidor**. Expanda o servidor que você criou e veja o banco de dados **mypgsqldb** sob ele.
 ![pgAdmin – Criar – Banco de Dados](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
Limpe todos os recursos que você criou no início rápido, excluindo o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.

1.  No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e, em seguida, clique em **myresourcegroup**.
2.  Em sua página de grupo de recursos, clique em **Excluir**, digite **myresourcegroup** na caixa de texto e, em seguida, clique em Excluir.

Se você apenas deseja excluir o servidor recém-criado:
1.  No menu à esquerda no Portal do Azure, clique em servidores PostgreSQL e, em seguida, pesquise o servidor que você acabou de criar
2.  Na página Visão geral, clique no botão Excluir no painel superior ![Banco de Dados do Azure para PostgreSQL – Excluir servidor](./media/quickstart-create-database-portal/12-delete.png)
3.  Confirme o nome do servidor que deseja excluir e exiba sob ele os bancos de dados que são afetados. Digite **mypgserver-20170401** na caixa de texto e, em seguida, clique em Excluir.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)

