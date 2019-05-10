---
title: Banco de Dados do Azure para PostgreSQL – Guia de início rápido para Hyperscale (Citus) (visualização)
description: Guia de início rápido para criar e consultar tabelas distribuídas no Hyperscale (Citus) do Banco de Dados do Azure para PostgreSQL (visualização).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 30de4da43569abf4d7bd668fd0fa481ecac23f4d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080024"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Início Rápido: Criar um Banco de Dados do Azure para PostgreSQL - Hyperscale (Citus) (visualização) no portal do Azure

O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado usado para executar, gerenciar e dimensionar bancos de dados altamente disponíveis do PostgreSQL na nuvem. Este Guia de Início Rápido mostra como criar um Banco de Dados do Azure para PostgreSQL - grupo de servidores Hyperscale (Citus) (visualização) usando o portal do Azure. Você explorará os dados distribuídos: fragmentar tabelas entre os nós, ingerir dados de amostra e efetuar consultas que são executadas em vários nós.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar um Banco de Dados do Azure para o PostgreSQL

Siga estas etapas para criar um Banco de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para PostgreSQL** na página **Bancos de Dados**.
3. Para a opção de implantação, clique no botão **Criar** em **grupo de servidores Hyperscale (Citus) - VISUALIZAÇÃO.**
4. Preencha o formulário de detalhes sobre o novo servidor com as seguintes informações:
   - Grupo de recursos: clique no link **Criar novo** abaixo da caixa de texto desse campo. Insira um nome como **myresourcegroup**.
   - Nome do grupo de servidores: digite um nome exclusivo para o novo grupo de servidores que também poderá ser usado para um subdomínio do servidor.
   - Nome de usuário administrador: insira um nome de usuário que será usado posteriormente para conectar ao banco de dados.
   - Senha: deve ter pelo menos oito caracteres e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.)
   - Localização: utilize a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.

   > [!IMPORTANT]
   > O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste início rápido. Lembre-se ou registre essas informações para o uso posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações nessa seção inalteradas e clique em **Salvar**.
6. Clique em **Examinar + criar** e, em seguida, **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.
7. A página será redirecionada para monitorar a implantação. Quando o status em tempo real mudar de **Sua implantação está em andamento** para **Sua implantação está concluída**, clique no item de menu **Saídas** no lado esquerdo da página.
8. A página de saídas conterá um nome do host do coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O serviço do Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (visualização) usa um firewall no nível do servidor. Por padrão, o firewall impede que aplicativos e ferramentas externos se conectam ao nó coordenador e a qualquer banco de dados interno. É necessário adicionar uma regra para abrir o firewall para um intervalo de endereços IP específico.

1. Na seção **Saídas** em que você copiou anteriormente o nome do host do nó coordenador, clique novamente no item de menu **Visão geral**.

2. O nome do seu grupo de escala da implantação será prefixado como “sg-”. Encontre-o na lista de recursos e clique nele.

3. Clique em **Firewall** em **Segurança** no menu à esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP do cliente atual**. Por fim, clique no botão **Salvar**.

5. Clique em **Save** (Salvar).

   > [!NOTE]
   > O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Conectar o banco de dados usando o psql no Cloud Shell

Usaremos agora o utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/app-psql.html) para nos conectarmos ao servidor do Banco de Dados do Azure para PostgreSQL.
1. Inicie o Azure Cloud Shell por meio do ícone do terminal no painel de navegação superior.

   ![Banco de Dados do Azure para PostgreSQL – Ícone do terminal do Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no seu navegador, permitindo que você digite comandos de bash.

   ![Banco de Dados do Azure para PostgreSQL – Prompt de bash do Azure Shell](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. No prompt do Cloud Shell, conecte-se ao Banco de Dados do Azure para servidor PostgreSQL usando os comandos psql. O formato a seguir é usado para conectar-se a um Banco de Dados do Azure para servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando a seguir conecta o banco de dados padrão chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira a senha de administrador do servidor quando solicitado.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Criar e distribuir tabelas

Uma vez conectado ao nó coordenador em hiperescala usando o psql, você pode concluir algumas tarefas básicas.

Dentro dos servidores Hyperscale, há três tipos de tabelas:

- Tabelas fragmentadas ou distribuídas (espalhadas para auxiliar na escala de desempenho e paralelização)
- Tabelas de referência (várias cópias mantidas)
- Tabelas locais (geralmente usadas para tabelas de administrador interno)

Neste guia de início rápido, nos concentraremos principalmente em tabelas distribuídas e nos familiarizaremos com elas.

O modelo de dados que vamos trabalhar é simples: dados de evento e do usuário do GitHub. Eventos incluem a criação de fork, confirmações do git relacionadas a uma organização e muito mais.

Depois de se conectar via psql, vamos criar nossas tabelas. No console do psql, execute:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

O `payload` campo de `github_events` tem um tipo de dados JSONB. O JSONB é o tipo de dados JSON em formato binário no Postgres. Isso torna mais fácil de armazenar um esquema mais flexível em uma única coluna.

O Postgres pode criar um `GIN` índice neste tipo que indexará cada chave e valor dentro dele. Com um índice, se tornará rápido e fácil consultar o conteúdo com várias condições. Vamos em frente para criar alguns índices antes de podermos carregar nossos dados. No psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Em seguida, vamos pegar as tabelas Postgres no nó coordenador e informar ao Hyperscale para fragmentá-las entre os trabalhadores. Para fazer isso, executaremos uma consulta para cada tabela especificando a chave para fragmentá-la. No exemplo atual, fragmentaremos a tabela de eventos e de usuários em `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Estamos prontos para carregar dados. Realize o download dos arquivos de dois exemplo [Users. csv](https://examples.citusdata.com/users.csv) e [Events](https://examples.citusdata.com/events.csv). Depois de baixar os arquivos, conecte-se ao banco de dados usando o psql, tomando cuidado para executar o psql no diretório que contém os arquivos baixados. Carregar os dados com o comando `\copy`:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Executar consultas

Agora é a hora da diversão: realmente efetuar algumas consultas. Vamos começar com um simples `count (*)` para ver a quantidade de dados carregada:

```sql
SELECT count(*) from github_events;
```

Funcionou muito bem. Voltaremos para esse tipo de agregação em breve, mas agora vamos examinar algumas outras consultas. Na coluna `payload` JSONB há um bom volume de dados, mas varia com base no tipo de evento. Os eventos `PushEvent` contêm um tamanho que inclui o número de confirmações distintos para o envio por push. É possível usar isso para localizar o número total de confirmações por hora:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Até agora, as consultas envolveram os eventos github\_de modo exclusivo, mas podemos combinar essas informações com os usuários do github\_. Uma vez que criamos usuários fragmentados e eventos no mesmo identificador (`user_id`), as linhas de ambas as tabelas com IDs de usuário correspondentes serão [colocadas](http://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) nos mesmos nós de banco de dados e podem ser unidas facilmente.

Se entrarmos em `user_id`, o Hyperscale pode efetuar push na execução de junção em fragmentos para execução em paralelo nos nós do trabalhador. Por exemplo, vamos encontrar os usuários que criaram o maior número de repositórios:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão **Excluir** na página **Visão geral** do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão **Excluir** final.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu como provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados.

Em seguida, siga um tutorial para compilar aplicativos escalonáveis de multilocatário.
> [!div class="nextstepaction"]
> [Criar um Banco de Dados Multilocatário](https://aka.ms/hyperscale-tutorial-multi-tenant)
