---
title: 'Portal do Azure: criar um Banco de Dados do Azure para PostgreSQL | Microsoft Docs'
description: "Guia de início rápido para criar e gerenciar o Banco de Dados do Azure para o servidor PostgreSQL usando a interface do usuário do Portal do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: a4ab9e9e7d331d35547d56406e1da993ade58113
ms.contentlocale: pt-br
ms.lasthandoff: 08/23/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Criar um Banco de Dados do Azure para PostgreSQL no Portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado que permite executar, gerenciar e dimensionar os bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este guia de início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL usando o Portal do Azure em aproximadamente cinco minutos.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Primeiro, abra seu navegador da Web e navegue até o [portal do Microsoft Azure](https://portal.azure.com/). Insira suas credenciais para entrar no portal. A exibição padrão é o painel de serviço.

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
    Logon de administrador do servidor |*mylogin*| Crie sua própria conta de logon para uso ao se conectar com o servidor. O nome de logon do administrador não pode ser 'azure_superuser', 'azure_pg_admin', 'admin', 'administrator', 'root', 'guest' nem 'public', e não pode iniciar com 'pg_'.
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

1.  Localize seu servidor após a conclusão da implantação. Se necessário, você pode pesquisar. Por exemplo, clique em **Todos os Recursos** no menu à esquerda e digite o nome do servidor (como *mypgserver-20170401*) para procurar o servidor recém-criado. Clique no nome do servidor listado no resultado da pesquisa. A página **Visão geral** do servidor é aberta e oferece outras opções de configuração.
 
    ![Banco de Dados do Azure para PostgreSQL – Pesquisar o nome do servidor](./media/quickstart-create-database-portal/4-locate.png)

2.  Na página do servidor, selecione **Segurança da conexão**. 
    ![Banco de Dados do Azure para PostgreSQL - Criar Regra de firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  No cabeçalho **Regras de firewall**, clique na caixa de texto em branco na coluna **Nome da Regra** para começar a criar a regra de firewall. 

    Para este início rápido, vamos permitir todos os endereços IP no servidor preenchendo a caixa de texto em cada coluna com os seguintes valores:

    Nome da Regra | IP Inicial | IP Final 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Na barra de ferramentas superior da página Segurança da conexão, clique em **Salvar**. Aguarde alguns instantes e observe a notificação mostrando que a atualização de segurança de conexão foi concluída com êxito antes de continuar.

    > [!NOTE]
    > As conexões ao Banco de Dados do Azure para servidor PostgreSQL se comunicam pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não conseguirá se conectar ao servidor do Azure MySQL, a menos que o departamento de TI abra a porta 5432.
    >

## <a name="get-the-connection-information"></a>Obter informações de conexão

Quando criamos nosso servidor Banco de Dados do Azure para PostgreSQL, um banco de dados padrão chamado **postgres** foi criado. Para se conectar ao servidor de banco de dados, você precisa se lembrar do nome do servidor completo e das credenciais de logon do administrador. Você pode ter anotado esses valores anteriormente no artigo do início rápido. Caso não tenha anotado, você pode encontrar facilmente o nome do servidor e as informações de logon na página Visão geral do servidor no portal do Azure.

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
    --host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para PostgreSQL anteriormente. Nosso servidor de exemplo mostrado é mypgserver-20170401.postgres.database.azure.com. Use o nome de domínio totalmente qualificado (\*.postgres.database.azure.com) conforme mostrado no exemplo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do seu nome do servidor. 
    --port | **5432** | Sempre use a porta 5432 ao conectar o Banco de Dados do Azure para PostgreSQL. 
    --username | *nome de logon do administrador do servidor* |Digite o nome de usuário de logon do administrador do servidor fornecido quando criou o Banco de Dados do Azure para PostgreSQL anteriormente. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do nome do usuário.  O formato é *username@servername*.
    --dbname | **postgres** | Use o nome do banco de dados padrão gerado pelo sistema *postgres* para a primeira conexão. Depois, você criará seu próprio banco de dados.

    Depois de executar o comando psql, com seus próprios valores do parâmetro, você será solicitado a digitar a senha do administrador do servidor. A senha é a mesma fornecida quando você criou o servidor. 

    parâmetro psql |Valor sugerido|Descrição
    ---|---|---
    Senha | *sua senha do administrador* | Observe que os caracteres da senha digitados não são mostrados no prompt do bash. Pressione enter após digitar todos os caracteres para autenticar e conectar.

    Uma vez conectado, o utilitário psql exibe um aviso de postgres onde você digita comandos sql. Na saída de conexão inicial, um aviso pode ser exibido, já que o psql no Azure Cloud Shell pode ser uma versão diferente da versão do Banco de Dados do Azure para servidor PostgreSQL. 
    
    Exemplo de saída psql:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Se o firewall não está configurado para permitir o endereço IP do Azure Cloud Shell, ocorre o seguinte erro:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL:  SSL connection is required. Especifique as opções de SSL e tente novamente.
    > 
    > Para resolver o erro, verifique se a configuração do servidor corresponde às etapas na seção *Configurar uma regra de firewall de nível de servidor* do artigo.

4.  Crie um banco de dados em branco no prompt digitando o seguinte comando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    O comando pode levar alguns minutos para ser concluído. 

5.  No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado.
    ```bash
    \c mypgsqldb
    ```

6.  Digite \q, em seguida, pressione ENTER para sair do psql. Quando terminar, você poderá fechar o Azure Cloud Shell.

Agora, você conectou o Banco de Dados do Azure para PostgreSQL e criou um banco de dados do usuário em branco. Continue na próxima seção para conectar usando outra ferramenta comum, pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conectar-se ao banco de dados PostgreSQL usando pgAdmin

Para se conectar ao servidor PostgreSQL do Azure usando a ferramenta GUI _pgAdmin_
1.  Inicie o aplicativo _pgAdmin_ no computador cliente. Você pode instalar o _pgAdmin_ de http://www.pgadmin.org/.
2.  Clique no ícone **Adicionar Novo Servidor** da seção **Links Rápidos** no centro da página Painel.
3.  Na guia **Geral** da caixa de diálogo **Criar – Servidor**, insira um nome amigável exclusivo para o servidor, como **Servidor PostgreSQL do Azure**.
![ferramenta pgAdmin – criar – servidor](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Na caixa de diálogo **Criar – Servidor**, na guia **Conexão**, use as configurações conforme especificado e clique em **Salvar**.
   ![pgAdmin – Criar – Servidor](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    parâmetro pgAdmin |Valor sugerido|Descrição
    ---|---|---
    Nome/endereço do host | *nome do servidor* | Especifique o valor do nome do servidor que foi usado quando você criou o Banco de Dados do Azure para PostgreSQL anteriormente. Nosso servidor de exemplo mostrado é mypgserver-20170401.postgres.database.azure.com. Use o nome de domínio totalmente qualificado (\*.postgres.database.azure.com) conforme mostrado no exemplo. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do seu nome do servidor. 
    Porta | **5432** | Sempre use a porta 5432 ao conectar o Banco de Dados do Azure para PostgreSQL.  
    Manutenção do banco de dados | **postgres** | Use o nome de banco de dados padrão gerado pelo sistema *postgres*.
    Nome de usuário | *nome de logon do administrador do servidor* | Digite o nome de usuário de logon do administrador do servidor fornecido quando criou o Banco de Dados do Azure para PostgreSQL anteriormente. Siga as etapas na seção anterior para obter as informações da conexão, caso não se lembre do nome do usuário. O formato é *username@servername*.
    Senha | *sua senha do administrador* |  A senha que você escolheu ao criar o servidor anteriormente neste guia de início rápido.
    Função | *deixar em branco* | Não é necessário fornecer um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | Exigência | Por padrão, todos os servidores PostgreSQL do Azure são criados com a imposição de SSL ligada. Para DESLIGAR a imposição de SSL, consulte os detalhes em [Imposição de SSL](./concepts-ssl-connection-security.md).
    
5.  Clique em **Salvar**.
6.  No painel esquerdo do navegador, expanda o nó **Servidores**. Escolha o servidor, por exemplo, **Azure PostgreSQL Server**, e clique para se conectar a ele.
7. Expanda o nó do servidor e expanda **Bancos de Dados** nele. A lista deve conter seu banco de dados *postgres* existente e bancos de dados de usuário recém-criados, como *mypgsqldb*, que criamos na seção anterior. Observe que você pode criar vários bancos de dados por servidor com o Banco de Dados do Azure para PostgreSQL.
8. Clique duas vezes em **Bancos de Dados**, escolha o menu **Criar** e clique em **Banco de Dados**.
9.  Digite um nome de banco de dados de sua escolha no campo **Banco de Dados**, por exemplo, o *mypgsqldb* mostrado no exemplo. 
10. Selecione o **Proprietário** do banco de dados na caixa suspensa. Escolha o nome de logon do administrador do servidor, como o nosso exemplo *mylogin*.
10. Clique em **Salvar** para criar um novo banco de dados em branco.
11. No painel **Navegador**, confira o banco de dados que você criou na lista de bancos de dados no nome do seu servidor.
 ![pgAdmin – Criar – Banco de Dados](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no início rápido excluindo o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos, ou excluindo o recurso de servidor se quiser manter os outros recursos.

> [!TIP]
> Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir os recursos criados por esse início rápido no portal do Azure.

Para excluir o grupo de recursos inteiro, incluindo o servidor criado recentemente:
1.  Encontre o grupo de recursos no portal do Azure. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado, como nosso exemplo **myresourcegroup**.
2.  Na página do seu grupo de recursos, clique em **Excluir**. Em seguida, digite o nome do grupo de recursos, como o nosso exemplo **myresourcegroup**, na caixa de texto para confirmar a exclusão e clique em **Excluir**.

Ou, em vez disso, para excluir o servidor criado recentemente:
1.  Encontre seu servidor no portal do Azure, se não estiver com ele aberto. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e pesquise pelo servidor que você criou.
2.  Na página **Visão Geral**, clique no botão **Excluir** no painel superior.
![Banco de Dados do Azure para PostgreSQL - Excluir servidor](./media/quickstart-create-database-portal/12-delete.png)
3.  Confirme o nome do servidor que deseja excluir e exiba sob ele os bancos de dados que são afetados. Digite o nome do servidor na caixa de texto, como o nosso exemplo **mypgserver-20170401** e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)

