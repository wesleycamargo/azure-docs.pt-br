---
title: Usar um Open Service Broker para o banco de dados do Azure com um aplicativo no Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Explica como configurar um aplicativo do Pivotal Cloud Foundry para usar um Open Service Broker para o banco de dados do Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker para Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258848"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Tutorial: Usar um Open Service Broker para o banco de dados do Azure com um aplicativo no Pivotal Cloud Foundry

O uso do Open Service Broker para Azure com uma instância do Pivotal Cloud Foundry permite provisionar serviços, como bancos de dados, no Azure diretamente por meio da CLI do Cloud Foundry e da instância do Pivotal Cloud Foundry. Você também pode associar esses serviços a um aplicativo em execução na instância do Pivotal Cloud Foundry. Quando você associa um aplicativo a um serviço dessa maneira, você não precisa atualizar nenhum código nem a configuração no aplicativo. Este artigo explica como usar um Open Service Broker para o serviço do Azure para um banco de dados com um aplicativo no Pivotal Cloud Foundry.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Preparar seu espaço de aplicativo no Pivotal Cloud Foundry.
> * Clonar o código-fonte de um aplicativo de exemplo do GitHub.
> * Preparar o aplicativo para implantação.
> * Implante o aplicativo.
> * Criar um banco de dados usando o Open Service Broker para Azure.
> * Associar o banco de dados ao aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você precisará:

* [Ter o Pivotal Cloud Foundry instalado e configurado](create-cloud-foundry-on-azure.md)
* [Ter o Open Service Broker para Azure instalado e configurado](https://network.pivotal.io/products/azure-open-service-broker-pcf) com a instância do Cloud Foundry

Este é um exemplo da tela do Pivotal Cloud Foundry Ops Manager com o bloco do Open Service Broker para Azure instalado e configurado:

![Pivotal Cloud Foundry com o Open Service Broker para Azure instalado](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Preparar seu espaço de aplicativo no Pivotal Cloud Foundry

Para implantar seu aplicativo na instância do Pivotal Cloud Foundry, você precisará estar conectado com a ferramenta de linha de comando `cf`. Você também precisará definir como destino a organização e o espaço desejados.

Para verificar se você está conectado e exibir o espaço de destino, use `cf target`. O exemplo abaixo mostra o usuário já conectado como *admin*, usando a organização *myorg* e o definindo o espaço *dev* como destino:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Para entrar, use `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Para criar uma organização e um espaço, use `cf create-org` e `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Para definir um espaço como destino, use `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Obter o código de aplicativo

Para que um aplicativo use o Open Service Broker para Azure, o aplicativo precisa usar uma fonte de dados, como um banco de dados. Neste artigo, usaremos o [aplicativo de exemplo Spring Music do Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar um aplicativo que usa uma fonte de dados.

Clone o aplicativo do GitHub e navegue para seu diretório:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Para exibir as fontes de dados desse aplicativo, abra o arquivo [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Preparar o aplicativo para implantação

Antes de implantar o aplicativo na instância do Pivotal Cloud Foundry, você precisará criá-lo. Para fins de demonstração, também habilitamos um log de *DEPURAÇÃO* que registrará as informações de conexão da fonte de dados.

Para habilitar o log de *DEPURAÇÃO* para obter detalhes de conexão de banco de dados, adicione a propriedade yaml abaixo ao final de *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

O aplicativo de exemplo usa o Gradle para criar o aplicativo em um jar executável do Spring Boot. O jar executável será implantado na instância do Pivotal Cloud Foundry. Para criar o aplicativo:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Implantar seu aplicativo

Use o comando `cf push` para implantar o aplicativo na instância do Pivotal Cloud Foundry:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Copie o valor de *rotas* exibido na saída de `cf push`. A rota é a URL que você usará para acessar o aplicativo em execução. Por exemplo: 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Depois que o aplicativo for implantado, você poderá exibir os logs do aplicativo para ver a URL de conexão usada pelo aplicativo. O comando abaixo exibe os logs do aplicativo e usa `grep` para pesquisar a configuração *jdbcUrl*.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Observe que o aplicativo está usando *h2:mem:testdb*, que é o banco de dados em memória. Um aplicativo do Spring é automaticamente configurado para usar um banco de dados em memória quando uma dependência do banco de dados em memória está no classpath e a [configuração automática](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) está habilitada. O aplicativo de exemplo tem a [dependência do banco de dados em memória h2 configurada](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) e a configuração automática habilitada em [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Use a rota do aplicativo para navegar para ele em um navegador. A rota, ou a URL, é exibida na saída do comando `cf push`.

> [!TIP]
> Você também pode exibir a URL do aplicativo executando `cf apps`.

Depois de navegar para o aplicativo usando o navegador, interaja com ele excluindo alguns álbuns existentes e criando outros. O aplicativo de exemplo está usando o banco de dados em memória para salvar as alterações. Você também observará que o aplicativo foi populado com alguns [dados padrão](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Aplicativo Spring Music com os dados padrão](media/music-app.png)

Como o aplicativo está usando um banco de dados em memória, as alterações não serão persistidas se o aplicativo for reiniciado ou reimplantado. Para mostrar que as alterações não são persistentes, depois de fazer algumas alterações, prepare o aplicativo novamente usando `cf restage`:

```cmd
cf restage spring-music
```

Depois que o aplicativo for preparado novamente, navegue para ele em um navegador usando a mesma URL. Observe que as alterações feitas desapareceram e os dados padrão são exibidos.

![Aplicativo Spring Music com os dados padrão](media/music-app.png)

## <a name="create-a-database"></a>Criar um banco de dados

Para criar um banco de dados persistente no Azure usando o Open Service Broker, use o comando `cf create-service`. O comando abaixo provisiona um banco de dados PostgreSQL no Azure, no grupo de recursos *MyResourceGroup* na região *eastus*. Mais informações sobre *resourceGroup*, *location* e outros parâmetros JSON específicos do Azure estão disponíveis na [documentação de referência de módulo do PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

O banco de dados é exposto na instância do Pivotal Cloud Foundry como um serviço chamado *mypgsql*. Depois que o banco de dados concluir o provisionamento, que deverá levar alguns minutos, você poderá associá-lo ao seu aplicativo. Para verificar se o banco de dados concluiu o provisionamento, use o comando `cf services`:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

O valor da *última operação* do serviço será *criado com êxito* quando ele concluir o provisionamento.

## <a name="bind-the-database-to-your-application"></a>Associar o banco de dados ao seu aplicativo

Use o comando `bind-service` para associar o serviço ao seu aplicativo.

```cmd
cf bind-service spring-music mypgsql
```

Depois de associar o serviço ao seu aplicativo, você precisará preparar o aplicativo novamente para que as alterações entrem em vigor.

```cmd
cf restage spring-music
```

O aplicativo está [configurado para usar o Spring Cloud Connectors](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), que permite que a instância do Pivotal Cloud Foundry use a [reconfiguração automática](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) na fonte de dados do aplicativo. Nesse caso, a instância do Pivotal Cloud Foundry reconfigurará automaticamente o aplicativo para que ele use um serviço ao qual está associado para uma fonte de dados quando o aplicativo for preparado novamente.

Depois o aplicativo for preparado novamente, ele usará *mypgsql* para armazenar dados, em vez do banco de dados em memória.

As alterações feitas agora serão persistidas entre reinicializações e reimplantações. Veja também a URL de conexão usada pelo aplicativo exibindo os logs do aplicativo novamente.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Observe que há duas entradas:

* O valor original de *h2:mem:testdb*.
* O valor atualizado do banco de dados PostgreSQL quando o aplicativo foi preparado novamente.

Para verificar se os dados estão sendo persistidos no banco de dados PostgreSQL, navegue para o aplicativo no navegador, faça algumas alterações e, em seguida, prepare o aplicativo novamente:

```cmd
cf restage spring-music
```

Depois que o aplicativo for preparado novamente, navegue para ele em um navegador usando a mesma URL. Observe que as alterações feitas ainda estão presentes.

## <a name="cleanup"></a>Limpeza

Caso deseje desconectar seu aplicativo do banco de dados, desassocie-o usando o comando `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

Semelhante à associação do aplicativo a um serviço, é necessário preparar o aplicativo novamente para que essas alterações entrem em vigor. Essa ação deixará *mypgsql* e o aplicativo intactos, mas o aplicativo começará a usar o banco de dados em memória em vez de *mypgsql*.

Para excluir o banco de dados, use o comando `cf delete-service`. *Não é possível desfazer essa ação; portanto, tenha certeza de que deseja excluir o banco de dados antes de continuar.*

```cmd
cf delete-service mypgsql
```

Para remover o aplicativo da instância do Pivotal Cloud Foundry:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Próximas etapas

Este tutorial abordou a implantação de um aplicativo no Pivotal Cloud Foundry, bem como a criação de um banco de dados usando o Open Service Broker para Azure. Também abordou a associação do banco de dados ao seu aplicativo na instância do Pivotal Cloud Foundry. Para obter mais informações sobre como implantar aplicativos no Cloud Foundry no Azure, confira:

* [Cloud Foundry no Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Implantar seu primeiro aplicativo no Cloud Foundry no Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)