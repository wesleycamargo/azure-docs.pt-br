---
title: Criar um painel em tempo real com o Banco de Dados do Azure para PostgreSQL – Tutorial de Hyperscale (Citus) (versão prévia)
description: Este tutorial mostra como criar, popular e consultar tabelas distribuídas em Hyperscale (Citus) do Banco de Dados do Azure para PostgreSQL (versão prévia).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079451"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutorial: Criar um painel de análise em tempo real usando o Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (versão prévia)

Neste tutorial, você utiliza o Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (versão prévia) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Fragmentar tabelas entre nós
> * Gerar dados de exemplo
> * Executar rollups
> * Consultar dados brutos e agregados
> * Expirar dados

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Criar um Banco de Dados do Azure para o PostgreSQL

Siga estas etapas para criar um Banco de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **Bancos de Dados** na página **Novo** e selecione **Banco de Dados do Azure para PostgreSQL** na página **Bancos de Dados**.
3. Para a opção de implantação, clique no botão **Criar** em **grupo de servidores Hyperscale (Citus) - VERSÃO PRÉVIA.**
4. Preencha o formulário de detalhes sobre o novo servidor com as seguintes informações:
   - Grupo de recursos: clique no link **Criar novo** abaixo da caixa de texto desse campo. Insira um nome como **myresourcegroup**.
   - Nome do grupo de servidores: **mydemoserver** (nome de um servidor que é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo).
   - Nome de usuário administrador: **myadmin** (ele será usado posteriormente para conectar o banco de dados).
   - Senha: deve ter pelo menos oito caracteres de comprimento e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 - 9) e caracteres não alfanuméricos (!, $, #, % etc.)
   - Localização: use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.

   > [!IMPORTANT]
   > O logon de administrador do servidor e a senha que você especificar aqui são necessários para fazer logon no servidor e em seus bancos de dados mais tarde neste tutorial. Lembre-se ou registre essas informações para o uso posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações nessa seção inalteradas e clique em **Salvar**.
6. Clique em **Examinar + criar** e, em seguida, **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.
7. A página será redirecionada para monitorar a implantação. Quando o status ao vivo mudar de **Sua implantação está em andamento** para **Sua implantação está concluída**, clique no item de menu **Saídas** no lado esquerdo da página.
8. A página de saídas conterá um nome do host do coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para o uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

O serviço do Banco de Dados do Azure para PostgreSQL cria um firewall no nível do servidor. Por padrão, o firewall impede que todas as ferramentas e aplicativos externos se conectem ao servidor e a quaisquer bancos de dados no servidor. É necessário adicionar uma regra para abrir o firewall em um intervalo de endereços IP específico.

1. Na seção **Saídas** em que você copiou anteriormente o nome do host do nó coordenador, clique em novamente no item de menu **Visão geral**.

2. Localize o grupo de escala para a implantação na lista de recursos e clique nele. (O nome dele será prefixado com "sg-".)

3. Clique em **Firewall** em **Segurança** no menu à esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP do cliente atual**. Por fim, clique no botão **Salvar**.

5. Clique em **Save** (Salvar).

   > [!NOTE]
   > O servidor PostgreSQL do Azure se comunica pela porta 5432. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Se isso acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Conectar o banco de dados usando o psql no Cloud Shell

Usaremos agora o utilitário de linha de comando [psql](https://www.postgresql.org/docs/current/app-psql.html) para nos conectarmos ao servidor do Banco de Dados do Azure para PostgreSQL.
1. Inicie o Azure Cloud Shell por meio do ícone do terminal no painel de navegação superior.

   ![Banco de Dados do Azure para PostgreSQL – Ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no seu navegador, permitindo que você digite comandos de bash.

   ![Banco de Dados do Azure para PostgreSQL – Prompt de bash do Azure Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. No prompt do Cloud Shell, conecte-se ao Banco de Dados do Azure para servidor PostgreSQL usando os comandos psql. O formato a seguir é usado para conectar-se a um Banco de Dados do Azure para servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando a seguir conecta o banco de dados padrão chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira a senha de administrador do servidor quando solicitado.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao Banco de Dados do Azure para PostgreSQL - Hyperscale (Citus) (versão prévia) usando o psql, é possível realizar algumas tarefas básicas. Este tutorial orienta você a ingerir dados de tráfego do Web Analytics e, em seguida, acumular os dados para fornecer painéis em tempo real com base nesses dados.

Vamos criar uma tabela que consumirá todos os nossos dados brutos de tráfego da Web. Execute os seguintes comandos no terminal do psql:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Também vamos criar uma tabela que conterá nossas agregações por minuto e uma tabela que mantém a posição do nosso último rollup. Também execute o seguinte no psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Agora é possível ver as tabelas recém-criadas na lista de tabelas com este comando do psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Fragmentar tabelas entre nós

Uma implantação em hiperescala armazena as linhas da tabela em diferentes nós com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca como os dados são fragmentados entre nós.

Vamos definir a coluna de distribuição como site\_id, a chave de fragmentação. No psql, execute estas funções:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora, nosso grupo de servidores deve estar pronto para ingerir alguns dados. Podemos executar o seguinte localmente da nossa conexão `psql` para inserir dados continuamente.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

A consulta adiciona uma linha aproximadamente a cada quarto de segundo. As linhas são armazenadas em diferentes nós de trabalho, conforme direcionado pela coluna de distribuição, `site_id`.

   > [!NOTE]
   > Deixe a consulta de geração de dados em execução e abra uma segunda conexão do psql para os comandos restantes neste tutorial.
   >

## <a name="query"></a>Consultar

A opção de hospedagem em hiperescala permite que vários nós processem as consultas em paralelo para aumentar a velocidade. Por exemplo, o banco de dados calcula agregações como SUM e COUNT em nós de trabalho e combina os resultados em uma resposta final.

Aqui está uma consulta para contagem de solicitações da Web por minuto, juntamente com algumas estatísticas.
Tente executá-la no psql e observe os resultados.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>Executar rollups

A consulta acima funciona bem nos estágios iniciais, mas o desempenho será reduzido conforme os dados são escalados. Até mesmo com o processamento distribuído, é mais rápido pré-computar esses dados do que recalculá-los repetidamente.

Podemos garantir que nosso painel permaneça rápido, acumulando regularmente os dados brutos em uma tabela de agregação. Nesse caso, nós acumularemos em nossa tabela de agregação de um minuto, mas também é possível ter agregações de 5 minutos, 15 minutos, uma hora e assim por diante.

Já que iremos executar continuamente esse acúmulo, vamos criar uma função para executá-lo. Execute estes comandos no psql para criar a função `rollup_http_request`.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Com nossa função no local, execute-a para acumular os dados:

```sql
SELECT rollup_http_request();
```

E com nossos dados em um formulário pré-agregado, é possível consultar a tabela de rollup para obter o mesmo relatório anterior. Execute o seguinte:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expirar dados antigos

Os rollups realizam consultas mais rapidamente, porém ainda é necessário expirar os dados antigos para evitar custos de armazenamento não associados. Basta decidir por quanto tempo você quer manter os dados para cada granularidade e usar consultas padrão para excluir dados expirados. No exemplo a seguir, decidimos manter os dados brutos de um dia e agregações por minuto de um mês:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

Na produção, é possível encapsular essas consultas em uma função e chamá-la a cada minuto em um trabalho de Cron.

## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão *Excluir* na página *Visão geral* do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *Excluir* final.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados. Você aprendeu a consultar os dados em forma bruta, agregar regularmente esses dados, consultar as tabelas agregadas e expirar os dados antigos.

A seguir, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)