---
title: Implantar funções do Azure com o Azure IoT Edge | Microsoft Docs
description: Neste tutorial, você implantará uma função do Azure em um dispositivo de borda como um módulo.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d37e08f58986a1318e6b379d2efeb71bc58d4583
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413715"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Tutorial: implantar funções do Azure como módulos do IoT Edge (versão prévia)

Use o Azure Functions para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos Azure IoT Edge. Este tutorial o orienta através da criação e implantação de uma função do Azure que filtra dados do sensor em um dispositivo IoT Edge simulado. Use o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos do [Windows][lnk-tutorial1-win] ou do [Linux][lnk-tutorial1-lin]. Neste tutorial, você aprenderá como:     

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure.
> * Usar o VS Code e o Docker para criar uma imagem do Docker e publicá-la em um registro de contêiner.
> * Implantar o módulo do registro do contêiner para seu dispositivo IoT Edge.
> * Exibir dados filtrados.

>[!NOTE]
>Os módulos de funções do Azure no Azure IoT Edge são [públicos na versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure criada neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ela só envia mensagens upstream para o Hub IoT do Azure quando a temperatura estiver acima de um limite especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [O SDK 2.1 do .NET Core](https://www.microsoft.com/net/download).
* [CE do Docker](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

    ![Criar um registro de contêiner](./media/tutorial-deploy-function/create-container-registry.png)

2. Insira um nome para o registro e escolha uma assinatura.
3. Para o grupo de recursos, é recomendável usar o mesmo nome do grupo de recursos que contém seu Hub IoT. Ao manter todos os recursos juntos no mesmo grupo, você pode gerenciá-los juntos. Por exemplo, quando o grupo de recursos usado para testes é excluído, todos os recursos de teste contidos nesse grupo são excluídos. 
4. Defina o SKU como **Básico**e alterne **Usuário administrador** para **Habilitar**. 
5. Selecione **Criar**.
6. Depois que o registro de contêiner for criado, navegue até ele e escolha **Chaves de acesso**. 
7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-a-function-project"></a>Criar um projeto de função
As etapas a seguir criam uma função do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.

1. Abra o Visual Studio Code.
2. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal Integrado**. 
2. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.
3. Na paleta de comandos, insira e execute o comando **Azure: entrar**. Siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.
3. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: nova solução IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para sua solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Azure Functions - C#** como o modelo de módulo. 
   4. Nomeie seu módulo **CSharpFunction**. 
   5. Especifique o registro de contêiner do Azure que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com \<nome do registro\>.azurecr.io/csharpfunction.

4. A janela do VS Code carrega seu espaço de trabalho da solução IoT Edge: uma \.pasta vscode, uma pasta de módulos, um arquivo de modelo do manifesto de implantação. e um arquivo \.env. No gerenciador do VS Code, abra os **módulos** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

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
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages.
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

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **CSharpFunction** para filtrar mensagens em que a temperatura relatada do computador for menor do que o limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Entre no Docker inserindo o comando a seguir no terminal integrado do Visual Studio Code. Em seguida, envie sua imagem de módulo por push para o registro de contêiner do Azure: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Use o nome de usuário e o servidor de logon que você copiou do seu Registro de Contêiner do Azure anteriormente. Você receberá uma solicitação de senha. Cole a senha no prompt e pressione **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. No explorador do VS Code, abra o arquivo deployment.template.json no espaço de trabalho da solução IoT Edge. Este arquivo informa ao tempo de execução do IoT Edge quais módulos implantar em um dispositivo. Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

3. Encontre a seção **registryCredentials** no manifesto de implantação. Atualize **nome de usuário**, **senha**, e **endereço** com as credenciais de seu registro de contêiner. Esta seção fornece o tempo de execução do IoT Edge em sua permissão de dispositivo para extrair as imagens de contêiner armazenadas no seu registro privado. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git.

5. Salve o arquivo.

6. No explorador do VS Code, clique com o botão direito do mouse no arquivo deployment.template.json e selecione **Compilar solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam as funções e enviam o código por push para o registro de contêiner que você especificou ao inicializar a solução. 

## <a name="view-your-container-image"></a>Exibir sua imagem de contêiner

O Visual Studio Code gera uma mensagem de êxito quando sua imagem de contêiner é enviada por push para seu registro de contêiner. Caso deseje confirmar a operação com êxito por conta própria, é possível exibir a imagem no registro. 

1. No portal do Azure, navegue até seu registro de contêiner do Azure. 
2. Selecione **Repositórios**.
3. Você deve ver o repositório **csharpfunction** na lista. Escolhe este repositório para ver mais detalhes.
4. Na seção **Marcas**, você deve ver a marca **0.0.1-amd64**. Essa marca indica a versão e a plataforma da imagem que você compilou. Esses valores são definidos no arquivo module.json na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

Você pode usar o portal do Azure para implantar o módulo da função em um dispositivo IoT Edge como foi feito no guia de início rápido. Você também pode implantar e monitorar os módulos no Visual Studio Code. As seções a seguir usam a extensão do Azure IoT Edge para VS Code que estava listada nos pré-requisitos. Instale a extensão agora, caso ainda não tenha feito isso. 

1. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

2. Pesquise e execute o comando **Azure: Entrar**. Siga as instruções para entrar na conta do Azure. 

3. Na paleta de comandos, pesquise e execute o comando **Hub IoT do Azure: Selecionar Hub IoT**. 

4. Escolha a assinatura que contém seu Hub IoT e escolha o que você deseja acessar.

5. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

6. Clique com o botão direito do mouse no nome do seu dispositivo IoT Edge e escolha **Criar implantação de dispositivo IoT Edge**. 

7. Navegue até a pasta da solução que contém **CSharpFunction**. Abra a pasta de configuração, escolha o arquivo deployment.json e escolha **Selecionar manifesto de implantação do Edge**.

8. Atualize a seção **Dispositivos Hub IoT do Azure**. Você deve ver o novo **CSharpFunction** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. 

   ![Exibir módulos implantados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode ver todas as mensagens que chegam ao seu Hub IoT ao executar **Hub IoT do Azure: iniciar monitoramento de mensagens D2C** na paleta de comandos.

Você também pode filtrar o modo de exibição para ver todas as mensagens que chegam ao seu Hub IoT vindas de um dispositivo específico. Clique com o botão direito do mouse na seção **Dispositivos do Hub IoT do Azure** e selecione **Iniciar monitoramento de mensagens D2C**.

Para interromper o monitoramento de mensagens, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

Remova o tempo de execução do serviço do IoT Edge baseado em sua plataforma do dispositivo IoT (Linux ou Windows).

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

Neste tutorial, você criou um módulo de função do Azure com o código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para compilar seus próprios módulos, você pode saber mais sobre como [Desenvolver o Azure Functions com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-function.md). 

Siga para os próximos tutoriais para conhecer outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Localizar médias usando uma janela flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
