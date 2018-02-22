---
title: "Módulo do SQL do Azure IoT Edge | Microsoft Docs"
description: "Armazene dados na borda com módulos do Microsoft SQL, com o Azure Functions para formatar os dados."
services: iot-edge
keywords: 
author: ebertrams
manager: timlt
ms.author: kgremban, ebertrams
ms.date: 02/07/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f7ff069a2536d0138be8cbb32eefba342e1e9275
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Armazenar dados na borda com os bancos de dados do SQL Server

Utilize os dispositivos do Azure IoT Edge para armazenar os dados que são gerados na borda. Os dispositivos com conexões de internet intermitentes podem manter seus próprios bancos de dados e reportar alterações de volta para a nuvem somente quando conectado. Os dispositivos que foram programados para enviar somente dados críticos para a nuvem podem salvar o resto dos dados para uploads em massa regulares. Uma vez na nuvem, os dados estruturados poderão ser compartilhados com outros serviços do Azure, por exemplo, para compilar um modelo do Machine Learning. 

Este artigo fornece instruções para a implantação de um banco de dados do SQL Server em um dispositivo IoT Edge. O Azure Functions, em execução no dispositivo IoT Edge, estruturam os dados de entrada e, em seguida, enviam para o banco de dados. As etapas neste artigo também podem ser aplicadas a outros bancos de dados que funcionam em contêineres, como o MySQL ou PostgreSQL. 

## <a name="prerequisites"></a>pré-requisitos 

Antes de iniciar as instruções neste artigo, você deverá completar os seguintes tutoriais:
* Implantar o Azure IoT Edge em um dispositivo simulado no [Windows](tutorial-simulate-device-windows.md) ou no [Linux](tutorial-simulate-device-linux.md)
* [Implantar o Azure Functions como um módulo do IoT Edge](tutorial-deploy-function.md)

Os artigos a seguir não são necessários para concluir com êxito este tutorial, porém, podem fornecer um contexto útil:
* [Executar a imagem de contêiner do SQL Server 2017 com Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Usar o Visual Studio Code para desenvolver e implantar Azure Functions no Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Após concluir os tutoriais necessários, você deverá ter todos os pré-requisitos necessários no seu computador: 
* Um Hub IoT do Azure ativo.
* Um dispositivo IoT Edge com pelo menos 2 GB de RAM e uma unidade de disco de 2 GB.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Script de controle do IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Modelo AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Um hub IoT ativo com pelo menos um dispositivo do IoT Edge.

Ambos os contêineres do Windows e Linux nas arquiteturas de processadores x64 funcionam para este tutorial. O SQL Server não oferece suporte para processadores ARM.

## <a name="deploy-a-sql-server-container"></a>Implantar um contêiner do SQL Server

Nesta seção, você adicionará um banco de dados MS-SQL ao seu dispositivo IoT Edge simulado. Use a imagem de contêiner docker do SQL Server 2017, disponível em [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) e [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Implantar SQL Server 2017

Por padrão, o código nesta seção cria um contêiner com a edição de Desenvolvedor gratuita do SQL Server 2017. Em vez disso, se você quer executar edições de produção, consulte [Executar imagens de contêiner de produção](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) para obter informações detalhadas. 

Na etapa 3, você adicionará criar opções ao contêiner do SQL Server, que são importantes para estabelecer variáveis de ambiente e armazenamento persistente. As [variáveis de ambiente](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) configuradas aceitam o Contrato de Licença de Usuário Final e definem uma senha. O [armazenamento persistente](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) é configurado usando [montagens](https://docs.docker.com/storage/volumes/). As montagens criam o contêiner SQL Server 2017 com um contêiner de volume *sqlvolume* anexado para que seus dados persistam, mesmo que o contêiner seja excluído. 

1. Abra o arquivo `deployment.json` no Visual Studio Code. 
2. Substitua a seção **módulos** do arquivo pelo código a seguir: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "localhost:5000/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
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
        }
      }
   ```

3. Dependendo do sistema operacional em execução, atualize as configurações para o módulo do SQL com o código a seguir: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"sa_password=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "C:\\mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"MSSQL_SA_PASSWORD=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "/var/opt/mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

4. Salve o arquivo. 
5. Na Paleta de Comandos do VS Code, selecione **Edge: Criar implantação para dispositivo Edge**. 
6. Selecione a ID do dispositivo IoT Edge.
7. Selecione o arquivo `deployment.json` que foi atualizado. Você poderá ver as saídas correspondentes da sua implantação na janela de saída. 
8. Para iniciar o tempo de execução do Edge, selecione **Edge: Iniciar Edge** na Paleta de Comandos.

>[!TIP]
>Sempre que você criar um contêiner do SQL Server em um ambiente de produção, será necessário [alterar a senha do administrador do sistema padrão](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker.md#change-the-sa-password).

## <a name="create-the-sql-database"></a>Criar o banco de dados SQL

Esta seção orienta-o através da configuração do banco de dados SQL para armazenar os dados de temperatura recebidos dos sensores conectados ao dispositivo IoT Edge. Se você estiver usando um dispositivo simulado, esses dados provêm do contêiner *tempSensor*. 

Em uma ferramenta de linha de comando, faça a conexão com seu banco de dados: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```cmd
   Docker exec -it sql 'bash'
   ```

Abra a ferramenta de comando SQL: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```cmd
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Crie seu banco de dados: 

* Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Defina sua tabela: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Você pode personalizar seu arquivo docker do SQL Server para configurar automaticamente o SQL Server a ser implantado em vários dispositivos IoT Edge. Para obter mais informações, consulte o [projeto de demonstração do contêiner do Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Entender a conexão SQL

Em outros tutoriais, usamos rotas para permitir que os contêineres comuniquem-se enquanto permanecem isolados uns dos outros. Ao trabalhar com um Banco de Dados do SQL Server, é necessário uma relação mais estreita. 

O IoT Edge compila automaticamente uma ponte (Linux) ou rede NAT (Windows) quando é iniciado. A rede é chamada **azure-iot-edge**. Se for necessário depurar essa conexão, você poderá pesquisar as propriedades na linha de comando:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```cmd
   sudo docker network inspect azure-iot-edge
   ```

O IoT Edge também pode resolver o DNS de um nome de contêiner através do docker, portanto, não é necessário consultar o banco de dados do SQL Server por seu endereço IP. 

Como exemplo, aqui está a cadeia de conexão que usaremos na próxima seção: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

É possível ver que a cadeia de conexão faz referência ao contêiner pelo nome, **sql**. Se você alterou o nome do módulo por outro diferente, atualize também essa cadeia de conexão. Caso contrário, continue na próxima seção. 

## <a name="update-your-azure-function"></a>Atualizar o Azure Functions
Para enviar os dados ao banco de dados, atualize o FilterFunction do Azure Functions que você fez no tutorial anterior. Altere esse arquivo para que ele estruture os dados recebidos pelos respectivos sensores e depois armazene em uma tabela SQL. 

1. No Visual Studio Code, abra a pasta FilterFunction. 
2. Substitua o arquivo run.csx pelo código a seguir que inclui a cadeia de conexão do SQL da seção anterior: 

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
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
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

## <a name="update-your-container-image"></a>Atualizar a imagem do contêiner

Para aplicar as alterações realizadas, atualize a imagem do contêiner, publique-a e reinicie o IoT Edge.

1. No Visual Studio Code, expanda a pasta **Docker**. 
2. Com base na plataforma que está sendo utilizada, expanda a pasta **windows-nano** ou **linux-x64**. 
3. Clique com o botão direito do mouse no arquivo **Dockerfile** e selecione **Compilar a imagem do Docker do módulo do IoT Edge**.
4. Navegue até a pasta do projeto **FilterFunction** e clique em **Selecionar pasta como EXE_DIR**.
5. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo, `<your container registry address>/filterfunction:latest`. Se você estiver implantando em um registro local, o nome deverá ser `<localhost:5000/filterfunction:latest>`.
6. Na paleta de comandos do VS Code, selecione **Edge: Efetuar push de imagem do Docker do módulo do IoT Edge**. 
7. Na caixa de texto pop-up, insira o mesmo nome da imagem. 
8. Na paleta de comandos do VS Code, selecione **Edge: Reiniciar Edge**.

## <a name="view-the-local-data"></a>Exibir os dados locais

Quando os contêineres reiniciam, os dados recebidos dos sensores de temperatura são armazenados em um Banco de Dados do SQL Server 2017 local no dispositivo IoT Edge. 

Em uma ferramenta de linha de comando, faça a conexão com seu banco de dados: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```cmd
   Docker exec -it sql 'bash'
   ```

Abra a ferramenta de comando SQL: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```cmd
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Exibir os dados: 

   ```sql
   Select * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Próximas etapas

* Saiba como [configurar imagens de contêiner do SQL Server 2017 em Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Visite o [repositório mssql-docker GitHub](https://github.com/Microsoft/mssql-docker) para obter recursos, comentários e problemas conhecidos.