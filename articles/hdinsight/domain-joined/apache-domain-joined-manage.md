---
title: "Gerenciar clusters do HDInsight ingressados no domínio – Azure | Microsoft Docs"
description: "Saiba como gerenciar clusters do HDInsight Ingressado no Domínio"
services: hdinsight
documentationcenter: 
author: bprakash
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2018
ms.author: bhanupr
ms.openlocfilehash: 68166be98acc64326a4053b45f0039ae54d930e4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Gerenciar clusters HDInsight ingressados no domínio
Conheça os usuários e as funções do HDInsight Ingressado no Domínio e como gerenciar seus clusters.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acesse os clusters com o Pacote de Segurança Enterprise.

O Pacote de Segurança Enterprise (anteriormente conhecido como HDInsight Premium) fornece acesso de vários usuário ao cluster, onde a autenticação é feita pelo Active Directory e a autorização pelo Apache Ranger e ACLs de armazenamento (ACLs do ADLS). A autorização fornece limites de segurança entre vários usuários e permite que somente usuários privilegiados tenham acesso aos dados com base nas políticas de autorização.

Segurança e isolamento de usuários são importantes para um cluster de HDInsight com o Pacote de Segurança Enterprise. Para atender a esses requisitos, o acesso do SSH ao cluster com o Pacote de Segurança Enterprise é bloqueado. A tabela a seguir mostra os métodos de acesso recomendados para cada tipo de cluster:

|Carga de trabalho|Cenário|Método de Acesso|
|--------|--------|-------------|
|O Hadoop|Hive – trabalhos/consultas interativas |<ul><li>[Beeline](#beeline)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Trabalhos/consultas interativas, PySpark interativo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin com Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Cenários em lote – envio de Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Consulta Interativa (LLAP)|Interativo|<ul><li>[Beeline](#beeline)</li><li>[Exibição de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC – Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Ferramentas do Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Qualquer|Instalar Aplicativo Personalizado|<ul><li>[Ações de Script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|


Usar as APIs padrão ajuda da perspectiva de segurança. Além disso, você obtém os benefícios a seguir:

1.  **Gerenciamento** – você pode gerenciar seu código e automatizar trabalhos usando APIs padrão – Livy, HS2 etc.
2.  **Auditoria** – com SSH, não é possível auditar quais usuários o SSH tinha para o cluster. Esse seria o caso em que os trabalhos seriam criados por meio de pontos de extremidade padrão conforme eles fossem executados no contexto do usuário. 



### <a name="beeline"></a>Usar o Beeline 
Instale o Beeline no seu computador e conecte-se pela internet pública, use os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://&lt;clustername&gt;.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Se você tiver Beeline instalado localmente e conectar-se pela Rede Virtual do Azure, use os seguintes parâmetros: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento Gerenciar HDInsight usando a API de REST do Ambari.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Usuários dos clusters do HDInsight Ingressado no Domínio
Um cluster do HDInsight que não for ingressado no domínio terá duas contas de usuário que serão criadas durante a elaboração do cluster:

* **Administração do Ambari**: essa conta também é conhecida como *usuário do Hadoop* ou *usuário HTTP*. Essa conta pode ser usada para fazer logon no Ambari em https://&lt;clustername>.azurehdinsight.net. Ela também pode ser usada para executar consultas nas exibições do Ambari, para executar trabalhos por meio de ferramentas externas (por exemplo, PowerShell, Templeton, Visual Studio) e para autenticar com o driver ODBC do Hive e com ferramentas de BI (por exemplo, Excel, PowerBI ou Tableau).

Um cluster do HDInsight ingressado no domínio tem três novos usuários, além da Administração do Ambari.

* **Administração do Ranger**: essa é a conta de administrador local do Apache Ranger. Ela não é um usuário do Domínio do Active Directory. Essa conta pode ser usada para configurar políticas e tornar outros usuários administradores ou administradores delegados (de modo que os usuários possam gerenciar políticas). Por padrão, o nome de usuário é *administrador* e a senha é a mesma que a senha de administrador do Ambari. A senha pode ser atualizada da página Configurações no Ranger.
* **Usuário de domínio do administrador do cluster**: essa conta é um usuário do Domínio do Active Directory designado como o administrador do cluster do Hadoop, incluindo o Ambari e o Ranger. Você deve fornecer as credenciais do usuário durante a criação do cluster. Esse usuário tem os seguintes privilégios:

  * Adicionar computadores ao domínio e colocá-los na UO que você especificar durante a criação do cluster.
  * Criar entidades de serviço na UO que você especificar durante a criação do cluster.
  * Criar entradas de DNS reverso.

    Observe que os usuários do AD também têm esses privilégios.

    Há alguns pontos de extremidade do cluster (por exemplo, Templeton) que não são gerenciados pelo Ranger e, portanto, não são seguros. Esses pontos de extremidade estão bloqueados para todos os usuários, exceto para o usuário de domínio do administrador do cluster.
* **Regular**: durante a criação do cluster, você poderá fornecer vários grupos do Active Directory. Os usuários nesses grupos são sincronizados ao Ranger e ao Ambari. Esses usuários são usuários de domínio e têm acesso apenas aos pontos de extremidade gerenciados pelo Ranger (por exemplo, Hiveserver2). Todas as políticas de RBAC e a auditoria serão aplicáveis a esses usuários.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Funções dos clusters do HDInsight Ingressado no Domínio
O HDInsight Ingressado no Domínio tem as seguintes funções:

* Administrador do cluster
* Operador do cluster
* Administrador de serviços
* Operador de serviço
* Usuário do cluster

**Para ver as permissões dessas funções**

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Funções**.
3. Clique no ponto de interrogação azul para ver as permissões:

    ![Permissões das funções do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abra a interface do usuário do Ambari Management

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Apra seu cluster HDInsight. Confira [Listar e mostrar clusters](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Clique em **Painel** no menu superior para abrir o Ambari.
4. Faça logon no Ambari usando o nome de usuário e a senha de domínio de administrador do cluster.
5. Clique no menu suspenso **Administrador** no canto superior direito e, em seguida, clique em **Gerenciar o Ambari**.

    ![Ambari gerenciado pelo HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    A interface do usuário é semelhante a:

    ![Interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Listar os usuários de domínio sincronizados do Active Directory
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Usuários**. Você deverá ver todos os usuários sincronizados do seu Active Directory com o cluster do HDInsight.

    ![Usuários listados na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Listar os grupos de domínio sincronizados do Active Directory
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Grupos**. Você deverá ver todos os grupos sincronizados do seu Active Directory no cluster do HDInsight.

    ![Grupos listados na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configurar permissões de exibições do Hive
1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Exibições**.
3. Clique em **HIVE** para mostrar os detalhes.

    ![Exibições do Hive na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Clique no link **Exibição do Hive** para configurar as exibições do Hive.
5. Role para baixo até a seção **Permissões**.

    ![Permissões de configuração das exibições do Hive na interface do usuário de gerenciamento do Ambari do HDInsight Ingressado no Domínio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Clique em **Adicionar Usuário** ou **Adicionar Grupo** e, em seguida, especifique os usuários ou grupos que podem usar as exibições do Hive.

## <a name="configure-users-for-the-roles"></a>Configurar usuários para as funções
 Para ver uma lista de funções e suas permissões, confira [Roles of Domain-joined HDInsight clusters](#roles-of-domain---joined-hdinsight-clusters) (Funções dos clusters do HDInsight Ingressado no Domínio).

1. Abra a interface do usuário do Ambari Management.  Confira [Open the Ambari Management UI](#open-the-ambari-management-ui) (Abrir a interface do usuário do Ambari Management).
2. No menu à esquerda, clique em **Funções**.
3. Clique em **Adicionar Usuário** ou **Adicionar Grupo** para atribuir usuários e grupos a funções diferentes.

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster do HDInsight ingressado no domínio, confira [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configurar clusters do HDInsight Ingressado no Domínio).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](apache-domain-joined-run-hive.md).
