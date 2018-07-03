---
title: Implantar função do Azure com o Azure IoT Edge | Microsoft Docs
description: Implantar função do Azure em um dispositivo de borda como um módulo
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0445817f9ff403156025e38a1e14a3892a9a292b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031185"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules---preview"></a>Tutorial: Implantar o Azure Functions como módulos do IoT Edge - versão prévia

Use o Azure Functions para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial orienta você pela criação e implantação de uma função do Azure que filtra dados de sensor no dispositivo IoT Edge simulado criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:     

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure
> * Usar o VS Code e o Docker para criar uma imagem do Docker e publicá-la em um registro de contêiner 
> * Implantar o módulo do registro do contêiner para seu dispositivo IoT Edge
> * Exibir dados filtrados

>[!NOTE]
>Os módulos do Azure Functions no Azure IoT Edge são públicos na [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure criada neste tutorial filtra os dados de temperatura gerados pelo dispositivo e envia apenas mensagens upstream para o Hub IoT do Azure quando a temperatura está acima do limite especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

Para testar o módulo Functions criado neste tutorial, é preciso de um dispositivo IoT Edge. Você pode usar o dispositivo que você configurou no guia de início rápido do [Linux](quickstart-linux.md) ou [Windows](quickstart.md).

Tenha os seguintes pré-requisitos em seu computador de desenvolvimento: 
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para a extensão do Visual Studio Code (fornecido por OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para o Visual Studio Code.
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para o Visual Studio Code. 
* [SDK 2.1 do .NET Core](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) em seu computador de desenvolvimento. 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

    ![criar registro de contêiner](./media/tutorial-deploy-function/create-container-registry.png)

2. Nomeie o registro e escolha uma assinatura.
3. Para o grupo de recursos, é recomendável usar o mesmo nome do grupo de recursos que contém seu Hub IoT. Ao manter todos os recursos juntos no mesmo grupo, você pode gerenciá-los juntos. Por exemplo, ao excluir o grupo de recursos usado para teste, todos os recursos de teste contidos nesse grupo são excluídos. 
4. Defina o SKU como **Básico**e alterne **Usuário administrador** para **Habilitar**. 
5. Clique em **Criar**.
6. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-a-function-project"></a>Criar um projeto de função
As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.

1. Abra o Visual Studio Code.
2. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal Integrado**. 
2. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.
3. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.
3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para sua solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Azure Functions - C#** como o modelo de módulo. 
   4. Nomeie seu módulo **CSharpFunction**. 
   5. Especifique o Registro de Contêiner do Azure que você criou na seção anterior como o repositório de imagens para o seu primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com  **\<nome do registro\>.azurecr.io/csharpfunction**.

4. A janela do VS Code carregará seu espaço de trabalho da solução IoT Edge. Há uma pasta **.vscode**, uma pasta **modules**, um arquivo de modelo manifesto de implantação e um arquivo **.env**. Abra **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Substitua o conteúdo do arquivo pelo código a seguir:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get; set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

6. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar sua solução do IoT Edge

Na seção anterior, você criou uma solução do IoT Edge e adicionou um código a CSharpFunction que filtrará mensagens em que a temperatura relatada do computador estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviar por push para seu registro de contêiner.

1. Entre no Docker digitando o seguinte comando no terminal integrado do Visual Studio Code para que você possa enviar sua imagem de módulo para o ACR: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Use o nome de usuário e o servidor de logon que você copiou do seu Registro de Contêiner do Azure anteriormente. A senha será solicitada. Cole a senha no prompt e pressione **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no seu espaço de trabalho da solução IoT Edge. Este arquivo informa ao tempo de execução do IoT Edge quais módulos implantar em um dispositivo. Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

3. Encontre a seção **registryCredentials** no manifesto de implantação. Atualize **nome de usuário**, **senha**, e **endereço** com as credenciais de seu registro de contêiner. Esta seção fornece o tempo de execução do IoT Edge em sua permissão de dispositivo para extrair as imagens de contêiner armazenadas no seu registro privado. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado para git.

5. Salve o arquivo.

6. No gerenciador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usa as informações no modelo de implantação e gera um arquivo `deployment.json` em uma nova pasta **config**. Em seguida, ele executa dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam as funções e enviam por push para o registro de contêiner que você especificou ao inicializar a solução. 

## <a name="view-your-container-image"></a>Exibir sua imagem de contêiner

O Visual Studio Code gera uma mensagem de êxito quando sua imagem de contêiner é enviada por push para seu registro de contêiner. Caso deseje confirmar a operação com êxito por conta própria, é possível exibir a imagem no registro. 

1. No portal do Azure, navegue até seu registro de contêiner do Azure. 
2. Selecione **Repositórios**.
3. Você deve ver **csharpfunction** listada em seus repositórios. Selecione o repositório para ver mais detalhes.
4. Na seção **Marcas**, você deve ver **0.0.1-amd64**. Essa marca reflete a versão e a plataforma da imagem que você criou. Esses valores são definidos no arquivo **module.json**, na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

Você pode usar o portal do Azure para implantar o módulo do Functions em um dispositivo IoT Edge como foi feito no guia de início rápido, mas também pode implantar e monitorar os módulos do Visual Studio Code. As seções a seguir usam a extensão do Azure IoT Edge para o VS Code que estava listado nos pré-requisitos. Caso ainda não o tenha feito, instale-a agora. 

1. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

2. Pesquise e execute o comando **Azure: Entrar**. Siga as instruções para entrar na sua conta do Azure. 

3. Na paleta de comandos, pesquise e execute o comando **Hub IoT do Azure: Selecionar Hub IoT**. 

4. Selecione a assinatura que contém seu Hub IoT, depois selecione aquele que você deseja acessar.

5. No gerenciador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

6. Clique com o botão direito do mouse no nome do seu dispositivo IoT Edge e selecione **Criar implantação de dispositivo IoT Edge**. 

7. Navegue até a pasta de solução que contém CSharpFunction. Abra a pasta **config** e selecione o arquivo **deployment.json**. Clique em **Selecionar manifesto de implantação do Edge**.

8. Atualize a seção **Dispositivos Hub IoT do Azure**. Você deve ver o novo **CSharpFunction** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. 

   ![Exibir módulos implantados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode ver todas as mensagens que chegam ao seu Hub IoT ao executar **Hub IoT do Azure: iniciar monitoramento de mensagens D2C** na paleta de comandos.

Você também pode filtrar para ver todas as mensagens que chegam ao seu Hub IoT vindas de um dispositivo específico. Clique com o botão direito do mouse na seção **Dispositivos do Hub IoT do Azure** e selecione **Iniciar monitoramento de mensagens D2C**.

Para interromper o monitoramento de mensagens, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Remova o tempo de execução do serviço de IoT Edge baseado em sua plataforma do dispositivo IoT (Linux ou Windows).

#### <a name="windows"></a>Windows

Remova o tempo de execução do IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Exclua os contêineres que foram criados no seu dispositivo. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Remova o tempo de execução do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Exclua os contêineres que foram criados no seu dispositivo. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Remova o tempo de execução do contêiner.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do Azure Functions que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode saber mais sobre como [Desenvolver o Azure Functions com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-function.md). 

Siga para os próximos tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Localizar médias usando uma janela flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
