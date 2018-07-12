---
title: Armazenar dados com o módulo do SQL do Azure IoT Edge | Microsoft Docs
description: Saiba como armazenar dados localmente no seu dispositivo IoT Edge com um módulo do SQL Server
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d0837787dcac44d2cc43701ac181ec7eac2dfa2c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687208"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na borda com os bancos de dados do SQL Server

Use o Azure IoT Edge e o SQL Server para armazenar e consultar dados na borda. O Azure IoT Edge vem com recursos de armazenamento básicos internos que armazenam mensagens em cache se um dispositivo fica offline, depois encaminha-os quando a conexão é restabelecida. No entanto, talvez você queira recursos mais avançados de armazenamento, como a capacidade de consultar dados localmente. Ao incorporar bancos de dados locais, seus dispositivos IoT Edge podem executar computações mais complexas sem a necessidade de manter uma conexão ao Hub IoT. Por exemplo, um técnico de campo pode visualizar localmente em um computador os dados do sensor dos últimos dias, mesmo que esses dados sejam carregados somente para a nuvem uma vez por mês, para ajudar a aprimorar um modelo de aprendizado de máquina.

Este artigo fornece instruções para a implantação de um banco de dados do SQL Server em um dispositivo IoT Edge. O Azure Functions, em execução no dispositivo IoT Edge, estruturam os dados de entrada e, em seguida, enviam para o banco de dados. As etapas neste artigo também podem ser aplicadas a outros bancos de dados que funcionam em contêineres, como o MySQL ou PostgreSQL.

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure
> * Implantar um banco de dados SQL em seu dispositivo IoT Edge
> * Use o Visual Studio Code para criar módulos e implantá-los em seu dispositivo IoT Edge
> * Exibir os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos

* O dispositivo do Azure IoT Edge criado no início rápido para [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* [Visual Studio Code](https://code.visualstudio.com/). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download). 
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

Para enviar dados para um banco de dados, você precisa de um módulo que possa estruturar os dados corretamente e, depois, armazená-los em uma tabela. 

As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.

1. Abra o Visual Studio Code.
2. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal Integrado**.
3. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.
4. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.
3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 
   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para sua solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Azure Functions - C#** como o modelo de módulo. 
   4. Nomeie seu módulo como **sqlFunction**. 
   5. Especifique o Registro de Contêiner do Azure que você criou na seção anterior como o repositório de imagens para o seu primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com **\<nome do registro\>.azurecr.io/sqlFunction**.

4. A janela do VS Code carregará seu espaço de trabalho da solução IoT Edge. Há uma pasta **modules**, uma pasta **.vscode** e um arquivo de modelo do manifesto de implantação. Abra **modules** > **sqlFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

5. Substitua o conteúdo do arquivo pelo código a seguir:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

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

           //Store the data in SQL db
           const string str = "<sql connection string>";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
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
       public Machine machine {get;set;}
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

6. Na linha 24, substitua a cadeia de caracteres **\<sql connection string\>** pela cadeia de caracteres a seguir. A propriedade **Fonte de Dados** referencia o nome de contêiner do SQL Server, que será criado com o nome **SQL** na próxima seção. 

   ```C#
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Salve o arquivo **run.csx**. 

## <a name="add-a-sql-server-container"></a>Adicionar um contêiner do SQL Server

Um [Manifesto de implantação](module-composition.md) declara quais módulos o tempo de execução do IoT Edge instalará em seu dispositivo IoT Edge. Você adicionou o código para criar um módulo do Function personalizado na seção anterior, mas o módulo do SQL Server já foi criado. Você precisa solicitar que o tempo de execução do IoT Edge o inclua. Depois, configure-o em seu dispositivo. 

1. No gerenciador do Visual Studio Code, abra o arquivo **deployment.template.json**. 
2. Localize a seção **moduleContent.$edgeAgent.properties.desired.modules**. Deve haver dois módulos listados: **tempSensor**, que gera dados simulados, e seu módulo **sqlFunction**.
3. Adicione o seguinte código para declarar um terceiro módulo:

   ```json
   "sql": {
       "version": "1.0",
       "type": "docker",
       "status": "running",
       "restartPolicy": "always",
       "settings": {
           "image": "",
           "createOptions": ""
       }
   }
   ```

4. Dependendo do sistema operacional do seu dispositivo IoT Edge, atualize os parâmetros **sql.settings** com o código a seguir:

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

   >[!Tip]
   >Sempre que você criar um contêiner do SQL Server em um ambiente de produção, será necessário [alterar a senha do administrador do sistema padrão](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

5. Salve o arquivo **deployment.template.json**. 

## <a name="build-your-iot-edge-solution"></a>Criar sua solução do IoT Edge

Nas seções anteriores, você criou uma solução com um módulo e depois adicionou outra ao modelo de manifesto de implantação. Agora, você precisa criar a solução, criar imagens de contêiner para os módulos e enviar por push as imagens para o registro de contêiner. 

1. No arquivo deployment.template.json, dê ao tempo de execução do IoT Edge suas credenciais de registro para que ele possa acessar suas imagens de módulo. Localize a seção **moduleContent.$edgeAgent.properties.desired.runtime.settings**. 
2. Insira o seguinte código JSON após **loggingOptions**:

   ```JSON
   "registryCredentials": {
       "myRegistry": {
           "username": "",
           "password": "",
           "address": ""
       }
   }
   ```

3. Insira suas credenciais de registro nos campos **nome de usuário**, **senha**, e **endereço**. Use os valores que você copiou ao criar o Registro de Contêiner do Azure no início do tutorial.
4. Salve o arquivo **deployment.template.json**.
5. Entre no registro de contêiner no Visual Studio Code para que você possa enviar suas imagens por push para o registro. Use as mesmas credenciais que você acabou de adicionar ao manifesto de implantação. Digite o seguinte comando no terminal integrado: 

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    A senha será solicitada. Cole a senha no prompt e pressione **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

6. No gerenciador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar solução IoT Edge**. 

## <a name="deploy-the-solution-to-a-device"></a>Implantar a solução a um dispositivo

Você pode definir os módulos em um dispositivo por meio do Hub IoT, mas também pode acessar o Hub IoT e dispositivos por meio do Visual Studio Code. Nesta seção, você configura o acesso ao seu Hub IoT, depois usa o VS Code para implantar a solução ao seu dispositivo IoT Edge. 

1. Na paleta de comandos do VS Code, selecione **Hub IoT do Azure: selecionar Hub IoT**.
2. Siga os prompts para entrar na sua conta do Azure. 
3. Na paleta de comandos, selecione sua assinatura do Azure, depois o Hub IoT. 
4. No gerenciador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 
5. Clique com o botão direito do mouse no dispositivo que deseja direcionar com sua implantação e selecione **Criar implantação do dispositivo IoT Edge**. 
6. No gerenciador de arquivos, navegue até a pasta **config** dentro de sua solução e escolha **deployment.json**. Clique em **Selecionar manifesto de implantação do Edge**. 

Se a implantação for bem-sucedida, a mensagem de confirmação será impressa na saída do VS Code. Você também pode verificar se todos os módulos estão em execução no seu dispositivo. 

No seu dispositivo IoT Edge, execute o comando a seguir para ver o status dos módulos. Isso pode levar alguns minutos.

   ```bash
   sudo iotedge list
   ```

## <a name="create-the-sql-database"></a>Criar o banco de dados SQL

Ao aplicar o manifesto de implantação para seu dispositivo, você obtém três módulos em execução. O módulo tempSensor gera dados de ambiente simulado. O módulo sqlFunction obtém os dados e os formata para um banco de dados. 

Esta seção orienta-o através da configuração do banco de dados SQL para armazenar os dados de temperatura. 

1. Em uma ferramenta de linha de comando, faça a conexão com seu banco de dados. 
   * Contêiner do Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Contêiner do Linux: 

      ```bash
      docker exec -it sql bash
      ```

2. Abra a ferramenta de comando SQL.
   * Contêiner do Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

   * Contêiner do Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Password'
      ```

3. Crie seu banco de dados: 

   * Contêiner do Windows
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Contêiner do Linux
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Defina sua tabela.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Você pode personalizar seu arquivo docker do SQL Server para configurar automaticamente o SQL Server a ser implantado em vários dispositivos IoT Edge. Para obter mais informações, consulte o [projeto de demonstração do contêiner do Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Exibir os dados locais

Depois que a tabela é criada, o módulo sqlFunction começa a armazenar dados em um banco de dados do SQL Server 2017 local em seu dispositivo IoT Edge. 

De dentro da ferramenta de comando do SQL, execute o seguinte comando para exibir os dados da tabela formatada: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Exibir dados locais](./media/tutorial-store-data-sql-server/view-data.png)



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
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
```

#### <a name="linux"></a>Linux

Remova o tempo de execução do IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Exclua os contêineres que foram criados no seu dispositivo. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor")
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
