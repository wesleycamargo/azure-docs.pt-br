<properties 
pageTitle="Configuração de uma conexão de área de trabalho remota para uma função nos Serviços de Nuvem do Azure" 
description="Como configurar seu aplicativo de serviço de nuvem do Azure para permitir conexões de área de trabalho remota" 
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
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Configuração de uma conexão de área de trabalho remota para uma função no Azure
Depois de criar um serviço de nuvem que está executando o aplicativo, você pode acessar remotamente uma instância de função no aplicativo para configurar definições ou solucionar problemas. O serviço de nuvem deve ter sido configurado para a área de trabalho remota.

* Precisa de um certificado para habilitar a autenticação de área de trabalho remota? [Crie](cloud-services-certs-create.md) um e configure-o abaixo.
* Já tem uma configuração de área de trabalho remota para seu serviço de nuvem? [Conecte-se](#remote-into-role-instances) ao serviço de nuvem.

## Configurar uma conexão de área de trabalho remota para funções web ou funções de trabalho
Para habilitar uma conexão de área de trabalho remota para uma função web ou função de trabalho, você pode configurar a conexão no modelo de serviço para o aplicativo, ou você pode usar o Portal de Gerenciamento do Azure para configurar a conexão depois que as instâncias forem executadas.

### Configurar a conexão no modelo de serviço
O elemento **Imports** deve ser adicionado ao arquivo de definição de serviço que importa o módulo **RemoteAccess** e o módulo **RemoteForwarder** no modelo de serviço. Quando você cria um projeto do Azure usando o Visual Studio, os arquivos de modelo de serviço são criados para você.

O modelo de serviço consiste em um arquivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) e um arquivo [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg). O arquivo de definição é empacotado com os binários da função quando o aplicativo do serviço de nuvem é preparado para implantação. O arquivo ServiceConfiguration.cscfg é implantado com o pacote de aplicativos e é usado pelo Azure para determinar como o aplicativo deve ser executado.

Depois de criar seu projeto, você pode usar o Visual Studio para [habilitar uma conexão de área de trabalho remota](https://msdn.microsoft.com/library/gg443832.aspx).

Depois de configurar a conexão, o arquivo de definição de serviço deve ser semelhante ao exemplo a seguir com o elemento `<Imports>` adicionado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```

O arquivo de configuração de serviço deve ser semelhante ao exemplo a seguir com os valores que você forneceu ao configurar a conexão, observe os elementos `<ConfigurationSettings>` e `<Certificates>`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```

Quando você [empacota](cloud-services-model-and-package.md#cspkg) e [publica](cloud-services-how-to-create-deploy-portal.md) o aplicativo, você deve garantir que a caixa de seleção **Habilitar a área de trabalho remota para todas as funções** esteja selecionada. [Esse](https://msdn.microsoft.com/library/ff683672.aspx) artigo fornece listas de tarefas comuns relacionadas ao uso do Visual Studio e dos Serviços de Nuvem.

### Configurar a conexão em instâncias em execução
Na página Configurar o serviço de nuvem, você pode habilitar ou modificar as configurações de conexão de área de trabalho remota. Para obter mais informações, consulte [Configurar o acesso remoto para instâncias de função](cloud-services-how-to-configure.md).




## Remoto em instâncias de função
Para acessar instâncias de funções web ou funções de trabalho, você deve usar um arquivo de protocolo RDP. Você pode baixar o arquivo no Portal de Gerenciamento ou você pode recuperar programaticamente o arquivo.

### Baixe o arquivo RDP no portal de gerenciamento

Você pode usar as seguintes etapas para recuperar o arquivo RDP do Portal de Gerenciamento e, em seguida, usar a conexão de área de trabalho remota para se conectar à instância usando o arquivo:

1.  Na página Instâncias, selecione a instância e, em seguida, clique em **Conectar** na barra de comandos.
2.  Clique em **Salvar** para salvar o arquivo de protocolo RDP no computador local.
3.  Abra **Conexão de Área de Trabalho Remota**, clique em **Mostrar Opções** e, em seguida, clique em **Abrir**.
4.  Navegue até o local onde você salvou o arquivo RDP, selecione o arquivo, clique em **Abrir**e, em seguida, clique em **Conectar**. Siga as instruções para concluir a conexão.

### Use o PowerShell para obter o arquivo RDP
Você pode usar o cmdlet [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) para recuperar o arquivo RDP.

### Usar o Visual Studio para baixar o arquivo RDP
No Visual Studio, você pode usar o Gerenciador de Servidores para criar uma conexão de área de trabalho remota.

1.  No Gerenciador de Servidores, expanda o nó **Azure\\Serviços de Nuvem\\[nome do serviço de nuvem]**.
2.  Expanda **Preparo** ou **Produção**.
3.  Expanda a função individual.
4.  Clique em uma das instâncias de função, clique em **Conectar-se usando a área de trabalho remota...** e, em seguida, insira o nome de usuário e senha.

### Baixar programaticamente o arquivo RDP por meio da API REST do gerenciamento de serviços
Você pode usar a operação REST [Baixar arquivo RDP](https://msdn.microsoft.com/library/jj157183.aspx) para baixar o arquivo RDP. Em seguida, você pode usar o arquivo RDP com a conexão de área de trabalho remota para acessar o serviço de nuvem.

## Próximas etapas
Talvez você precise [empacotar](cloud-services-model-and-package.md) ou [carregar (implantar)](cloud-services-how-to-create-deploy-portal.md) seu aplicativo.

<!---HONumber=July15_HO4-->