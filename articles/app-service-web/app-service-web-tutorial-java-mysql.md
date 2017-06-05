---
title: Compilar um aplicativo Web Java e MySQL no Azure | Microsoft Docs
description: "Saiba como fazer com que um aplicativo Java que se conecta ao serviço de banco de dados MySQL do Azure funcione no serviço de aplicativo do Azure."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Compilar um aplicativo Web Java e MySQL no Azure
Este tutorial mostra como criar um aplicativo Web Java no Azure que se conecta a um banco de dados MySQL. A primeira etapa é clonar um aplicativo em seu computador local e fazê-lo funcionar com uma instância do MySQL local.
A etapa seguinte é configurar serviços do Azure para o aplicativo Java e para o MySQL e, em seguida, implantar o aplicativo em um serviço de aplicativo do Azure.
Quando tiver terminado, você terá um aplicativo de lista de tarefas pendentes em execução no Azure e conectado ao serviço de banco de dados MySQL do Azure.

![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de executar este exemplo, instale localmente os seguintes pré-requisitos:

1. [Baixe e instale o git](https://git-scm.com/)
1. [Baixe e instale o Java 7 ou superior](http://Java.net/downloads.Java)
1. [Baixe e instale o Maven](https://maven.apache.org/download.cgi)
1. [Baixe, instale e inicie o MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Baixe e instale o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>Preparar o banco de dados MySQL local

Nesta etapa, você cria um banco de dados em seu servidor MySQL local para uso neste tutorial.

### <a name="connect-to-mysql-server"></a>Conectar ao servidor MySQL
Conecte-se ao servidor MySQL local na linha de comando:

```bash
mysql -u root -p
```

Se o comando for executado com êxito, o servidor MySQL já estará sendo executado. Caso contrário, veja se o servidor MySQL local foi iniciado seguindo as [Etapas pós-instalação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Se for solicitada uma senha, insira a senha da conta `root`. Caso não se lembre da senha de sua conta raiz, consulte [MySQL: como redefinir a senha raiz](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).


### <a name="create-a-database-and-table"></a>Criar um banco de dados e uma tabela

No prompt `mysql`, crie um banco de dados e uma tabela para os itens de tarefa.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

## <a name="create-local-java-application"></a>Criar um aplicativo Java local
Nesta etapa, você clonar um repositório GitHub, configurar a conexão de banco de dados MySQL e executa o aplicativo localmente. 

### <a name="clone-the-sample"></a>Clonar o exemplo

No prompt de comando, navegue até um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

Em seguida, configure lombok.jar seguindo as etapas no arquivo Leiame do repositório.


### <a name="configure-mysql-connection"></a>Configurar a conexão do MySQL

Esse aplicativo usa o plug-in Maven Jetty para executar o aplicativo localmente e conectar-se ao banco de dados MySQL.
Para habilitar o acesso à instância do MySQL local, defina a ID e a senha de usuário do MySQL local em WebContent/WEB-INF/jetty-env.xml.

Atualize os valores de usuário e senha com a senha e a ID de usuário da sua instância do MySQL local:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; Para obter informações sobre como o Jetty usa o arquivo `jetty-env.xml`, consulte a [Referência de XML do Jetty](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html).
&gt; &gt;

### <a name="run-the-sample"></a>Execute o exemplo

Use um comando Maven para executar o exemplo: 

```bash
mvn package jetty:run
```

Em seguida, navegue até `http://localhost:8080` em um navegador. Adicione algumas tarefas à página.

Para parar o aplicativo a qualquer momento, digite `Ctrl`+`C` no prompt de comando. 

## <a name="create-a-mysql-database-in-azure"></a>Criar um banco de dados MySQL no Azure

Nesta etapa, você cria um banco de dados MySQL no [Banco de dados do Azure para MySQL (versão prévia)](/azure/mysql). Posteriormente, você configurará seu aplicativo Java para se conectar a esse banco de dados.

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Use a CLI do Azure 2.0 em uma janela de terminal para criar os recursos necessários para hospedar seu aplicativo Java no serviço de aplicativo do Azure. Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual recursos do Azure, como aplicativos da Web, bancos de dados e contas de armazenamento são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos na região da Europa Setentrional:

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

Para ver os valores disponíveis para `--location`, use o comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Criar um servidor MySQL

Crie um servidor no Banco de Dados do Azure para MySQL (versão prévia) com o comando [az mysql server create](/cli/azure/mysql/server#create).

Substitua pelo nome do servidor MySQL exclusivo onde vir o espaço reservado `&lt;mysql_server_name&gt;`. Esse nome é parte do nome de host do servidor MySQL, `&lt;mysql_server_name&gt;.database.windows.net`, portanto, ele precisa ser globalmente exclusivo. Substitua também `&lt;admin_user&gt;` e `&lt;admin_password&gt;` por seus próprios valores.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

Quando o servidor MySQL for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>Configurar um firewall de servidor

Crie uma regra de firewall para o servidor MySQL para permitir conexões de cliente usando o comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; O Banco de Dados do Azure para MySQL (versão prévia) ainda não permite conexões somente de serviços do Azure. Como os endereços IP no Azure são atribuídos dinamicamente, é melhor habilitar todos os endereços IP por enquanto. Como o serviço está na fase de versão prévia, melhores métodos para proteger seu banco de dados serão habilitados em breve.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>Conectar ao servidor MySQL

Na janela do terminal, conecte-se ao servidor MySQL no Azure. Use o valor especificado anteriormente para `&lt;admin_user&gt;` e `&lt;mysql_server_name&gt;`.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Criar um banco de dados e uma tabela no Serviço MySQL do Azure

No prompt `mysql`, crie um banco de dados e uma tabela para os itens de tarefa.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>Criar um usuário com permissões

Crie um usuário do banco de dados e dê a ele todos os privilégios no banco de dados `todoItemDb`. Substitua os espaços reservados `&lt;Javaapp_user&gt;` e `&lt;Javaapp_password&gt;` por seu próprio nome exclusivo do aplicativo.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

Saia da conexão do servidor digitando `quit`.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>Configurar a conexão do MySQL local com o novo Serviço MySQL do Azure

Nesta etapa, você conecta seu aplicativo Java ao banco de dados MySQL que acabou de criar no Banco de Dados do Azure para MySQL (versão prévia). 

Para habilitar o acesso do aplicativo local ao serviço MySQL do Azure, defina seu novo ponto de extremidade MySQL, ID de usuário e senha em WebContent/WEB-INF/jetty-env.xml:

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

Salve suas alterações.

## <a name="test-the-application"></a>Testar o aplicativo

Use o mesmo comando Maven que antes para executar o exemplo localmente mais uma vez, mas desta vez se conectando ao serviço MySQL do Azure: 

```bash
mvn package jetty:run
```

Navegue até `http://localhost:8080` em um navegador. Se a página for carregada sem erros, seu aplicativo Java estará se conectando ao banco de dados MySQL no Azure. 

Você não deve ver “Adicionar algumas tarefas” na página.

Para parar o aplicativo a qualquer momento, digite `Ctrl`+`C` no terminal. 

### <a name="secure-sensitive-data"></a>Proteger dados confidenciais

Certifique-se de que os dados confidenciais em `WebContent/WEB-INF/jetty-env.xml` não sejam confirmados no Git.

Para fazer isso, abra `.gitignore` da raiz do repositório e adicione `WebContent/WEB-INF/jetty-env.xml` em uma nova linha. Salve suas alterações.

Confirme as alterações em `.gitignore`.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Implantar o aplicativo Java no Azure
Em seguida, implantamos o aplicativo Java em um serviço de aplicativo do Azure.

### <a name="create-an-appservice-plan"></a>Criar um plano do serviço de aplicativo

Crie um plano do serviço de aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

&gt; [!NOTE] 
&gt; Um plano do serviço de aplicativo representa a coleção de recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do serviço de aplicativo compartilham os recursos definidos por ele, permitindo que você economize hospedando vários aplicativos. 
&gt; &gt; Os planos do serviço de aplicativo definem: &gt; &gt; * Região (Europa Setentrional, Leste dos EUA, Sudeste Asiático) &gt; * Tamanho da instância (pequeno, médio, grande) &gt; * Contagem de escala (uma, duas ou três instâncias etc.) &gt; * SKU (Gratuito, Compartilhado, Básico, Standard e Premium) &gt; 

O exemplo a seguir cria um plano do serviço de aplicativo denominado `myAppServicePlan` usando o tipo de preço **GRATUITO**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o plano do serviço de aplicativo é criado, a CLI do Azure mostra informações semelhantes ao exemplo a seguir:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

Agora que um plano do serviço de aplicativo foi criado, crie um aplicativo Web do Azure no plano do serviço de aplicativo `myAppServicePlan`. O aplicativo Web fornece um espaço de hospedagem para implantar seu código e fornecer uma URL para exibir o aplicativo implantado. Use o comando [az appservice web create](/cli/azure/appservice/web#create) para criar o aplicativo Web. 

No seguinte comando, substitua o espaço reservado `&lt;app_name&gt;` por seu próprio nome de aplicativo exclusivo. O nome exclusivo será usado como a parte do nome de domínio padrão para o aplicativo Web, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Posteriormente, você poderá mapear qualquer entrada DNS personalizada para o aplicativo Web antes de expor para seus usuários. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Defina a versão do Java, o tipo do servidor de aplicativos Java e a versão do servidor de aplicativos

Defina a versão do Java, do Servidor de aplicativos Java (contêiner) e a versão do contêiner usando o comando [az appservice web config update](/cli/azure/appservice/web/config#update).

O comando a seguir define a versão do Java como 8, o Servidor de aplicativos Java como Jetty e a versão do Jetty como a mais recente, o Jetty 9.3.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>Obter credenciais para implantação no aplicativo Web usando FTP 

É possível implantar seu aplicativo no serviço de aplicativo do Azure de várias maneiras, incluindo FTP, Git local, GitHub, Visual Studio Team Services e BitBucket. Para este exemplo, usamos o Maven para compilar um arquivo .WAR e o FTP para implantar o arquivo .WAR no aplicativo Web

Para determinar as credenciais a serem passadas em um comando ftp para o aplicativo Web, use o comando [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) da seguinte forma: 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>Compilar o aplicativo local para implantar no aplicativo Web 

Para preparar o aplicativo Java local para ser executado no aplicativo Web do Azure, recompile todos os recursos no aplicativo Java em um único arquivo .WAR pronto para implantação. Navegue até o diretório em que o pom.xml do aplicativo está localizado e digite:

```bash 
mvn clean package
``` 
No final do processo do pacote Maven, você verá a localização do arquivo .WAR.  A saída deve ser semelhante a esta:

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

Tome nota da localização do arquivo .WAR e use seu método FTP favorito para implantar o arquivo .WAR na pasta WebApps do Jetty.  Observe que a pasta WebApps do Jetty está localizada em /site/wwwroot/webapps em um aplicativo Web do Azure. 

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure

Navegue até `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` e adicione algumas tarefas à lista. 

![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**Parabéns!** Você está executando um aplicativo Java controlado por dados no Serviço de Aplicativo do Azure.
Para atualizar o aplicativo, repita o comando de limpeza de pacote do Maven e reimplante o aplicativo via FTP.

## <a name="manage-your-azure-web-app"></a>Gerenciar seu aplicativo Web do Azure

Vá até o portal do Azure para ver o aplicativo Web criado entrando em [https://portal.azure.com](https://portal.azure.com).

No menu à esquerda, clique em **serviço de aplicativo** e, em seguida, clique no nome do seu aplicativo Web do Azure.

Agora, você deve estar na _folha_ de seu aplicativo Web (uma página do portal que abre horizontalmente).

Por padrão, a folha de seu aplicativo Web mostra a página **Visão Geral**. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da folha mostram as páginas de configuração diferentes que você pode abrir.

Na página **Configurações do Aplicativo**, 

![Configurações do aplicativo Web do serviço de aplicativo do Azure](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



Essas guias na folha mostram muitos recursos excelentes que você pode adicionar ao seu aplicativo Web. A lista a seguir fornece algumas possibilidades:

* mapear um nome DNS personalizado
* associar um certificado SSL personalizado
* configurar uma implantação contínua
* Escalar vertical e horizontalmente
* adicionar a autenticação do usuário

## <a name="more-resources"></a>Mais recursos

- [Mapear um nome DNS personalizado existente para aplicativos Web do Azure](app-service-web-tutorial-custom-domain.md)
- [Associar um certificado SSL personalizado existente a aplicativos Web do Azure](app-service-web-tutorial-custom-ssl.md)
- [Scripts da CLI de aplicativos Web](app-service-cli-samples.md)</PRE>

