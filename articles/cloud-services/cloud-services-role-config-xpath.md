<properties 
pageTitle="Folha de consulta do XPath da configuração da Função dos Serviços de Nuvem | Microsoft Azure" 
description="As várias configurações do XPath que podem ser usadas na configuração da função do serviço de nuvem para expor as configurações como uma variável de ambiente." 
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
ms.date="02/25/2016" 
ms.author="adegeo"/>

# Expor as definições de configuração da função como uma variável de ambiente com o XPath

No arquivo de definição de serviço de função web ou do trabalho do serviço de nuvem, é possível expor os valores de configuração do tempo de execução como variáveis de ambiente. Há suporte para os valores do XPath a seguir (que correspondem aos valores da API).

Esses valores do XPath também estão disponíveis por meio da biblioteca [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx).

## Aplicativo em execução no emulador

Indica que o aplicativo está em execução no emulador.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Código | var x = RoleEnvironment.IsEmulated; |


## ID de Implantação

Recupera a ID de implantação da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Código | var deploymentId = RoleEnvironment.DeploymentId; |


## ID de Função 

Recupera a ID de função atual da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## Domínio de atualização

Recupera o domínio de atualização da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## Domínios de falha

Recupera o domínio de falha da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## Nome da função

Recupera o nome da função das instâncias.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |


## Definição de configuração

Recupera o valor da definição de configuração especificada.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código | var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## Caminho do armazenamento local

Recupera o caminho do armazenamento local da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |


## Tamanho do armazenamento local

Recupera o tamanho do armazenamento local da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## Protocolo do ponto de extremidade 

Recupera o protocolo do ponto de extremidade da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código | var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## IP do ponto de extremidade

Obtém o endereço IP do ponto de extremidade especificado.

| Tipo | Exemplo |
| ----- | ---- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código | var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## Porta do ponto de extremidade 

Recupera a porta do ponto de extremidade da instância.

| Tipo | Exemplo |
| ----- | ------- |
| XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código | var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |





## Exemplo

Aqui está um exemplo de uma função de trabalho que cria uma tarefa de inicialização com uma variável de ambiente chamada `TestIsEmulated` definida como o [valor @emulated xpath](#app-running-in-emulator).

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## Próximas etapas

Saiba mais sobre o arquivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).

Crie um pacote [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg).

Habilite a [área de trabalho remota](cloud-services-role-enable-remote-desktop.md) para uma função.

<!---HONumber=AcomDC_0323_2016-->