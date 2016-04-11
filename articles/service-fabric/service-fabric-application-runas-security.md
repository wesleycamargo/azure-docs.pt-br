<properties
   pageTitle="Noções básicas sobre as políticas de segurança RunAs do aplicativo Service Fabric | Microsoft Azure"
   description="Uma visão geral de como executar um aplicativo Service Fabric em contas de segurança do sistema e locais, incluindo o SetupEntryPoint no qual o aplicativo precisa executar alguma ação privilegiada antes de iniciar"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="msfussell"/>

# RunAs: Executar um aplicativo de Service Fabric com permissões de segurança diferentes
O Service Fabric do Azure fornece o recurso de proteção de aplicativos em execução no cluster em contas de usuário diferentes, conhecido como **RunAs**. O Service Fabric também protege os recursos usados pelos aplicativos com a conta de usuário, como arquivos, diretórios e certificados.

Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário ou em um sistema local especificado no manifesto do aplicativo. Os tipos de conta do sistema local com suporte para RunAs são **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

> [AZURE.NOTE] As contas de domínio têm suporte em implantações do Windows Server nas quais o Active Directory do Azure está disponível.

É possível definir e criar grupos de usuários onde um ou mais usuários podem ser adicionados a cada grupo a gerenciados em conjunto. Isso será particularmente útil quando houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo.

## Definir a política de RunAs para SetupEntryPoint

Como descrito no [modelo de aplicativo](service-fabric-application-model.md), o **SetupEntryPoint** é um ponto de entrada privilegiado executado com as mesmas credenciais que o Service Fabric (normalmente, a conta *NetworkService*) antes de qualquer outro ponto de entrada. O arquivo executável especificado pelo **EntryPoint** normalmente é o host de serviço de longa execução; portanto, ter um ponto de entrada de instalação separado evita a necessidade de executar o executável do host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo **EntryPoint** é executado depois que o **SetupEntryPoint** é encerrado com êxito. O processo resultante é monitorado e reiniciado (começando novamente com **SetupEntryPoint**) se ele terminar ou falhar.

Veja abaixo um exemplo simples de manifesto do serviço mostrando o SetupEntryPoint e o EntryPoint principal para o serviço.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Configurar a política RunAs usando uma conta local

Após ter configurado do serviço para ter um SetupEntryPoint, é possível alterar as permissões de segurança sob as quais ele é executado no manifesto do aplicativo. O exemplo a seguir mostra como configurar o serviço para ser executado com privilégios de conta de administrador de usuário.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Primeiro, crie uma seção **Principals** com um nome de usuário SetupAdminUser. Isso indica que o usuário é membro do grupo do sistema Administradores.

Em seguida, na seção **ServiceManifestImport**, configure uma política para aplicar essa entidade ao **SetupEntryPoint**. Isso informa ao Service Fabric que quando o arquivo **MySetup.bat** for executado ele deve ser RunAs com privilégios de Administrador. Considerando que você *não* aplicou uma política ao ponto de entrada principal, o código em **MyServiceHost.exe** será executado sob o conta do sistema **NetworkService**. Essa é a conta padrão que todos os pontos de entrada de serviço são executados como.

Agora, vamos adicionar o arquivo MySetup.bat ao projeto do Visual Studio para testar os privilégios de Administrador. No Visual Studio, clique com botão direito do mouse no projeto de serviço e adicione uma nova chamada do arquivo MySetup.bat.

Em seguida, certifique-se de que o arquivo MySetup.bat é incluído no service pack. Por padrão, não é. Selecione o arquivo, clique com o botão direito para exibir o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, verifique se **Copiar para diretório de saída** está definido como **Copiar se mais recente**. Isso é mostrado na captura de tela abaixo.

![CopyToOutput do Visual Studio para o arquivo em lotes SetupEntryPoint][image1]

Agora, abra o arquivo MySetup.bat e adicione os comandos a seguir.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Em seguida, compile e implante a solução em um cluster de desenvolvimento local. Após a inicialização do serviço, conforme mostrado no Gerenciador do Service Fabric, é possível ver que MySetup.bat foi bem-sucedido de duas maneiras. Abra um prompt de comando do PowerShell e digite:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Em seguida, anote o nome do nó no qual o serviço foi implantado e iniciado no Gerenciador do Service Fabric, por exemplo, Nó 2. Em seguida, navegue até a pasta de trabalho de instância do aplicativo para localizar o arquivo out.txt que mostra o valor de **TestVariable**. Por exemplo, se ele foi implantado no Nó 2, você pode acessar este caminho até o **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Configurar a política RunAs usando contas do sistema local
Em geral, é preferível executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administradores, como mostrado acima. A execução da política RunAs como administrador geralmente não funciona bem, já que os computadores têm o UAC (Controle de Acesso de Usuário) habilitado por padrão. Em tais casos, **a recomendação é executar o SetupEntryPoint como LocalSystem em vez de um usuário local adicionado ao grupo Administradores**. O exemplo a seguir mostra a definição de SetupEntryPoint para ser executado como LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  Iniciar comandos do PowerShell a partir do SetupEntryPoint
Para executar o PowerShell do ponto **SetupEntryPoint**, execute **PowerShell.exe** em um arquivo em lotes que aponte para um arquivo do PowerShell. Primeiro, adicione um arquivo do PowerShell ao projeto de serviço, por exemplo, **MySetup.ps1**. Não deixe de definir a propriedade *Copiar se for mais recente* para que esse arquivo também seja incluído no pacote de serviço. O exemplo a seguir mostra um exemplo de arquivo em lotes para iniciar um arquivo do PowerShell chamado MySetup.ps1, que define uma variável de ambiente do sistema chamada **TestVariable**.


Use MySetup.bat para iniciar o arquivo do PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

No arquivo do PowerShell, adicione o seguinte para definir uma variável de ambiente do sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Observação:** por padrão, quando o arquivo em lote é executado, ele examina a pasta de aplicativo chamada **trabalho** em busca de arquivos. Nesse caso, quando MySetup.bat é executado, queremos que o MySetup.ps1 seja encontrado na mesma pasta, que é a pasta do **pacote de código** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho como mostrado abaixo.
    
~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint> 
~~~

## Usando a política de redirecionamento de console para depuração local dos pontos de entrada
De vez em quando, é útil ver a saída do console da execução de um script para fins de depuração. Para fazer isso, você pode definir uma política de redirecionamento de console que grava a saída em um arquivo. A saída do arquivo é gravada na pasta de aplicativo chamada **log** no nó onde o aplicativo é implantado e executado (veja onde encontrá-la no exemplo acima).

**Observação: nunca** use a política de redirecionamento de console em um aplicativo implantado na produção, pois isso pode afetar o failover do aplicativo. **SÓ** use isso para fins de depuração e de desenvolvimento locais.

O exemplo a seguir mostra a configuração de redirecionamento do console com um valor de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint> 
~~~

Se você alterar o arquivo MySetup.ps1 para gravar um comando **Echo**, ele gravará o arquivo de saída para fins de depuração.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to" 
~~~

**Depois de ter depurado seu script, remova imediatamente a política de redirecionamento de console**

## Aplicar RunAsPolicy aos serviços
Nas etapas acima, você viu como aplicar a política RunAs a um SetupEntryPoint. Agora, vamos analisar com mais detalhes como criar entidades diferentes que possam ser aplicadas como políticas de serviço.

### Criar grupos de usuários locais
É possível definir e criar grupos de usuários nos quais um ou mais usuários podem ser adicionados a um grupo. Isso será particularmente útil se houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo. O exemplo a seguir mostra um grupo local chamado **LocalAdminGroup** com privilégios de administrador. Dois usuários, Customer1 e Customer2, tornam-se membros desse grupo local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Criar usuários locais
Você pode criar um usuário local que pode ser usado para proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Entidades do manifesto do aplicativo, o Service Fabric cria contas de usuário local em máquinas nas quais o aplicativo foi implantado. Por padrão, essas contas não têm os mesmos nomes especificados no manifesto do aplicativo (por exemplo, Customer3 no exemplo abaixo). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Atribuir políticas aos pacotes de códigos do serviço
A seção **RunAsPolicy** para uma **ServiceManifestImport** especifica a conta da seção de entidades que deve ser usada para executar um pacote de códigos. Ela também associa os pacotes de código do manifesto do serviço com contas de usuário na seção entidades. Você pode especificar isso para os pontos de entrada de configuração ou principal, ou especificar Todos para aplicar a ambos. O exemplo a seguir mostra a aplicação de políticas diferentes:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se **EntryPointType** não for especificado, o padrão será definido como EntryPointType ="Main". A especificação de um **SetupEntryPoint** é especialmente útil quando você deseja executar determinada operação de instalação de privilégio alto em uma conta do sistema. O código de serviço real pode ser executado em uma conta de baixo privilégio.

### Aplicar uma política padrão a todos os pacotes de códigos de serviço
A seção **DefaultRunAsPolicy** é usada para especificar uma conta de usuário padrão para todos os pacotes de códigos que não tenham uma **RunAsPolicy** específica definida. Quando a maioria dos pacotes de códigos especificada nos manifestos do serviço usados por um aplicativo precisar ser executada com o mesmo usuário de RunAs, o aplicativo poderá definir apenas uma política RunAs padrão nessa conta de usuário, em vez de especificar uma **RunAsPolicy** para cada pacote de códigos. O exemplo a seguir especifica que quando um pacote de códigos não tiver uma **RunAsPolicy** especificada, ele deverá ser executado em uma **MyDefaultAccount** especificada na seção Entidades.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Atribuindo SecurityAccessPolicy aos pontos de extremidade HTTP e HTTPS
Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, será necessário especificar uma **SecurityAccessPolicy** para assegurar que as portas alocadas a esses pontos de extremidade sejam corretamente listadas no controle de acesso para a conta de usuário RunAs na qual o serviço é executado. Caso contrário, o **http.sys** não terá acesso ao serviço e você receberá uma falha com chamadas do cliente. O exemplo a seguir se aplica a conta Customer3 a um ponto de extremidade chamado **ServiceEndpointName**, concedendo a ele direitos de acesso totais.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Para o ponto de extremidade HTTPS, você também precisa indicar o nome do certificado para retornar para o cliente. Você pode fazer isso usando **EndpointBindingPolicy**, com o certificado definido em uma seção de certificados no manifesto do aplicativo.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Um exemplo completo de manifesto do aplicativo
O manifesto do aplicativo abaixo mostra várias configurações diferentes descritas acima.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

* [Entenda o modelo de aplicativo](service-fabric-application-model.md)
* [Especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md)
* [Implantar um aplicativo](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0330_2016-->