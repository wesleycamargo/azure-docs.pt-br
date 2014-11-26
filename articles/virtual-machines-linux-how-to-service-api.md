<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Como usar a API de Gerenciamento de Serviços

## Inicialização

Para invocar a API de gerenciamento de serviços do Azure IaaS do NodeJS, o módulo `azure` é usado.

    var azure = require('azure');

Primeiro, crie uma instância de ServiceManagementService. Todas as chamadas para a API usarão esse objeto. A ID da assinatura, as credenciais e a identificação de conexão são estabelecidas neste momento. Para gerenciar mais de uma assinatura, crie mais de um ServiceManagementService.

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid é uma cadeia de caracteres obrigatória. Ela deve ser a ID de assinatura da conta que está sendo acessada.
-   Auth é um objeto opcional que especifica a chave privada e o certificado público a serem usados com essa conta.

    -   keyfile - caminho do arquivo .pem que tem a chave privada. Ignorado se keyvalue for especificado.
    -   keyvalue - o valor real da chave privada, conforme armazenado em um arquivo .pem.
    -   certfile - caminho do arquivo .pem que tem o certificado público. Ignorado se cervalue for especificado.
    -   certvalue - o valor real do certificado público, conforme armazenado em um arquivo .pem.
    -   Se os valores acima não forem especificados, os valores das variáveis de ambiente de processo `CLIENT_AUTH_KEYFILE` e `CLIENT_AUTH_CERTFILE` serão lidos e usados. Se eles não estiverem definidos, os valores padrão dos arquivos serão tentados: priv.pem e pub.pem.
    -   Se não for possível carregar a chave privada e o certificado público, um erro será gerado.
-   Options é um objeto opcional que pode ser usado para controlar as propriedades usadas pelo cliente.

    -   host - nome de host do servidor de gerenciamento do Azure. Se não for especificado, ele usará o host padrão.
    -   apiversion - cadeia de caracteres de versão a ser usada no cabeçalho HTTP. Se não for especificada, a versão padrão será usada.
    -   serializetype - pode ser XML ou JSON. Se não for especificado, a serialização padrão será usada.

Opcionalmente, um proxy de túnel poderá ser usado para habilitar a solicitação HTTPS para passar por um proxy. Quando o IaasClient for criado, ele processará a variável de ambiente `HTTPS_PROXY`. Se ele for definido para uma URL válida, o nome do host e a porta serão analisados a partir da URL e usados nas solicitações subsequentes para identificar o proxy.

        iaasClient.SetProxyUrl(proxyurl)

O SetProxyUrl pode ser chamado para definir explicitamente o host do proxy e a porta. Ele tem o mesmo efeito que a configuração da variável de ambiente `HTTPS_PROXY` e substituirá a configuração de ambiente.

## Retornos de chamada

Todas as APIs têm um argumento de retorno de chamada obrigatório. A conclusão da solicitação é sinalizada chamando a função passada no retorno de chamada.

    callback(rsp)

-   O retorno de chamada tem um único parâmetro que é o objeto de resposta.
-   isSuccessful - true ou false
-   statusCode - código de status HTTP da resposta
-   response - a resposta analisada em um objeto javascript. Defina se isSuccessful é true.
-   error - um objeto javascript que armazenará informações de erro definidas se isSuccessful for false.
-   body - o corpo real da resposta como uma cadeia de caracteres
-   headers - os cabeçalhos HTTP reais da resposta
-   reqopts - as opções da solicitação HTTP do nó usadas para fazer a solicitação.

Observe que, em alguns casos, a conclusão pode indicar apenas que a solicitação foi aceita. Nesse caso, use **GetOperationStatus** para obter o status final.

## APIs

**iaasClient.GetOperationStatus(solicitado, callback)**

-   Várias chamadas de gerenciamento retornam antes que a operação seja concluída. Elas retornam o valor 202 Aceito e colocam um retorno solicitado no cabeçalho HTPP ms-request-id. Para monitorar a conclusão da solicitação, use essa API e passe o valor solicitado.
-   callback é obrigatório.

**iaasClient.GetOSImage(imagename, callback)**

-   imagename é um nome de cadeia de caracteres obrigatório da imagem.
-   callback é obrigatório.
-   O objeto de resposta conterá propriedades da imagem nomeada se for bem-sucedido.

**iaasClient.ListOSImages(callback)**

-   callback é obrigatório.
-   O objeto de resposta conterá uma matriz de objetos de imagem nomeada se for bem-sucedido.

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imagename é um nome de cadeia de caracteres obrigatório da imagem.
-   mediaLink é um nome de cadeia de caracteres obrigatório do mediaLink a ser usado.
-   imageOptions é um objeto opcional. Ele pode conter essas propriedades

    -   Categoria
    -   Label - padrão para imageName se não estiver definida.
    -   Local
    -   RoleSize
-   callback é obrigatório. (Se imageOptions não estiver definida, esse poderá ser o terceiro parâmetro.)
-   O objeto de resposta conterá propriedades da imagem criada se for bem-sucedido.

**iaasClient.ListHostedServices(callback)**

-   callback é obrigatório.
-   O objeto de resposta conterá uma matriz de objetos de serviço hospedado se for bem-sucedido.

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   callback é obrigatório.
-   O objeto de resposta conterá propriedades do serviço hospedado se for bem-sucedido.

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   serviceOptions é um objeto opcional. Ele pode conter essas propriedades

    -   Description - padrão para 'Service host'
    -   Label - padrão para serviceName se não estiver definida.
    -   Location - a região para criar o serviço
-   callback é obrigatório.

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   callback é obrigatório.
-   O objeto de resposta conterá chaves de acesso de armazenamento se for bem-sucedido.

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   callback é obrigatório.
-   O objeto de resposta conterá propriedades da implantação nomeada se for bem-sucedido.

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentSlot é um nome de cadeia de caracteres obrigatório do slot (Staged ou Production).
-   callback é obrigatório.
-   O objeto de resposta conterá propriedades das implantações no slot se for bem-sucedido.

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   VMRole é um objeto obrigatório que tem propriedades da função a ser criada para a implantação.
-   deployOptions é um objeto opcional. Ele pode conter essas propriedades

    -   DeploymentSlot - padrão para 'Production
    -   Label - padrão para deploymentName se não estiver definida.
    -   UpgradeDomainCount - sem padrão
-   callback é obrigatório.

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   callback é obrigatório.
-   O objeto de resposta conterá propriedades da função nomeada se for bem-sucedido.

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   VMRole é um objeto obrigatório que tem propriedades da função a ser adicionada à implantação.
-   callback é obrigatório.

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   VMRole é um objeto obrigatório que tem propriedades a serem modificadas na função.
-   callback é obrigatório.

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   callback é obrigatório.

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   Datadisk é um objeto obrigatório usado para especificar como o disco de dados será criado.
-   callback é obrigatório.

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   LUN é o número que identifica o disco de dados
-   Datadisk é um objeto obrigatório usado para especificar como o disco de dados será modificado.
-   callback é obrigatório.

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleName é um nome de cadeia de caracteres obrigatório da função.
-   LUN é o número que identifica o disco de dados
-   callback é obrigatório.

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleInstance é um nome de cadeia de caracteres obrigatório da instância da função.
-   callback é obrigatório.

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleInstance é um nome de cadeia de caracteres obrigatório da instância da função.
-   callback é obrigatório.

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName é um nome de cadeia de caracteres obrigatório do serviço hospedado.
-   deploymentName é um nome de cadeia de caracteres obrigatório da implantação.
-   roleInstance é um nome de cadeia de caracteres obrigatório da instância da função.
-   captOptions é um objeto obrigatório que define as ações de captura

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback é obrigatório.

## Objetos de dados

As APIs usam objetos como entrada ao criar ou modificar uma implantação, uma função ou um disco. Outras APIs retornarão objetos semelhantes em uma operação Get ou List.
Essa seção esboça as propriedades do objeto.
Deployment

-   Nome - cadeia de caracteres
-   DeploymentSlot - 'Staging' ou 'Production'
-   Status - cadeia de caracteres - apenas saída
-   PrivateID - cadeia de caracteres - apenas saída
-   Label - cadeia de caracteres
-   UpgradeDomainCount - número
-   SdkVersion - cadeia de caracteres
-   Locked - true ou false
-   RollbackAllowed - true ou false
-   RoleInstance - objeto - apenas saída
-   Role - objeto de VMRole
-   InputEndpointList - matriz de InputEndpoint

**VMRole**

-   RoleName - cadeia de caracteres. Obrigatório para Criar.
-   RoleSize - cadeia de caracteres. Opcional para Criar.
-   RoleType - cadeia de caracteres. Assume 'PersistentVMRole' como padrão se não for especificado em Criar.
-   OSDisk - objeto. Obrigatório para Criar.
-   DataDisks - matriz de objetos. Opcional para Criar.
-   ConfigurationSets - matriz de objetos de Configuração.

**RoleInstance**

-   RoleName - cadeia de caracteres
-   InstanceName - cadeia de caracteres
-   InstanceStatus - cadeia de caracteres
-   InstanceUpgradeDomain - número
-   InstanceFaultDomain - número
-   InstanceSize - cadeia de caracteres
-   IpAddress - cadeia de caracteres

**OSDisk**

-   SourceImageName - cadeia de caracteres. Obrigatório para Criar.
-   DisableWriteCache - true ou false
-   DiskName - cadeia de caracteres.
-   MediaLink - cadeia de caracteres

**DataDisk**

-   DisableReadCache - true ou false
-   EnableWriteCache - true ou false
-   DiskName - cadeia de caracteres
-   MediaLink - cadeia de caracteres
-   LUN - número (0-15)
-   LogicalDiskSizeInGB - número
-   SourceMediaLink - cadeia de caracteres
-   Há 3 maneiras de especificar o disco durante a criação - pelo nome, pela mídia ou pelo tamanho. As opções especificadas determinarão seu funcionamento. Consulte o documento da API de RDFE para obter detalhes.

**ProvisioningConfiguration**

-   ConfigurationSetType - 'ProvisioningConfiguration'
-   AdminPassword - cadeia de caracteres
-   MachineName - cadeia de caracteres
-   ResetPasswordOnFirstLogon - true ou false

**NetworkConfiguration**

-   ConfigurationSetType - 'NetworkConfiguration'
-   InputEndpoints - matriz de ExternalEndpoints

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   Nome
-   Port
-   Protocolo

## Exemplo de código

Este é um exemplo de código de javascript que cria um serviço hospedado e uma implantação e, em seguida, monitora o status de conclusão da implantação.

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
