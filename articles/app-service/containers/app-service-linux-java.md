---
title: Suporte para linguagem Java para o Serviço de Aplicativo do Azure no Linux | Microsoft Docs
description: Guia do desenvolvedor para implantar aplicativos Java com o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, aplicativo Web, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: e11b115d7a6421c34e7f1371ad8931b6affa0436
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815164"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guia do desenvolvedor Java para o Serviço de Aplicativo no Linux

O Serviço de Aplicativo do Azure no Linux permite que desenvolvedores de Java criem, implantem e escalonem rapidamente os aplicativos Web empacotados do Tomcat ou do Java Standard Edition (SE) em um serviço baseado em Linux totalmente gerenciado. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia mostra os principais conceitos e instruções para desenvolvedores de Java que usam o Serviço de Aplicativo para Linux. Se você nunca tiver usado o Serviço de Aplicativo do Azure para Linux, leia o [Início rápido do Java](quickstart-java.md) primeiro. Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Confira a [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](/azure/app-service/app-service-diagnostics) para saber mais sobre como acessar e usar essas ferramentas de diagnóstico.

### <a name="ssh-console-access"></a>Acesso ao console SSH 

A conectividade do SSH com o ambiente Linux em execução no aplicativo está disponível. Confira [Suporte do SSH para o Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers/app-service-linux-ssh-support) para ver instruções completas sobre como se conectar com o sistema do Linux por meio do navegador da Web ou terminal local.

### <a name="streaming-logs"></a>Logs de streaming

Para depurar e solucionar problemas de maneira rápida, é possível transmitir logs para o console usando a CLI do Azure. Configure a CLI com o `az webapp log config` para habilitar o registro em log:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Em seguida, transmita os logs para o console usando `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Para saber mais, confira [Transmitir logs com a CLI do Azure](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs.

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Personalização e ajuste

O Serviço de Aplicativo do Azure para Linux é compatível com o ajuste e a personalização de caixas por meio do portal e da CLI do Azure. Examine os seguintes artigos para saber mais sobre a configuração de aplicativos Web específica para não Java:

- [Definir configurações do Serviço de Aplicativo](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurar um domínio personalizado](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Habilitar SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Adicionar uma CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Definir opções de tempo de execução do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM nos ambientes do Tomcat e do Java SE, defina JAVA_OPTS conforme mostrado abaixo como uma [configuração de aplicativo](/azure/app-service/web-sites-configure#app-settings). O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o tempo de execução do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `$JAVA_OPTS -Xms512m -Xmx1204m`.

Para definir a configuração de aplicativo do plug-in do Maven do Serviço de Aplicativo do Azure no Linux, adicione marcas configuração/valor à seção de plug-in do Azure. O exemplo a seguir define um tamanho de heap de Java específico mínimo e máximo:

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1: -Xms1024m -Xmx1024m
- Instâncias B2 e S2: -Xms3072m -Xmx3072m
- Instâncias B3 e S3: -Xms6144m -Xmx6144m


Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

### <a name="turn-on-web-sockets"></a>Ativar os soquetes da Web

Ative o suporte para soquetes da Web no portal do Azure, nas **Configurações de aplicativo**. Será necessário reiniciar o aplicativo para a configuração entrar em vigor.

Ative o suporte para soquete da Web usando a CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Depois, reinicie o aplicativo:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Definir a codificação de caractere padrão 

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` com o valor `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Como alternativa, é possível definir a configuração do aplicativo usando o plug-in do Maven do Serviço de Aplicativo. Adicione as marcas de nome e valor de configuração à configuração do plug-in: 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-application"></a>Proteger o aplicativo

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos. 

### <a name="authenticate-users"></a>Autenticar usuários

Configure a autenticação de aplicativo no portal do Azure com a opção **Autenticação e autorização**. Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação.  Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) e os artigos relacionados para outros provedores de identidade.

Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

 Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [Associar um certificado SSL personalizado existente](/azure/app-service/app-service-web-tutorial-custom-ssl) para carregar um certificado SSL existente e associá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Conectar-se a fontes de dados

>[!NOTE]
> Se o aplicativo usar o Spring Framework ou o Spring Boot, será possível definir informações de conexão de banco de dados para o Spring Data JPA, como variáveis de ambiente (no arquivo de propriedades do aplicativo). Em seguida, use as [configurações do aplicativo](/azure/app-service/web-sites-configure#app-settings) para definir esses valores para o aplicativo no portal ou na CLI do Azure.

Para configurar o Tomcat para usar conexões gerenciadas com bancos de dados usando o JDBC (Java Database Connectivity) ou a JPA (API de Persistência do Java), primeiro, personalize a variável de ambiente CATALINA_OPTS lida pelo Tomcat na inicialização. Defina esses valores por meio de uma configuração de aplicativo no plug-in Maven do Serviço de Aplicativo:

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Ou uma configuração equivalente do Serviço de Aplicativo no portal do Azure.

Em seguida, determine se a fonte de dados precisa ser disponibilizada apenas para um aplicativo ou para todos os aplicativos em execução no Plano do Serviço de Aplicativo.

Para fontes de dados no nível do aplicativo: 

1. Adicione um arquivo `context.xml` se ele não existir no aplicativo Web e adicione-o ao diretório `META-INF` do arquivo WAR quanto o projeto estiver pronto.

2. Nesse arquivo, adicione uma entrada do caminho `Context` para vincular a fonte de dados a um endereço JNDI. O

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Atualize o `web.xml` do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

Para os recursos compartilhados no nível do servidor:

1. Copie o conteúdo de `/usr/local/tomcat/conf` para `/home/tomcat` na instância do Serviço de Aplicativo no Linux usando SSH se já não houver uma configuração.

2. Adicione o contexto ao `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Atualize o `web.xml` do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório `/home/tomcat/lib`. Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:  
    1. Instale a extensão de aplicativo Web do Serviço de Aplicativo do Azure:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta `/home/tomcat/lib`.

5. Reinicie o aplicativo Serviço de Aplicativo no Linux. O Tomcat redefinirá `CATALINA_HOME` como `/home/tomcat` e usará as classes e a configuração atualizadas.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o Zulu JDK com suporte do Azure nos contêineres, certifique-se de efetuar pull e usar as imagens pré-compiladas listadas na [página de download do Azul](https://www.azul.com/downloads/azure-only/zulu/#docker) ou usar `Dockerfile` exemplos do [repositório GitHub do Java da Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilidade de tempo de execução e declaração de suporte

O Serviço de Aplicativo para Linux dá suporte a dois tempos de execução para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](http://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de tempo de execução do Java SE para executar aplicativos empacotados como arquivos Java (JAR). A única versão principal com suporte é a Java 8.

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do tempo de execução do Java 

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/).

Atualizações de versão principal serão fornecidas por meio de novas opções de tempo de execução no Serviço de Aplicativo do Azure para Linux. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um tempo de execução do Java com suporte for desativado, os desenvolvedores do Azure que usam o tempo de execução afetado receberão um aviso de reprovação pelo menos seis meses antes de o tempo de execução ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

O suporte ao produto do [Azul Zulu JDK com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível durante o desenvolvimento para Azure ou [Microsoft Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de tempo de execução

Os desenvolvedores podem [abrir um problema](/azure/azure-supportability/how-to-create-azure-support-request) com o Azul Zulu JDK por meio do Suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.
