<properties 
	pageTitle="Carregar um aplicativo Web Java personalizado no Azure" 
	description="Este tutorial mostra como carregar um aplicativo Web Java personalizado em Aplicativos Web do Serviço de Aplicativo do Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/31/2015" 
	ms.author="robmcm"/>

# Carregar um aplicativo Web Java personalizado no Azure

Este tópico explica como carregar um aplicativo Web Java personalizado para aplicativos Web do [Serviço de Aplicativo do Azure]. Há informações que se aplicam a qualquer site ou aplicativo Web Java e também alguns exemplos para aplicativos específicos.

Observe que o Azure fornece um meio para a criação de aplicativos Web Java usando a interface de configuração do portal de visualização do Azure e o Azure Marketplace, conforme documentado em [Criar um aplicativo Web Java no Serviço de Aplicativo do Azure](web-sites-java-get-started.md). Este tutorial destina-se a cenários em que você não deseja usar a interface do usuário de configuração do portal ou o Azure Marketplace.

## Diretrizes de configuração

As configurações esperadas para aplicativos Web Java personalizados no Azure são descritas a seguir.

- A porta HTTP usada pelo processo Java é atribuída dinamicamente. O processo deve usar a porta a partir da variável de ambiente `HTTP_PLATFORM_PORT`.
- Todas as portas de escuta que não sejam a única porta ouvinte HTTP devem ser desabilitadas. No Tomcat, que inclui o desligamento, as portas HTTPS e AJP.
- O contêiner precisa ser configurado apenas para tráfego IPv4.
- O comando **startup** do aplicativo precisa ser definido na configuração.
- Os aplicativos que precisam de diretórios com permissão de gravação precisam estar localizados no diretório de conteúdo do aplicativo Web do Azure, que é **D:\\home**. A variável de ambiente `HOME` refere-se a D:\\home.  

Você pode definir as variáveis de ambiente conforme necessário no arquivo web.config.

## Configuração de httpPlatform do web.config

As seguintes informações descrevem o formato de **httpPlatform** no web.config.
                                 
**arguments** (Padrão=""). Os argumentos para o arquivo executável ou o script especificado na configuração de **processPath**.

Exemplos (mostrados com **processPath** incluído):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"


**processPath** - Caminho para o arquivo executável ou script que iniciará um processo de escuta de solicitações HTTP.

Exemplos:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (Padrão = 10.) Número de vezes que o processo especificado em **processPath** pode falhar por minuto. Se esse limite for excedido, **HttpPlatformHandler** interromperá o início do processo para o restante do minuto.
                                    
**requestTimeout** (Padrão="00:02:00".) Duração na qual **HttpPlatformHandler** aguardará por uma resposta do processo que escuta `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Padrão = 10.) Número de vezes que **HttpPlatformHandler** tentará iniciar o processo especificado em **processPath**. Consulte **startupTimeLimit** para obter mais detalhes.

**startupTimeLimit** (Padrão = 10 segundos.) Duração na qual **HttpPlatformHandler** aguardará o executável/script iniciar um processo de escuta na porta. Se esse limite de tempo for excedido, **HttpPlatformHandler** irá interromper o processo e tentar iniciá-lo novamente **startupRetryCount** vezes.
                                                                                      
**stdoutLogEnabled** (Padrão = "true".) Se verdadeiros, **stdout** e **stderr** para o processo especificado na configuração de **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile** (consulte a seção **stdoutLogFile**).
                                    
**stdoutLogFile** (Padrão="d:\\home\\LogFiles\\httpPlatformStdout.log".) Caminho absoluto do arquivo para o qual **stdout** e **stderr** do processo especificado em **processPath** será registrado.
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%` é um espaço reservado especial que precisa ser especificado como parte de **arguments** ou como parte da lista de **httpPlatform** **environmentVariables**. Será substituído por uma porta gerada internamente por **HttpPlatformHandler** para que o processo especificado por **processPath** possa escutar nessa porta.

## Implantação

Aplicativos Web baseados em Java podem ser implantados facilmente quase que da mesma maneira usada com aplicativos Web com base em IIS (Serviços de Informações da Internet). Há suporte para FTP, Git e Kudu como mecanismos de implantação, assim como o recurso SCM integrado para aplicativos Web. O WebDeploy funciona como um protocolo, no entanto, como o Java não é desenvolvido no Visual Studio, o WebDeploy não é adequado para casos de uso de implantação de aplicativos Web Java.

## Exemplos de configuração de aplicativos

Para os aplicativos a seguir, um arquivo web.config e a configuração do aplicativo são fornecidos como exemplos para mostrar como habilitar o aplicativo Java em Aplicativos Web do Serviço de Aplicativo.

### Tomcat
Embora, existam duas variações Tomcat fornecidas com Aplicativos Web do Serviço de Aplicativo do Azure, ainda é perfeitamente possível carregar instâncias específicas do cliente. A seguir, um exemplo de uma instalação do Tomcat com uma JVM (Máquina Virtual Java) diferente.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

No lado do Tomcat, há algumas alterações na configuração que precisam ser feitas. O server.xml precisa ser editado para definir:

-	Shutdown port = -1
-	HTTP connector port = ${port.http}
-	HTTP connector address = "127.0.0.1"
-	Comente os conectores HTTPS e AJP
-	A configuração de IPv4 também pode ser definida no arquivo catalina.properties, onde você pode adicionar `java.net.preferIPv4Stack=true`
    
Chamadas de Direct3d não têm suporte em Aplicativos Web do Serviço de Aplicativo. Para desabilitá-las, adicione a seguinte opção de Java se seu aplicativo fizer essas chamadas: `-Dsun.java2d.d3d=false`

### Jetty

Como no caso do Tomcat, os clientes podem carregar suas próprias instâncias de Jetty. No caso de execução da instalação completa do Jetty, a configuração ficaria assim:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115"; -jar ";%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar";"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

A configuração do Jetty precisa ser alterada no start.ini para definir `java.net.preferIPv4Stack=true`.

### Hudson

Nosso teste usou o war do Hudson 3.1.2 e a instância padrão do Tomcat 7.0.50, mas sem usar a interface do usuário para configurar as coisas. Como o Hudson é uma ferramenta de criação de software, é aconselhável instalá-lo em instâncias dedicadas em que o sinalizador **AlwaysOn** pode ser definido no aplicativo Web.

1. Na raiz dos sites do Azure, ou seja, **d:\\home\\site\\wwwroot**, crie um diretório **webapps** (se ainda não existir um) e coloque o Hudson.war em **d:\\home\\site\\wwwroot\\webapps**.
2. Baixe o Apache Maven 3.0.5 (compatível com Hudson) e coloque-o em **d:\\home\\site\\wwwroot**.
3. Crie o web.config em **d:\\home\\site\\wwwroot** e cole o seguinte conteúdo nele:
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    Nesse momento, o aplicativo Web pode ser reiniciado para efetuar as alterações. Conecte-se a http://yourwebapp/hudson para iniciar o Hudson.

4. Depois do Hudson configurar-se, você verá a seguinte tela:

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Acesse a página de configuração do Hudson: clique em **Gerenciar o Hudson** e, em seguida, clique em **Configurar o Sistema**.
6. Configure o JDK conforme mostrado abaixo:

	![Configuração do Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configure o Maven conforme mostrado abaixo:

	![Configuração do Maven](./media/web-sites-java-custom-upload/maven.png)

8. Salve as configurações. O Hudson agora deve estar configurado e pronto para uso.

Para obter informações adicionais sobre o Hudson, consulte [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

O Liferay tem suporte em Aplicativos Web do Serviço de Aplicativo. Como o Liferay pode exigir memória significativa, o aplicativo Web precisa ser executado em um trabalho dedicado médio ou grande, que pode fornecer memória suficiente. O Liferay também leva vários minutos para ser inicializado. Por esse motivo, é recomendável que você defina o aplicativo Web como **Sempre Ativo**.

Usando o Liferay 6.1.2 Community Edition GA3 empacotado com o Tomcat, os seguintes arquivos foram editados após o download do Liferay:

**Server.xml**

- Altere a porta de desligamento para -1.
- Altere o conector HTTP para `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Comente o conector AJP.

Na pasta **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes**, crie um arquivo chamado **portal-ext.properties**. Esse arquivo deve conter uma linha, como mostrado aqui:

    liferay.home=%HOME%/site/wwwroot/liferay

No mesmo nível de diretório que a pasta tomcat-7.0.40, crie um arquivo chamado **web.config** com o seguinte conteúdo:

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Sob o bloco **httpPlatform**, o **requestTimeout** é definido como "00:10:00". Pode ser reduzido, mas você provavelmente verá alguns erros de tempo limite enquanto o Liferay estiver inicializando. Se esse valor for alterado, o **connectionTimeout** no server.xml do tomcat também deverá ser modificado.

É importante observar que a variável de ambiente JRE\_HOME é especificada no web.config acima para apontar para o JDK de 64 bits. O padrão é 32 bits, mas como o Liferay pode exigir altos níveis de memória, é recomendável usar o JDK de 64 bits.

Depois de fazer essas alterações, reinicie seu aplicativo Web executando o Liferay. Em seguida, abra http://yourwebapp. O portal do Liferay está disponível na raiz do aplicativo Web.

## Próximas etapas

Para obter mais informações sobre o Liferay, consulte [http://www.liferay.com](http://www.liferay.com).

Para obter mais informações sobre Java, consulte o [Centro de desenvolvedores do Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Serviço de Aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=Sept15_HO4-->