---
title: Criar um banco de dados multilocatário com o Banco de Dados do Azure para PostgreSQL – Tutorial de Hyperscale (Citus) (versão prévia)
description: Este tutorial mostra como criar, popular e consultar tabelas distribuídas em Hyperscale (Citus) do Banco de Dados do Azure para PostgreSQL (versão prévia).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079541"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutorial: criar um banco de dados multilocatário usando o Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (versão prévia)

Neste tutorial, você utiliza o Banco de Dados do Azure para PostgreSQL – Hyperscale (Citus) (versão prévia) para saber como:

> [!div class="checklist"]
> * Criar um grupo de servidores Hyperscale (Citus)
> * Usar o utilitário psql para criar um esquema
> * Fragmentar tabelas entre nós
> * Ingerir dados de exemplo
> * Consultar dados do locatário
> * Consultar dados entre locatários
> * Personalizar o esquema por locatário

## <a name="prerequisites"></a>Pré-requisitos

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
   - Nome de usuário administrador: insira um nome de usuário que será usado posteriormente para conectar o banco de dados.
   - Senha: deve ter pelo menos oito caracteres de comprimento e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 a 9) e caracteres não alfanuméricos (!, $, #, % etc.)
   - Localização: use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.

   > [!IMPORTANT]
   > O logon de administrador do servidor e a senha que você especificar aqui são necessários para fazer logon no servidor e em seus bancos de dados mais tarde neste tutorial. Lembre-se ou registre essas informações para o uso posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações nessa seção inalteradas e clique em **Salvar**.
6. Clique em **Examinar + criar** e, em seguida, **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.
7. A página será redirecionada para monitorar a implantação. Quando o status em tempo real mudar de **Sua implantação está em andamento** para **Sua implantação está concluída**, clique no item de menu **Saídas** no lado esquerdo da página.
8. A página de saídas conterá um nome do host do coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para uso posterior.

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

   ![Banco de Dados do Azure para PostgreSQL – Ícone do terminal do Azure Cloud Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. O Azure Cloud Shell é aberto no seu navegador, permitindo que você digite comandos de bash.

   ![Banco de Dados do Azure para PostgreSQL – Prompt de bash do Azure Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. No prompt do Cloud Shell, conecte-se ao Banco de Dados do Azure para servidor PostgreSQL usando os comandos psql. O formato a seguir é usado para conectar-se a um Banco de Dados do Azure para servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Por exemplo, o comando a seguir conecta o banco de dados padrão chamado **citus** no servidor PostgreSQL **mydemoserver.postgres.database.azure.com** usando as credenciais de acesso. Insira a senha de administrador do servidor quando solicitado.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Usar o utilitário psql para criar um esquema

Uma vez conectado ao Banco de Dados do Azure para PostgreSQL - Hyperscale (Citus) (versão prévia) usando o psql, é possível realizar algumas tarefas básicas. Este tutorial orienta você durante a criação de um aplicativo Web que permite aos anunciantes acompanharem suas campanhas.

Várias empresas podem usar o aplicativo, então, vamos criar uma tabela para armazenar as empresas e outra para as campanhas delas. No console do psql, execute estes comandos:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Cada campanha pagará para executar anúncios. Também adicione uma tabela para anúncios, executando o seguinte código no psql após o código acima:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Por fim, vamos rastrear as estatísticas sobre cliques e impressões para cada anúncio:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Agora é possível ver as tabelas recém-criadas na lista de tabelas com este comando do psql:

```postgres
\dt
```

Aplicativos multilocatários podem impor exclusividade apenas por locatário, por isso, todas as chaves primárias e estrangeiras incluem a ID da empresa.

## <a name="shard-tables-across-nodes"></a>Fragmentar tabelas entre nós

Uma implantação em hiperescala armazena as linhas da tabela em diferentes nós com base no valor de uma coluna designada pelo usuário. Essa "coluna de distribuição" marca qual locatário possui quais linhas.

Vamos definir a coluna de distribuição como empresa\_id, o identificador do locatário. No psql, execute estas funções:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Agora, fora do psql, na linha de comando normal, baixe conjuntos de dados de exemplo:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Novamente dentro do psql, carregue em massa os dados. Certifique-se de executar o psql no mesmo diretório em que você baixou os arquivos de dados.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Esses dados agora serão espalhados entre nós de trabalho.

## <a name="query-tenant-data"></a>Consultar dados do locatário

Quando o aplicativo solicita dados para um locatário único, o banco de dados pode executar a consulta em um nó de trabalho único. Consultas de locatário único filtram por uma ID de locatário único. Por exemplo, a consulta a seguir filtra `company_id = 5` para anúncios e impressões. Tente executá-la no psql para ver os resultados.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Consultar dados entre locatários

Até agora todas as tabelas foram distribuídas por `company_id`, mas alguns dados não "pertencem" naturalmente a qualquer locatário em particular e podem ser compartilhados. Por exemplo, todas as empresas na plataforma de anúncios de exemplo talvez queiram obter informações geográficas do respectivo público-alvo com base em endereços IP.

Crie uma tabela para armazenar informações geográficas compartilhadas. Execute isso no psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Em seguida, faça com `geo_ips` uma "tabela de referência" para armazenar uma cópia da tabela em cada nó de trabalho.

```sql
SELECT create_reference_table('geo_ips');
```

Carregue-a com dados de exemplo. Lembre-se de executar isso em psql de dentro do diretório em que o conjunto de dados foi baixado.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Unir a tabela de cliques com geo\_ips é eficiente em todos os nós.
Aqui está uma junção para encontrar as localizações de todos aqueles que clicaram no anúncio
290. Tente executar a consulta no psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalizar o esquema por locatário

Cada locatário pode precisar armazenar informações especiais não necessárias para outras pessoas. No entanto, todos os locatários compartilham uma infraestrutura comum com um esquema de banco de dados idêntico. Para onde os dados extras podem ir?

Um truque é usar um tipo de coluna aberta como JSONB do PostgreSQL.  Nosso esquema tem um campo JSONB `clicks` chamado `user_data`.
Uma empresa (digamos empresa cinco), pode usar a coluna para rastrear se o usuário está em um dispositivo móvel.

Aqui está uma consulta para descobrir quem clica mais: visitantes móveis ou tradicionais.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Podemos otimizar essa consulta para uma única empresa criando um [índice parcial](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

De modo mais geral, é possível criar [índices GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) em cada chave e valor dentro da coluna.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Limpar recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão *Excluir* na página *Visão geral* do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *Excluir* final.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um grupo de servidores Hyperscale (Citus). Você conectou ele com o psql, criou um esquema e distribuiu dados. Você aprendeu a consultar dados de dentro e entre locatários, além de personalizar o esquema por locatário.

A seguir, saiba mais sobre os conceitos de hiperescala.
> [!div class="nextstepaction"]
> [Tipos de nó de hiperescala](https://aka.ms/hyperscale-concepts)
