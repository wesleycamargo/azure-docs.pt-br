---
title: Criar um Hub IoT usando um modelo ARM e C# | Microsoft Docs
description: Siga este tutorial para começar a usar os modelos do Gerenciador de Recursos para criar um Hub IoT com um programa em C#.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: dobett

---
# Criar um Hub IoT usando um programa C# com um modelo do Resource Manager
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introdução
Você pode usar o Gerenciador de Recursos do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar um modelo do Resource Manager para criar um Hub IoT em um programa C#.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Microsoft Visual Studio 2015.
* Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
* Uma [conta de armazenamento do Azure][lnk-storage-account] na qual você possa armazenar seus arquivos de modelo.
* [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar seu projeto do Visual Studio
1. No Visual Studio, crie um projeto Windows do Visual C# usando o modelo de projeto de **Aplicativo do Console**. Nomeie o projeto **CreateIoTHub**.
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Gerenciar Pacotes NuGet**.
3. No Gerenciador de Pacotes Nuget, marque **Incluir pré-lançamento** e procure **Microsoft.Azure.Management.ResourceManager**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.
4. No Gerenciador de Pacotes do Nuget, procure **Microsoft.IdentityModel.Clients.ActiveDirectory**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar a licença.
5. Em Program.cs, substitua as instruções **using** existentes pelo seguinte:
   
    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
6. Em Program.cs, adicione as variáveis estáticas a seguir, substituindo os valores de espaço reservado. Você fez uma anotação de **ApplicationId**, **SubscriptionId**, **TenantId**, e **Password** anteriormente neste tutorial. **Nome de sua conta de armazenamento** é o nome da conta de armazenamento do Azure onde você armazena seus arquivos de modelo. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa ao criar o Hub IoT; ele pode ser um grupo de recursos pré-existente ou um novo. **Nome de implantação** é um nome para a implantação, como **Deployment\_01**.
   
    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Enviar um modelo para criar um hub IoT
Use um modelo JSON e um arquivo de parâmetros para criar um Hub IoT em seu grupo de recursos. Você também pode usar um modelo para fazer alterações em um hub IoT existente.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e em **Novo Item**. Adicione um arquivo JSON chamado **template.json** ao seu projeto.
2. Substitua o conteúdo do **template.json** pela seguinte definição de recurso para adicionar um Hub IoT padrão à região **Leste dos EUA** (para obter a lista atual de regiões que dão suporte ao Hub IoT, confira o [Status do Azure][lnk-status]):
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```
3. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e em **Novo Item**. Adicione um arquivo JSON chamado **parameters.json** ao seu projeto.
4. Substitua o conteúdo de **parameters.json** pelas seguintes informações de parâmetro que definem um nome para o novo Hub IoT, como **{suas iniciais}mynewiothub**. Observe que o nome do Hub IoT precisa ser globalmente exclusivo e, por isso, deve incluir seu nome e suas iniciais:
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
5. No **Gerenciador de Servidores**, conecte-se à sua assinatura do Azure e, em sua conta de armazenamento, crie um contêiner chamado **modelos**. No painel **Propriedades**, defina as permissões **Acesso de Leitura Público** para o contêiner **modelos** como **Blob**.
6. No **Gerenciador de Servidores**, clique com o botão direito do mouse no contêiner **modelos** e clique em **Exibir Contêiner de Blobs**. Clique no botão **Carregar Blob**, escolha os dois arquivos, **parameters.json** e **templates.json**, e clique em **Abrir** para carregar os arquivos JSON no contêiner **modelos**. As URLs dos blobs com os dados JSON são:
   
    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adicione o seguinte método ao Program.cs:
   
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
   
    }
    ```
8. Adicione o seguinte código ao método **CreateIoTHub** para enviar os arquivos de modelo e parâmetro ao Azure Resource Manager:
   
    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```
9. Adicione o seguinte código ao método **CreateIoTHub**, que exibe o status e as chaves do novo Hub IoT:
   
    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);
   
    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## Compilar e executar o aplicativo
Agora, você pode concluir o aplicativo chamando o método **CreateIoTHub** antes de compilá-lo e de executá-lo.

1. Adicione o seguinte código ao final do método **Main**:
   
    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
2. Clique em **Criar** e **Compilar Solução**. Corrija todos os erros.
3. Clique em **Depurar** e, em seguida, **Iniciar Depuração** para executar o aplicativo. Pode levar vários minutos para que a implantação seja executada.
4. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um Hub IoT Standard S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource**do PowerShell quando tiver terminado.
> 
> 

## Próximas etapas
Agora que você implantou um Hub IoT usando um modelo do Azure Resource Manager com um programa C#, convém explorar ainda mais:

* Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
* Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

Para saber mais sobre como desenvolver para o Hub IoT, consulte o seguinte:

* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Projetar sua solução][lnk-design]
* [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
* [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->