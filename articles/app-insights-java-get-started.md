<properties 
	pageTitle="Introdução ao Application Insights em um projeto Web Java" 
	description="Monitorar o desempenho e o uso de seu site Java com o Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>
 
# Introdução ao Application Insights em um projeto Web Java

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Adicionando o Visual Studio Application Insights ao seu projeto, você pode detectar e diagnosticar problemas de desempenho e exceções.


![dados de exemplo](./media/app-insights-java-track-http-requests/5-results.png)

Além disso, você pode configurar [testes da Web][availability] para monitorar a disponibilidade do aplicativo e inserir [código em suas páginas da Web][track] para compreender os padrões de uso.

Você precisará de:

* Oracle JRE 1.6 ou posterior, ou então JRE Zulu 1.6 ou posterior
* Uma assinatura do [Microsoft Azure](http://azure.microsoft.com/). \(Você pode começar com a [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).\)


## 1\. Obter uma chave de instrumentação do Application Insights

1. Fazer logon no [Portal do Microsoft Azure](https://portal.azure.com)
2. Criar um novo recurso do Application Insights

    ![Clique em + e escolha Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Defina o tipo de aplicativo para aplicativo Web Java.

    ![Preencha um nome, escolha o aplicativo Java da Web e clique em Criar](./media/app-insights-java-get-started/02-create.png)
4. Localize a chave de instrumentação do novo recurso. Você precisará colar isto no código de seu projeto em breve.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/app-insights-java-get-started/03-key.png)

## 2\. Adicionar o SDK do Application Insights para Java a seu projeto

*Escolha o modo apropriado para seu projeto.*

#### Se você estiver criando um projeto Web dinâmico no Eclipse...

Use o [plug-in SDK do Application Insights para Java][eclipse].

#### Se você estiver usando o Maven...

Se o seu projeto já estiver configurado para usar o Maven para compilação, realize a mesclagem do trecho de código a seguir ao seu arquivo pom.xml.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[0.9,)</version>
      </dependency>
    </dependencies>


* *Erros de validação de soma de verificação ou compilação? Tente usar uma versão específica:* `<version>0.9.3</version>`

#### Se você estiver usando o Gradle...

Se o seu projeto já estiver configurado para usar o Gradle para compilação, realize a mesclagem do trecho de código a seguir ao seu arquivo build.gradle.

Em seguida, atualize as dependências do projeto para obter os binários baixados.

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '0.9.+'
      // or applicationinsights-core for bare API
    }

* *Erros de validação de soma de verificação ou compilação? Tente usar uma versão específica:* `version:'0.9.3'`

#### Caso contrário...

Adicione manualmente o SDK:

1. Baixe as [Bibliotecas do Azure para Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html)
2. Extraia os binários a seguir do arquivo zip e adicione-os ao seu projeto:
 * applicationinsights-core
 * applicationinsights-web
 * commons-codec
 * commons-io
 * commons-lang
 * commons-logging
 * guava
 * httpclient
 * httpcore
 * jsr305


*Qual é a relação entre os componentes `-core` e `-web`?*

`applicationinsights-core` fornece a você a API vazia, sem nenhuma telemetria automática. `applicationinsights-web` fornece métricas acompanhando tempos de resposta e contagens de solicitações HTTP.


## 3\. Adicione um arquivo xml do Application Insights

Adicione ApplicationInsights.xml à pasta de recursos em seu projeto. Copie o XML a seguir para dentro dela.

Substitua a chave de instrumentação que você obteve no Portal do Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* A chave de instrumentação é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.
* O componente de solicitação HTTP é opcional. Ele envia automaticamente a telemetria sobre solicitações e tempos de resposta para o portal.
* A correlação de eventos é uma adição ao componente de solicitação HTTP. Ele atribui um identificador a cada solicitação recebida pelo servidor e adiciona isso como uma propriedade para cada item de telemetria, como a propriedade “Operation.Id”. Ele permite que você correlacione a telemetria associada com cada solicitação, definindo um filtro na [pesquisa de diagnóstico][diagnostic].

## 4\. Adicionar um filtro HTTP

A última etapa de configuração permite que o componente de solicitação HTTP registre cada solicitação da Web. \(Não obrigatório se você quiser apenas a API vazia.\)

Localize e abra o arquivo web.xml em seu projeto, então mescle o trecho de código a seguir sob o nó do aplicativo Web, no qual seus filtros de aplicativo estão configurados.

Para obter os resultados mais precisos, o filtro deve ser mapeado antes de todos os outros filtros.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

#### Se você estiver usando o MVC 3.1 ou posterior

Edite esses elementos para incluir o pacote do Application Insights:

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>

#### Se você estiver usando o Struts 2

Adicione este item ao arquivo de configuração do Struts \(geralmente chamado de struts.xml ou struts-default.xml\):

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />

\(Se você tiver interceptores definidos em uma pilha padrão, o interceptador pode simplesmente ser adicionado àquela pilha.\)

## 5\. Exibir sua telemetria no Application Insights

Execute seu aplicativo.

Retorne para seu recurso Application Insights no Microsoft Azure.

Dados de solicitações HTTP aparecerão na folha de visão geral. \(Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.\)

![dados de exemplo](./media/app-insights-java-track-http-requests/5-results.png)
 

Clique em qualquer gráfico para ver métricas mais detalhadas.

![](./media/app-insights-java-track-http-requests/6-barchart.png)

 

Ao exibir as propriedades de uma solicitação, você pode ver os eventos de telemetria associados a ela, como solicitações e exceções.
 
![](./media/app-insights-java-track-http-requests/7-instance.png)



[Saiba mais sobre métricas.][metrics]

#### Cálculo de nome de endereço inteligente

O Application Insights presume que o formato de solicitações HTTP para aplicativos MVC é: `VERB controller/action`


Por exemplo, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` e `GET Home/Product/sdf96vws` serão agrupados em `GET Home/Product`.

Isso permite agregações significativas de solicitações, como o número de solicitações e o tempo médio de execução para solicitações.

## 5\. Contadores de desempenho

Clique no bloco Servidores e você verá uma variedade de contadores de desempenho.


![](./media/app-insights-java-get-started/11-perf-counters.png)

### Personalizando a coleta do contador de desempenho

Para desabilitar a coleta do conjunto padrão de contadores de desempenho, adicione o seguinte trecho sob o nó raiz do arquivo ApplicationInsights.xml:

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>

### Coleta de contadores de desempenho adicionais

Você pode especificar contadores de desempenho adicionais a serem coletados.

#### Contadores JMX \(expostos pela Máquina Virtual Java\)

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>

*	`displayName` – o nome exibido no portal do Application Insights.
*	`objectName` – o nome do objeto JMX.
*	`attribute` – o atributo do nome do objeto JMX a buscar
*	`type` \(opcional\) - o tipo do atributo do objeto JMX:
 *	Padrão: um tipo simples como “int” ou “long”.
 *	`composite`: os dados do contador de desempenho estão no formato “Attribute.Data”
 *	`tabular`: os dados do contador de desempenho estão no formato de uma linha de tabela



#### Contadores de desempenho do Windows \(64 bits\) 

Cada [contador de desempenho do Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) é membro de uma categoria \(do mesmo modo que um campo é um membro de uma classe\). Categorias podem ser globais, ou podem ter instâncias numeradas ou nomeadas.

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>

*	displayName – o nome exibido no portal do Application Insights.
*	categoryName – a categoria de contador de desempenho \(objeto de desempenho\) com o qual este contador de desempenho está associado
*	counterName – o nome do contador de desempenho
*	instanceName – o nome da instância da categoria do contador de desempenho ou uma cadeia de caracteres vazia \(""\), se a categoria contém uma única instância. Se categoryName é o processo, e o contador de desempenho que você gostaria de coletar faz parte do processo atual da JVM em que seu aplicativo está sendo executado, especifique `"__SELF__"`.

Seus contadores de desempenho são visíveis como métricas personalizadas em [Metrics Explorer][metrics].

![](./media/app-insights-java-get-started/12-custom-perfs.png)


## 6\. Capturar rastreamentos de log

Você pode usar o Application Insights para fracionar e dividir logs de Log4J, Logback ou outras estruturas de registros. Você pode correlacionar os logs de solicitações HTTP e outras telemetrias. [Saiba como][javalogs].

## 7\. Enviar sua próprias telemetrias

Agora que você instalou o SDK, você pode usar a API para enviar sua próprias telemetrias.

* [Acompanhe eventos personalizados e métricas][track] para saber o que os usuários estão fazendo com seu aplicativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.


Além disso, você pode empregar mais recursos do Application Insights em seu aplicativo:

* [Adicione telemetria do cliente Web][usage] para monitorar modos de exibição de página e métricas de usuário básico.
* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.


## Perguntas? Problemas?

[Solucionar problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->