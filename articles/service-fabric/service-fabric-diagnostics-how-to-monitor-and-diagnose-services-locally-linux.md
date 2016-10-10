<properties
   pageTitle="Monitorar e diagnosticar localmente serviços escritos com o Service Fabric do Azure | Microsoft Azure"
   description="Saiba como monitorar e diagnosticar seus serviços escritos com o Service Fabric do Microsoft Azure em um computador de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Monitoramento, detecção, diagnóstico e solução de problemas permitem dar continuidade aos serviços com mínima interrupção da experiência do usuário. O monitoramento e o diagnóstico são essenciais em um ambiente de produção implantado. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funcionará quando você se deslocar para um ambiente de produção. O Service Fabric facilita para o desenvolvedor de serviço implementar diagnóstico que possa funcionar perfeitamente tanto em configurações de desenvolvimento local de computador único, quanto em configurações reais de cluster de produção.


## Depuração de aplicativos Java do Service Fabric

Para aplicativos Java, [várias estrutura de registros](http://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma vez que `java.util.logging` é a opção padrão com o JRE, ele também é usado nos [códigos de exemplos no GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started). A discussão a seguir explica como configurar a estrutura `java.util.logging`.
 
Ao usar java.util.logging, você pode redirecionar os logs do aplicativo para a memória, para os fluxos de saída, para os arquivos de consoles ou para os soquetes. Para cada uma dessas opções, há manipuladores padrão já fornecidos na estrutura. Você pode criar um arquivo `app.properties` para configurar o manipulador de arquivo para o seu aplicativo redirecionar todos os logs para um arquivo local.

O trecho de código a seguir contém um exemplo de configuração:

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A pasta apontada pelo arquivo `app.properties` deve existir. Depois que o arquivo `app.properties` for criado, você também precisará modificar o script de ponto de entrada `entrypoint.sh` na pasta `<applicationfolder>/<servicePkg>/Code/` para definir a propriedade `java.util.logging.config.file` para o arquivo `app.propertes`. A entrada deverá se parecer com o seguinte trecho:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Esta configuração resulta em logs sendo coletados em um modo de rotação em `/tmp/servicefabric/logs/`. O **%u** e o **%g** permitem a criação de mais arquivos, com os nomes de arquivo mysfapp0.log, mysfapp1.log e assim por diante. Por padrão, se nenhum manipulador for configurado explicitamente, o manipulador de console será registrado. Estes logs podem ser vistos no syslog, em /var/log/syslog.
 
Para obter mais informações, confira os [códigos de exemplos no GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).



## Próximas etapas
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico do seu aplicativo em um cluster do Azure. Consulte estes artigos que abordam as diferentes opções para as ferramentas e descrevem como configurá-las.
* [Como coletar logs com o Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)

<!---HONumber=AcomDC_0928_2016-->