<properties 
	pageTitle="Serviços de Nuvem do Azure - definição de serviço e configuração do serviço - certificado XML" 
	description="Saiba como configurar um certificado na definição de serviço e arquivos de configuração." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# Configurar a definição de serviço e configuração para um certificado

Máquinas virtuais que estejam executando as funções de trabalho ou web podem ter certificados associados a elas. Depois de carregar o certificado no portal, você precisa configurar a definição de serviço (.csdef) e arquivos de configuração de serviço (.cscfg) para o certificado.

A máquina virtual pode acessar a chave privada do certificado depois que tiver sido instalada. Por isso, você talvez queira restringir o acesso a processos com permissões elevadas.

## Exemplo de definição de serviço

Aqui está um exemplo de um certificado definido na definição do serviço.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Permissões
As permissões (atributo `permisionLevel`) podem ser definidas como um dos seguintes:

| Valor da permissão | Descrição |
| ----------------  | ----------- |
| limitedOrElevated | **(Padrão)** Todos os processos de função podem acessar a chave privada. |
| elevado | Somente processos elevados podem acessar a chave privada.|

## Exemplo de configuração de serviço

Aqui está um exemplo de um certificado definido na configuração de serviço.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Observe** os atributos `name` de correspondência.

## Próximas etapas
Examine o esquema [XML de definição de serviço](https://msdn.microsoft.com/library/azure/ee758711.aspx) e o esquema [XML da configuração de serviço](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=July15_HO5-->