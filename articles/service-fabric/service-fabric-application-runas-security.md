<properties
   pageTitle="Noções básicas sobre as políticas de segurança de serviço e do aplicativo Service Fabric | Microsoft Azure"
   description="Uma visão geral de como executar um aplicativo Service Fabric em contas de segurança do sistema e locais, incluindo o SetupEntryPoint no qual o aplicativo precisa executar alguma ação privilegiada antes de iniciar"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# Configurar políticas de segurança para seu aplicativo
O Service Fabric do Azure fornece o recurso de proteção de aplicativos em execução no cluster em contas de usuário diferentes. O Service Fabric também protege os recursos usados pelos aplicativos no momento da implantação na conta de usuário, como arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, protegidos uns dos outros.

Por padrão, os aplicativos de Service Fabric são executados na conta sob a qual o processo Fabric.exe está sendo executado. O Service Fabric também fornece a capacidade de executar aplicativos em uma conta de usuário ou em um sistema local especificado no manifesto do aplicativo. Os tipos de conta do sistema local com suporte são **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

 Durante a execução do Service Fabric no Windows Server em seu data center usando o instalador autônomo, você pode usar contas de domínio do Active Directory (AD).

É possível definir e criar grupos de usuários onde um ou mais usuários podem ser adicionados a cada grupo a gerenciados em conjunto. Isso será útil quando houver vários usuários para pontos de entrada de serviço diferentes e eles precisarem de privilégios comuns disponíveis no nível do grupo.

## Configurar a política para o serviço SetupEntryPoint

Como descrito no [modelo de aplicativo](service-fabric-application-model.md), o **SetupEntryPoint** é um ponto de entrada privilegiado executado com as mesmas credenciais que o Service Fabric (normalmente, a conta *NetworkService*) antes de qualquer outro ponto de entrada. O arquivo executável especificado pelo **EntryPoint** normalmente é o host de serviço de longa execução; portanto, ter um ponto de entrada de instalação separado evita a necessidade de executar o executável do host de serviço com altos privilégios por longos períodos de tempo. O executável especificado pelo **EntryPoint** é executado depois que o **SetupEntryPoint** é encerrado com êxito. O processo resultante é monitorado e reiniciado, começando novamente com **SetupEntryPoint**, caso ele termine ou falhe.

O item a seguir é um exemplo simples de manifesto do serviço mostrando o SetupEntryPoint e o EntryPoint principal para o serviço.

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

### Configurar a política usando uma conta local

Após ter configurado do serviço para ter um SetupEntryPoint, é possível alterar as permissões de segurança sob as quais ele é executado no manifesto do aplicativo. O item a seguir mostra como configurar o serviço para ser executado com privilégios de conta de administrador de usuário.

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

Em seguida, na seção **ServiceManifestImport**, configure uma política para aplicar essa entidade de segurança ao **SetupEntryPoint**. Isso informa ao Service Fabric que quando o arquivo **MySetup.bat** for executado ele deve ser RunAs com privilégios de Administrador. Considerando que você *não* aplicou uma política ao ponto de entrada principal, o código em **MyServiceHost.exe** será executado sob o conta do sistema **NetworkService**. Essa é a conta padrão que todos os pontos de entrada de serviço são executados como.

Agora, vamos adicionar o arquivo MySetup.bat ao projeto do Visual Studio para testar os privilégios de Administrador. No Visual Studio, clique com o botão direito do mouse no projeto de serviço e adicione uma nova chamada do arquivo MySetup.bat.

Em seguida, certifique-se de que o arquivo MySetup.bat é incluído no service pack. Por padrão, não é. Selecione o arquivo, clique com o botão direito do mouse para exibir o menu de contexto e escolha **Propriedades**. Na caixa de diálogo Propriedades, verifique se **Copiar para diretório de saída** está definido como **Copiar se mais recente**. Consulte a captura de tela a seguir.

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

Em seguida, crie e implante a solução em um cluster de desenvolvimento local. Após a inicialização do serviço, conforme mostrado no Gerenciador do Service Fabric, é possível ver que MySetup.bat foi bem-sucedido de duas maneiras. Abra um prompt de comando do PowerShell e digite:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Em seguida, anote o nome do nó no qual o serviço foi implantado e iniciado no Gerenciador do Service Fabric, por exemplo, Nó 2. Em seguida, navegue até a pasta de trabalho de instância do aplicativo para localizar o arquivo out.txt que mostra o valor de **TestVariable**. Por exemplo, se o serviço foi implantado no Nó 2, você pode acessar este caminho até o **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Configurar a política usando contas do sistema local
Em geral, é preferível executar o script de inicialização usando uma conta do sistema local em vez de uma conta de administradores, como mostrado acima. A execução da política RunAs como administrador geralmente não funciona bem, já que os computadores têm o UAC (Controle de Acesso de Usuário) habilitado por padrão. Em tais casos, **a recomendação é executar o SetupEntryPoint como LocalSystem em vez de um usuário local adicionado ao grupo Administradores**. O item a seguir mostra a definição de SetupEntryPoint para ser executado como LocalSystem.

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

##  Iniciar comandos do PowerShell em um SetupEntryPoint
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

**Observação:** por padrão, quando o arquivo em lote é executado, ele examina a pasta de aplicativo chamada **trabalho** em busca de arquivos. Nesse caso, quando MySetup.bat é executado, queremos que o MySetup.ps1 seja encontrado na mesma pasta, que é a pasta do **pacote de códigos** do aplicativo. Para alterar essa pasta, defina a pasta de trabalho como mostrado a seguir.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## Usando o redirecionamento de console para depuração local
De vez em quando, é útil ver a saída do console da execução de um script para fins de depuração. Para fazer isso, você pode definir uma política de redirecionamento de console que grava a saída em um arquivo. A saída do arquivo é gravada na pasta de aplicativo chamada **log** no nó no qual o aplicativo é implantado e executado (veja onde encontrá-la no exemplo acima).

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

## Configurar a política para pacotes de código de serviço 
Nas etapas anteriores, você viu como aplicar a política RunAs a um SetupEntryPoint. Agora, vamos analisar com mais detalhes como criar entidades diferentes que possam ser aplicadas como políticas de serviço.

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
Você pode criar um usuário local que pode ser usado para proteger um serviço dentro do aplicativo. Quando um tipo de conta **LocalUser** é especificado na seção Entidades do manifesto do aplicativo, o Service Fabric cria contas de usuário local em máquinas nas quais o aplicativo foi implantado. Por padrão, essas contas não têm os mesmos nomes especificados no manifesto do aplicativo (por exemplo, Customer3 no exemplo a seguir). Em vez disso, eles são gerados dinamicamente e têm senhas aleatórias.

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

### Atribuir políticas aos pacotes de códigos do serviço
A seção **RunAsPolicy** para uma **ServiceManifestImport** especifica a conta da seção de entidades que deve ser usada para executar um pacote de códigos. Ela também associa os pacotes de código do manifesto do serviço com contas de usuário na seção entidades. Você pode especificar isso para os pontos de entrada de configuração ou principal, ou especificar `All` para aplicar a ambos. O exemplo a seguir mostra a aplicação de políticas diferentes:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se **EntryPointType** não for especificado, o padrão será definido como EntryPointType ="Main". A especificação de um **SetupEntryPoint** é especialmente útil quando você deseja executar determinada operação de instalação de privilégio alto em uma conta do sistema. O código de serviço real pode ser executado em uma conta de baixo privilégio.

### Aplicar uma política padrão a todos os pacotes de códigos de serviço
A seção **DefaultRunAsPolicy** é usada para especificar uma conta de usuário padrão para todos os pacotes de códigos que não tenham uma **RunAsPolicy** específica definida. Quando a maioria dos pacotes de códigos especificada no manifesto do serviço usado por um aplicativo precisar ser executada com o mesmo usuário, o aplicativo poderá definir apenas uma política RunAs padrão nessa conta de usuário. O exemplo a seguir especifica que quando um pacote de códigos não tiver uma **RunAsPolicy** especificada, ele deverá ser executado em uma **MyDefaultAccount** especificada na seção Entidades.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### Usando um usuário ou um grupo de domínio do Active Directory
Para o Service Fabric instalado no Windows Server com o instalador autônomo, você pode executar o serviço sob as credenciais para uma conta de grupo ou de usuário do Active Directory (AD). Observação: esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (AAD). Ao usar um usuário de domínio ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

O exemplo a seguir mostra um usuário do AD denominado *TestUser* com a senha de domínio criptografada usando um certificado chamado *MyCert*. Você pode usar o comando `Invoke-ServiceFabricEncryptText` do Powershell para criar o texto cifrado secreto. Confira o artigo [Gerenciamento de segredos em aplicativos do Service Fabric](service-fabric-application-secret-management.md) para saber como. A chave privada do certificado para descriptografar a senha deve ser implantada no computador local em um método fora de banda (no Azure, isso ocorre por meio do Gerenciador de Recursos). Em seguida, quando o Service Fabric implanta o pacote de serviço no computador, ele é capaz de descriptografar o segredo e, juntamente com o nome de usuário, autenticar com o AD para execução com essas credenciais.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## Atribuindo SecurityAccessPolicy aos pontos de extremidade HTTP e HTTPS
Se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, será necessário especificar uma **SecurityAccessPolicy** para assegurar que as portas alocadas a esses pontos de extremidade sejam corretamente listadas no controle de acesso para a conta de usuário RunAs na qual o serviço é executado. Caso contrário, o **http.sys** não terá acesso ao serviço e você receberá uma falha com chamadas do cliente. O exemplo a seguir se aplica a conta Customer3 a um ponto de extremidade chamado **ServiceEndpointName**, concedendo a ele direitos de acesso completo.

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
O manifesto do aplicativo a seguir mostra várias configurações diferentes:

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

<!---HONumber=AcomDC_0928_2016-->