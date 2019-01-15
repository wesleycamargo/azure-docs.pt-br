---
title: Tutorial para armazenar dados com o módulo do SQL – Azure IoT Edge | Microsoft Docs
description: Saiba como armazenar dados localmente no seu dispositivo IoT Edge com um módulo do SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: af33842b2006ceb94bd464d9e4148c9aa9206d7b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054989"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na borda com os bancos de dados do SQL Server

Use o Azure IoT Edge e o SQL Server para armazenar e consultar dados na borda. O Azure IoT Edge tem recursos de armazenamento básicos que armazenam mensagens em cache caso um dispositivo fique offline e depois as encaminha quando a conexão for restabelecida. No entanto, talvez você queira recursos mais avançados de armazenamento, como a capacidade de consultar dados localmente. Ao incorporar bancos de dados locais, seus dispositivos IoT Edge podem executar computações mais complexas sem a necessidade de manter uma conexão ao Hub IoT. Por exemplo, uma vez por mês, o sensor de um computador carrega dados para a nuvem para gerar relatórios e melhorar a um módulo de aprendizado de máquina. No entanto, se um técnico de campo estiver trabalhando no computador, ele pode acessar localmente os dados do sensor dos últimos dias.

Este artigo fornece instruções para a implantação de um banco de dados do SQL Server em um dispositivo IoT Edge. O Azure Functions, em execução no dispositivo IoT Edge, estruturam os dados de entrada e, em seguida, enviam para o banco de dados. As etapas neste artigo também podem ser aplicadas a outros bancos de dados que funcionam em contêineres, como o MySQL ou PostgreSQL.

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar uma função do Azure
> * Implantar um banco de dados SQL em seu dispositivo IoT Edge
> * Use o Visual Studio Code para criar módulos e implantá-los em seu dispositivo IoT Edge
> * Exibir os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md). 

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* Extensão [C# para Visual Studio Code (da plataforma OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) para o Visual Studio Code. 
* [Ferramentas de IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download). 
* [CE do Docker](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usará as Ferramentas IoT do Azure para Visual Studio Code para criar um módulo e uma **imagem de contêiner** com base nos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor | 
   | ----- | ----- |
   | Nome do registro | Forneça um nome exclusivo. |
   | Assinatura | Selecione uma assinatura na lista suspensa. |
   | Grupo de recursos | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Local padrão | Escolha um local perto de você. |
   | Usuário administrador | Definido como **Habilitar**. |
   | SKU | Selecione **Basic**. | 

5. Selecione **Criar**.

6. Depois que o registro de contêiner for criado, navegue até ele e escolha **Chaves de acesso**. 

7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. É possível usar esses valores posteriormente no tutorial para fornecer acesso ao registro de contêiner.  

## <a name="create-a-function-project"></a>Criar um projeto de função

Para enviar dados para um banco de dados, você precisa de um módulo que possa estruturar os dados corretamente e, depois, armazená-los em uma tabela. 

As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

1. Abra o Visual Studio Code.

2. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução, como **SqlSolution** ou aceite o padrão. |
   | Selecionar modelo do módulo | Escolha **Azure Functions – C#**. |
   | Fornecer um nome de módulo | Nomeie seu módulo como **sqlFunction**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. A cadeia de caracteres final se parece com \<nome do registro\>.azurecr.io/sqlFunction. |

   A janela do VS Code carregará seu workspace da solução IoT Edge. 
   
4. Abra o arquivo \.env na sua solução de IoT Edge. 

   Sempre que uma nova solução de IoT Edge for criada, o VS Code solicitará que você forneça suas credenciais de registro no arquivo \.env. Esse arquivo é ignorado pelo git, e a extensão de IoT Edge o usa mais tarde para fornecer acesso ao registro do dispositivo IoT Edge. 

   Se você não forneceu seu registro de contêiner na etapa anterior, mas aceitou o localhost:5000 padrão, você não terá um arquivo \.env.

5. No arquivo .env, dê suas credenciais de Registro ao tempo de execução do IoT Edge para que ele possa acessar as imagens do seu módulo. Localize as seções **CONTAINER_REGISTRY_USERNAME** e **CONTAINER_REGISTRY_PASSWORD** e insira suas credenciais após o símbolo de igual: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Salve o arquivo .env.

7. No gerenciador do VS Code, abra **modules** > **sqlFunction** > **sqlFunction.cs**.

8. Substitua o conteúdo do arquivo pelo código a seguir:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
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
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
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
   }
   ```

6. Na linha 35, substitua a cadeia de caracteres **\<sql connection string\>** pela cadeia de caracteres a seguir. A propriedade **Fonte de Dados** referencia o nome de contêiner do SQL Server, que será criado com o nome **SQL** na próxima seção. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Salve o arquivo **sqlFunction.cs**. 

8. Abra o arquivo **sqlFunction.csproj**.

9. Localize o grupo de referências de pacote e adicione um novo um para o SqlClient incluído. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. Salve o arquivo **sqlFunction.csproj**.

## <a name="add-a-sql-server-container"></a>Adicionar um contêiner do SQL Server

Um [Manifesto de implantação](module-composition.md) declara quais módulos o tempo de execução do IoT Edge instalará em seu dispositivo IoT Edge. Você forneceu o código para criar um módulo do Function personalizado na seção anterior, mas o módulo do SQL Server já foi criado. Você precisa solicitar que o tempo de execução do IoT Edge o inclua. Depois, configure-o em seu dispositivo. 

1. No gerenciador do Visual Studio Code, abra o arquivo **deployment.template.json**. 

2. Localize a seção de **módulos**. Deve haver dois módulos listados: **tempSensor**, que gera dados simulados, e seu módulo **sqlFunction**.

3. Se você estiver usando contêineres do Windows, modifique a seção **sqlFunction.settings.image**.

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. Adicione o seguinte código para declarar um terceiro módulo. Adicione uma vírgula após a seção sqlFunction e insira:

   ```json
   "sql": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "env":{},
     "settings": {
       "image": "",
       "createOptions": ""
     }
   }
   ```

   ![Adicionar o módulo do SQL Server ao manifesto](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. Dependendo do tipo de contêineres do Docker em seu dispositivo IoT Edge, atualize os parâmetros do módulo **sql** com o código a seguir:
   * Contêineres do Windows:

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "microsoft/mssql-server-windows-developer",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "C:\\\\mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   * Contêineres do Linux:

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "mcr.microsoft.com/mssql/server:latest",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   >[!Tip]
   >Sempre que você criar um contêiner do SQL Server em um ambiente de produção, será necessário [alterar a senha do administrador do sistema padrão](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Salve o arquivo **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Criar sua solução do IoT Edge

Nas seções anteriores, você criou uma solução com um módulo e depois adicionou outra ao modelo de manifesto de implantação. Agora, você precisa criar a solução, criar imagens de contêiner para os módulos e enviar por push as imagens para o registro de contêiner. 

1. Entre no seu Registro de contêiner no Visual Studio Code para que você possa efetuar o push das suas imagens para o Registro. Use as mesmas credenciais que você adicionou ao arquivo .env. Digite o seguinte comando no terminal integrado:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    Você receberá uma solicitação de senha. Cole sua senha no prompt (sua senha fica oculta para sua segurança) e pressione **Enter**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam o módulo e depois efetuam o push do código para o registro de contêiner que você especificou ao inicializar a solução. 

## <a name="deploy-the-solution-to-a-device"></a>Implantar a solução a um dispositivo

Você pode definir os módulos em um dispositivo por meio do Hub IoT, mas também pode acessar o Hub IoT e dispositivos por meio do Visual Studio Code. Nesta seção, você configura o acesso ao seu Hub IoT, depois usa o VS Code para implantar a solução ao seu dispositivo IoT Edge. 

1. Na paleta de comandos do VS Code, selecione **Hub IoT do Azure: selecionar Hub IoT**.

2. Siga os prompts para entrar na sua conta do Azure. 

3. Na paleta de comandos, selecione sua assinatura do Azure, depois o Hub IoT. 

4. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

5. Clique com o botão direito no dispositivo que deseja direcionar com sua implantação e selecione **Criar implantação para dispositivo único**. 

   ![Criar implantação para dispositivo único](./media/tutorial-store-data-sql-server/create-deployment.png)

6. No gerenciador de arquivos, navegue até a pasta **config** dentro de sua solução e escolha **deployment.amd64**. Clique em **Selecionar manifesto de implantação do Edge**. 

Se a implantação for bem-sucedida, uma mensagem de confirmação será impressa na saída do VS Code. 

Atualize o status do seu dispositivo na seção Dispositivos Azure Hub IoT do VS Code. Os novos módulos são listados e começarão a relatar como em execução dentro de alguns minutos, conforme os contêineres são instalados e iniciados. Você também pode verificar se todos os módulos estão em execução no seu dispositivo. No seu dispositivo IoT Edge, execute o comando a seguir para ver o status dos módulos. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Criar o banco de dados SQL

Ao aplicar o manifesto de implantação para seu dispositivo, você obtém três módulos em execução. O módulo tempSensor gera dados de ambiente simulado. O módulo sqlFunction obtém os dados e os formata para um banco de dados. Esta seção orienta-o através da configuração do banco de dados SQL para armazenar os dados de temperatura. 

Execute os seguintes comandos em seu dispositivo IoT Edge. Esses comandos fazem a conexão com o módulo **sql** em execução em seu dispositivo e criam um banco de dados e uma tabela para manter os dados de temperatura que estão sendo enviados a ele. 

1. Em uma ferramenta de linha de comando no dispositivo IoT Edge, faça a conexão com seu banco de dados. 
   * Contêiner do Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Contêiner do Linux: 

      ```bash
      sudo docker exec -it sql bash
      ```

2. Abra a ferramenta de comando SQL.
   * Contêiner do Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Contêiner do Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
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

   ![Exibir o conteúdo do banco de dados local](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do Azure Functions que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode saber mais sobre como [Desenvolver o Azure Functions com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-function.md). 

Siga para os próximos tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados do sensor usando código C#](tutorial-csharp-module.md)
