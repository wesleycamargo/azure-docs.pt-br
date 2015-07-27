<properties 
	pageTitle="Monitorar dependências, exceções e tempos de execução em aplicativos Web Java" 
	description="Monitoramento estendido do seu site Java com o Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Monitorar dependências, exceções e tempos de execução em aplicativos Web Java

*O Application Insights está em modo de visualização.*

Se você [instrumentou seu aplicativo Web Java com o Application Insights][java], será possível usar o Agente Java para obter percepções mais aprofundadas, sem nenhuma mudança de código:

* **Dependências remotas:** dados sobre chamadas que seu aplicativo faz por meio de um driver [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), como MySQL, SQL Server, PostgreSQL ou SQLite.
* **Exceções capturadas:** dados sobre exceções que são tratadas pelo seu código.
* **Tempo de execução do método:** dados sobre o tempo necessário para executar métodos específicos.

Para usar o agente Java, instale-o no servidor. Seus aplicativos Web devem ser instrumentados com o [SDK do Java do Application Insights][java].

## Instalar o agente do Application Insights para Java

1. No computador que está executando o servidor Java, [baixe o agente](http://go.microsoft.com/fwlink/?LinkId=618633).
2. Edite o script de inicialização do servidor de aplicativos e adicione a seguinte JVM:

    `javaagent:`*caminho completo para o arquivo JAR do agente*

    Por exemplo, no Tomcat em um computador Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie o servidor de aplicativos.

## Configurar o agente

Crie um arquivo chamado `AI-Agent.xml` e coloque-o na mesma pasta que o arquivo JAR do agente.

Defina o conteúdo do arquivo xml. Edite o exemplo a seguir para incluir ou omitir os recursos desejados.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Você precisa habilitar a exceção de relatórios e o tempo de método para métodos individuais.

Por padrão, `reportExecutionTime` é true, `reportCaughtExceptions` é false.

## Exibir os dados

No recurso Application Insights, a dependência remota e os tempos de execução do método agregados aparecerão [no bloco Desempenho][metrics].

Para procurar instâncias individuais de dependência, exceções e relatórios de método, abra [Pesquisa][diagnostic].

## Perguntas? Problemas?

[Solucionar problemas de Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->