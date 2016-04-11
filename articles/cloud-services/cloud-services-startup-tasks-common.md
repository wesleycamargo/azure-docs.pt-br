<properties 
pageTitle="Tarefas de inicialização comuns para Serviços de Nuvem | Microsoft Azure" 
description="Oferece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar na função Web ou função de trabalho de seus serviços de nuvem." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="03/25/2016" 
ms.author="adegeo"/>

# Tarefas de inicialização comuns do Serviço de Nuvem

Este artigo oferece alguns exemplos de tarefas de inicialização comuns que talvez você queira executar no serviço de nuvem. Você pode usar as tarefas de inicialização para executar operações antes do início de uma função. As operações que talvez você queira executar incluem a instalação de um componente, o registro de componentes COM, a configuração de chaves do registro ou o início de um processo de longa duração.

Confira [este artigo](cloud-services-startup-tasks.md) para entender o funcionamento das tarefas de inicialização e, especificamente, como criar as entradas que definem uma tarefa de inicialização.

Muitas das tarefas aqui usam

>[AZURE.NOTE] As tarefas de inicialização não são aplicáveis às Máquinas Virtuais, apenas às funções Web e de Trabalho do Serviço de Nuvem.


## Definir variáveis de ambiente antes de iniciar uma função

Você pode definir variáveis de ambiente para uma função inteira adicionando o elemento [Runtime] à definição da função no arquivo de definição de serviço.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

Se você precisar de variáveis de ambiente definidas para uma tarefa específica, que não sejam compartilhadas por outras tarefas, poderá usar o elemento [Environment] dentro do elemento [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

As variáveis também podem usar um [valor válido do Azure XPath](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implantação. Em vez de usar o atributo `value`, defina um elemento filho [RoleInstanceValue].

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Configurar a inicialização do IIS com AppCmd.exe

A ferramenta de linha de comando [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) pode ser usada para gerenciar as configurações do IIS na inicialização no Azure. *AppCmd.exe* oferece acesso de linha de comando conveniente às definições de configuração para uso nas tarefas de inicialização no Azure. Com *AppCmd.exe*, as configurações do site podem ser adicionadas, modificadas ou removidas para aplicativos e sites.

No entanto, há algumas coisas que merecem atenção no uso de *AppCmd.exe* como uma tarefa de inicialização:

- As tarefas de inicialização podem ser executadas mais de uma vez entre as reinicializações. Isso pode acontecer caso a função seja reciclada, por exemplo.
- Algumas ações do *AppCmd.exe* podem gerar erros caso elas sejam executadas mais de uma vez. A tentativa de adicionar uma seção a *Web.config* duas vezes pode gerar um erro.
- As tarefas de inicialização falharão caso retornem um código de saída diferente de zero ou **errorlevel**. Isso poderá acontecer se *AppCmd.exe* gerar um erro.

Pelos motivos listados, geralmente é prudente verificar o **errorlevel** depois de chamar *AppCmd.exe*, o que será fácil se você encapsular a chamada para *AppCmd.exe* com um arquivo *.cmd*. Se você detectar uma resposta conhecida de **errorlevel**, poderá ignorá-la ou, ao contrário, retorná-la. Isso é demonstrado no exemplo abaixo.

Os errorlevels retornados por *AppCmd.exe* são listados no arquivo winerror.h e também podem ser vistos no [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### Exemplo

Este exemplo adiciona uma seção e uma entrada de compactação para JSON para o arquivo *Web.config*, com tratamento de erros e registro em log.

As seções relevantes do arquivo [ServiceDefinition.csdef] são mostradas aqui, o que inclui a definição do atributo [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) como `elevated` para dar a *AppCmd.exe* permissões suficientes para alterar as configurações no arquivo *Web. config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

O arquivo em lotes Startup.cmd usa AppCmd.exe para adicionar uma seção e uma entrada de compactação para JSON ao arquivo *Web.config*. O **errorlevel** esperado de 183 é definido como zero usando o programa de linha de comando VERIFY.EXE. Os errorlevels inesperados são registrados em StartupErrorLog.txt.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Adicionar regras de firewall

No Azure, há efetivamente dois firewalls. O primeiro firewall controla conexões entre a máquina virtual e o mundo externo. Isso é controlado pelo elemento [EndPoints] no arquivo [ServiceDefinition.csdef].

O segundo firewall controla conexões entre a máquina virtual e os processos dessa máquina virtual. Isso é controlado pela ferramenta de linha de comando `netsh advfirewall firewall` e é o foco deste artigo.

O Azure cria regras de firewall para processos iniciados em suas funções. Por exemplo, quando você inicia um serviço ou um programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que o serviço ser comunique com a Internet. No entanto, se você criar um serviço iniciado por um processo fora de sua função (por exemplo, um serviço COM+ ou um programa que inicia usando o Agendador do Windows), você precisará criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Essas regras de firewall podem ser criadas usando uma tarefa de inicialização.

Uma tarefa de inicialização que cria uma regra de firewall deve ter um [executionContext][Task] **elevado**. Adicione a seguinte tarefa de inicialização ao arquivo [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Para adicionar a regra de firewall, você deverá usar os comandos `netsh advfirewall firewall` adequados no arquivo em lotes de inicialização. Neste exemplo, a tarefa de inicialização exige segurança e criptografia para a porta TCP 80.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Bloquear um endereço IP específico

Você pode restringir o acesso de uma função Web do Azure a um conjunto de endereços IP especificados modificando o arquivo **Web.config** do IIS e criando um arquivo de comando que desbloqueia a seção **ipSecurity** do arquivo **ApplicationHost.config**.

Primeiro, crie um arquivo de comando que seja executado quando a função for iniciada, o que desbloqueará a seção **ipSecurity** do arquivo **ApplicationHost.config**. Crie uma nova pasta no nível raiz da sua função Web chamada **startup** e, nessa pasta, crie um arquivo em lotes chamado **startup.cmd**. Defina as propriedades desse arquivo como **Copiar Sempre** para garantir que ele seja implantado.

Adicione a tarefa de inicialização a seguir ao arquivo [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adicione este comando ao arquivo **startup.cmd**:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

Isso faz com que o arquivo em lotes **startup.cmd** seja executado sempre que a função Web for inicializada, garantindo que a seção **ipSecurity** necessária seja desbloqueada.

Por fim, modifique a [seção system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) do arquivo **web.config** da sua função Web para adicionar uma lista de endereços IP com acesso concedido, como mostrado neste exemplo:

Esta configuração de exemplo **permite** que todos os IPs acessem o servidor, exceto os dois definidos

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta configuração de exemplo **nega** que todos os IPs acessem o servidor, exceto os dois definidos.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Criar uma tarefa de inicialização do PowerShell

Os scripts do Windows PowerShell não podem ser chamados diretamente do arquivo [ServiceDefinition.csdef], mas podem ser chamados de um arquivo em lotes de inicialização.

O PowerShell, por padrão, não executará um script não assinado. A menos que você assine seus scripts, será necessário configurar o Windows PowerShell para executar scripts não assinados. Para executar scripts não assinados, **ExecutionPolicy** deve ser definido como **Irrestrito**. A configuração **ExecutionPolicy** que você usa baseia-se na versão do Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Se você estiver usando um SO Convidado que esteja executando o PowerShell 2.0 ou 1.0, poderá impor a execução da versão 2. Se ela não estiver disponível, use a versão 1.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Criar arquivos no armazenamento local de uma tarefa de inicialização

Você pode usar um recurso de armazenamento local para armazenar os arquivos criados pela tarefa de inicialização que será acessada posteriormente por seu aplicativo.

Para criar o recurso de armazenamento local, adicione uma seção [LocalResources] ao arquivo [ServiceDefinition.csdef] e adicione o elemento filho [LocalStorage]. Dê ao recurso de armazenamento local um nome exclusivo e um tamanho adequado para sua tarefa de inicialização.

Para usar um recurso de armazenamento local em sua tarefa de inicialização, será necessário criar uma variável de ambiente para fazer referência ao local do recurso de armazenamento local. Em seguida, a tarefa de Inicialização e o aplicativo poderão ler e gravar arquivos no recurso de armazenamento local.

As seções relevantes do arquivo **ServiceDefinition.csdef** são mostradas aqui:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Como um exemplo, esse arquivo em lotes **Startup.cmd** usa a variável de ambiente **PathToStartupStorage** para criar o arquivo **MyTest.txt** no armazenamento local.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

Você pode acessar o armazenamento local do SDK do Azure usando o método [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). As operações padrão de leitura e gravação do arquivo funcionarão para ler e gravar o conteúdo do recurso de armazenamento local.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Diferenciar entre a execução no Emulador e na Nuvem

Você pode fazer com que sua tarefa de inicialização execute etapas diferentes quando estiver funcionando na nuvem em comparação a quando estiver no emulador de computação. Por exemplo, convém usar uma cópia atualizada dos dados SQL somente durante a execução no emulador. Ou você talvez queira fazer algum tipo de otimização de desempenho para a nuvem que não seja necessária na execução no emulador.

Essa capacidade de executar ações diferentes no emulador de computação e na nuvem pode ser obtida por meio da criação de uma variável de ambiente no arquivo [ServiceDefinition.csdef] e do teste da variável de ambiente em sua tarefa de inicialização.

Para criar a variável de ambiente, adicione o elemento [Variable]/[RoleInstanceValue] e crie um valor XPath de `/RoleEnvironment/Deployment/@emulated`. O valor da variável de ambiente **%ComputeEmulatorRunning%** será `"true"` na execução no emulador de computação e será `"false"` na execução na nuvem.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

Agora, todas as tarefas poderão usar a variável de ambiente **%ComputeEmulatorRunning%** para executar ações diferentes com base na função estar ou não em execução na nuvem ou no emulador. A seguir, um script de shell .cmd que verifica essa variável de ambiente.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## Detectar se a tarefa já foi executada

A função pode ser reciclada sem uma reinicialização, fazendo com que suas tarefas de inicialização sejam executadas novamente. Há um sinalizador para indicar que uma tarefa já foi executada na VM de hospedagem. Talvez você tenha algumas tarefas onde não importará se elas forem executadas várias vezes. No entanto, você poderá encontrar uma situação em que precisará impedir que uma tarefa seja executada mais de uma vez.

A maneira mais simples de detectar se uma tarefa já foi executada é criar um arquivo na pasta **%TEMP%** quando a tarefa for bem-sucedida e procurá-lo no início da tarefa. A seguir, um script de shell cmd de exemplo que faz isso para você.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## Práticas recomendadas para tarefas
A seguir, algumas práticas recomendadas que você deve seguir ao configurar a tarefa para sua função Web ou de trabalho.

### Sempre registrar em log as atividades de inicialização

O Visual Studio não fornece um depurador para percorrer arquivos em lotes e, portanto, será bom ter tantos dados sobre a operação de arquivos em lotes quanto possível. O registro em log da saída de arquivos em lotes, **stdout** e **stderr**, pode fornecer informações importantes ao tentar depurar e corrigir arquivos em lotes. Para registrar em log **stdout** e **stderr** para o arquivo StartupLog.txt no diretório apontado pela variável de ambiente **%TEMP%**, adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao final de linhas específicas que deseja registrar em log. Por exemplo, para executar setup.exe no diretório **%PathToApp1Install%**:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Se você quiser registrar em log todas as saídas da tarefa de inicialização sem adicionar `>> "%TEMP%\StartupLog.txt" 2>&1`ao final de cada linha, serão necessários dois arquivos em lotes de inicialização. O primeiro arquivo em lotes chamará o segundo arquivo em lotes com redirecionamento para registrar em log todas as atividades do segundo arquivo em lotes. Isso será necessário para que o redirecionamento adequado ocorra.

A seguir, você verá como redirecionar todas as saídas de um arquivo em lotes de inicialização. Neste exemplo, o arquivo ServerDefinition.csdef cria uma tarefa de inicialização que chama Startup1.cmd. Startup1.cmd chama Startup2.cmd, redirecionando todas as saídas para %TEMP%\\StartupLog.txt.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Definir executionContext adequadamente para tarefas de inicialização

Definir privilégios adequadamente para a tarefa de inicialização. Às vezes, as tarefas de inicialização devem ser executadas com privilégios elevados, mesmo que a função seja executada com privilégios normais.

O atributo [executionContext][Task] define o nível de privilégio da tarefa de inicialização. A utilização de `executionContext="limited"` significa que a tarefa de inicialização terá o mesmo nível de privilégio que a função. A utilização de `executionContext="elevated"` significa que a tarefa de inicialização terá privilégios de administrador, o que permite que a tarefa de inicialização execute tarefas de administrador sem conceder privilégios de administrador à sua função.

Um exemplo de uma tarefa de inicialização que exija privilégios elevados é uma tarefa de inicialização que usa **AppCmd.exe** para configurar o IIS. **AppCmd.exe** exige `executionContext="elevated"`.

### Usar o taskType adequado

O atributo [taskType][Task] determina a maneira como a tarefa de inicialização será executada. Há três valores: **simples**, **segundo plano** e **primeiro plano**. As tarefas em primeiro e segundo plano são iniciadas de forma assíncrona e as tarefas simples são executadas de forma síncrona, uma de cada vez.

Com as tarefas de inicialização **simples**, você pode definir a ordem na qual as tarefas ocorrerão pela ordem na qual as tarefas são listadas no arquivo ServiceDefinition.csdef. Se uma tarefa **simples** terminar com um código de saída diferente de zero, o procedimento de inicialização será interrompido e a função não será iniciada.

A diferença entre as tarefas de inicialização em **segundo plano** e em **primeiro plano** é que as tarefas de inicialização em **primeiro plano** manterão a função em execução até a tarefa em **primeiro plano** ser encerrada. Isso também significa que, se a tarefa em **primeiro plano** congelar ou falhar, a função não será reciclada até a tarefa em **primeiro plano** ser forçada a fechar. Por esse motivo, as tarefas em **segundo plano** são recomendadas para tarefas de inicialização assíncronas, a menos que você precise desse recurso da tarefa em **primeiro plano**.

### Encerrar arquivos em lotes com EXIT /B 0

A função só será iniciada se o **errorlevel** de cada uma de suas tarefas de inicialização simples for zero. Nem todos os programas definem o **errorlevel** (código de saída) corretamente e, portanto, o arquivo em lotes deverá terminar com um `EXIT /B 0` se tudo tiver sido executado corretamente.

Um `EXIT /B 0` ausente no final de um arquivo em lotes de inicialização é uma causa comum de funções que não são iniciadas.

### Esperar que tarefas de inicialização sejam executadas mais de uma vez

Nem todas as reciclagens de função incluem uma reinicialização, mas todas as reciclagens incluem a execução de todas as tarefas de inicialização. Isso significa que deve ser possível executar as tarefas de inicialização várias vezes entre as reinicializações sem problemas. Veja a discussão sobre isso [acima](#detect-that-your-task-has-already-run).

### Usar o armazenamento local para armazenar arquivos que devem ser acessados na função

Se você quiser copiar ou criar um arquivo enquanto sua tarefa de inicialização estiver acessível para sua função, esse arquivo deverá ser colocado no armazenamento local. Confira a [seção](#create-files-in-local-storage-from-a-startup-task) acima.

## Próximas etapas

Examine o [modelo de serviço e o pacote da nuvem](cloud-services-model-and-package.md)

Saiba mais sobre o funcionamento de [Tarefas](cloud-services-startup-tasks.md).

[Crie e implante](cloud-services-how-to-create-deploy-portal.md) seu pacote de serviço de nuvem.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_0330_2016-->