<properties
 pageTitle="Importação e exportação de identidades de dispositivo do Hub IoT | Microsoft Azure"
 description="Conceitos e trechos de código .NET para gerenciamento em massa de identidades de dispositivo do Hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Gerenciamento em massa de identidades de dispositivo do Hub IoT

Cada Hub IoT tem um registro de identidade do dispositivo que você pode usar para criar recursos de acordo com o dispositivo no serviço, como uma fila contendo mensagens em andamento da nuvem para o dispositivo, e para permitir acesso aos pontos de extremidade voltados para o dispositivo. Este artigo descreve como importar e exportar identidades de dispositivo em massa bidirecionalmente em um registro de identidade do dispositivo.

As operações de importação e exportação ocorrem no contexto de *Trabalhos*, que permitem aos usuários executar operações de serviço em massa em um Hub IoT.

A classe **RegistryManager** inclui os métodos **ExportDevicesAsync** e **ImportDevicesAsync** que usam a estrutura **Trabalho**. Esses métodos permitem exportar, importar e sincronizar todo o registro de dispositivo de um Hub IoT.

## O que são Trabalhos?

As operações de registro de identidade do dispositivo usam o sistema de **Trabalho** quando a operação:

*  Tem um tempo de execução potencialmente longo em comparação com as operações de tempo de execução padrão ou
*  Retorna uma grande quantidade de dados para o usuário.

Nesses casos, em vez de ter uma única chamada à API aguardando ou bloqueando o resultado da operação, a operação cria de modo assíncrono um **Trabalho** para que o Hub IoT e a operação retornem imediatamente um objeto **JobProperties**.

O trecho de código de C# a seguir mostra como criar um trabalho de exportação:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

Em seguida, você pode usar a classe **RegistryManager** para consultar o estado do **Trabalho** usando os metadados retornados **JobProperties**.

O trecho de código de C# a seguir mostra como verificar a cada cinco segundos para ver se o trabalho finalizou a execução:

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Exportar dispositivos

Use o método **ExportDevicesAsync** para exportar todo o registro de dispositivo de um Hub IoT para um contêiner de blobs do [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) usando uma [Assinatura de Acesso Compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

Esse método permite criar backups confiáveis das informações do dispositivo em um contêiner de blobs controlado por você.

O método **ExportDevicesAsync** exige dois parâmetros:

*  Uma *cadeia de caracteres* que contenha um URI de um contêiner de blobs. Esse URI deve conter um token SAS que conceda acesso de gravação ao contêiner. O trabalho cria um blob de blocos nesse contêiner para armazenar os dados do dispositivo de exportação serializados. O token SAS deve incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  Um *booliano* que indica se você deseja excluir chaves de autenticação dos dados de exportação. Se for **false**, as chaves de autenticação serão incluídas na saída de exportação; caso contrário, as chaves serão exportadas como **null**.

O seguinte trecho de código em C# mostra como iniciar um trabalho de exportação que inclui chaves de autenticação de dispositivo nos dados de exportação e, em seguida, pesquisar se houve a conclusão:

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

O trabalho armazena sua saída no contêiner de blobs fornecido como um blob de blocos com o nome **devices.txt**. Os dados da saída consistem em dados de dispositivo serializados JSON, com um dispositivo por linha.

Veja a seguir um exemplo dos dados de saída:

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se precisar de acesso a esses dados no código, você poderá desserializar facilmente esses dados usando a classe **ExportImportDevice**. O seguinte trecho de código de C# mostra como ler informações do dispositivo que foram anteriormente exportadas para um blob de blocos:

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  Você também pode usar o método **GetDevicesAsync** da classe **RegistryManager** para buscar uma lista de seus dispositivos. No entanto, essa abordagem tem um limite rígido de 1.000 no número de objetos de dispositivo que são retornados. O caso de uso esperado para o método **GetDevicesAsync** é para cenários de desenvolvimento como auxílio na depuração, não sendo recomendado para cargas de trabalho de produção.

## Importar dispositivos

O método **ImportDevicesAsync** na classe **RegistryManager** permite que você execute operações em massa de importação e sincronização em um registro de dispositivo do Hub IoT. Assim como ocorre com o método **ExportDevicesAsync**, o método **ImportDevicesAsync** usa a estrutura **Trabalho**.

Você deve ter cuidado ao usar o método **ImportDevicesAsync** porque, além de provisionar novos dispositivos no registro de identidade do dispositivo, ele também pode atualizar e excluir os dispositivos existentes.

> [AZURE.WARNING]  Uma operação de importação não pode ser desfeita. Você deve sempre fazer backup de seus dados existentes usando o método **ExportDevicesAsync** em outro contêiner de blobs antes de fazer alterações em massa no registro de identidade do dispositivo.

O método **ImportDevicesAsync** usa dois parâmetros:

*  Uma *cadeia de caracteres* que contém um URI de um contêiner de blobs do [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) como *entrada* para o trabalho. Esse URI deve conter um token SAS que conceda acesso de leitura ao contêiner. Esse contêiner deve conter um blob com o nome **devices.txt** que contém os dados de dispositivo serializados a serem importados no registro de identidade do dispositivo. Os dados de importação devem conter informações do dispositivo no mesmo formato JSON usado pelo trabalho **ExportImportDevice** ao criar um blob **devices.txt**. O token SAS deve incluir estas permissões:

    ```
    SharedAccessBlobPermissions.Read
    ```

*  Uma *cadeia de caracteres* que contém um URI de um contêiner de blobs do [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) como *saída* do trabalho. O trabalho cria um blob de blocos nesse contêiner para armazenar quaisquer informações de erro do **Trabalho** de importação concluído. O token SAS deve incluir estas permissões:
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  Os dois parâmetros podem apontar para o mesmo contêiner de blobs. Os parâmetros separados simplesmente permitem mais controle sobre seus dados, pois o contêiner de saída requer permissões adicionais.

O trecho de código de C# a seguir mostra como iniciar um trabalho de importação:

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## Comportamento de importação

É possível usar o método **ImportDevicesAsync** para executar as seguintes operações em massa no registro de identidade do dispositivo:

-   Registro em massa de novos dispositivos
-   Exclusões em massa dos dispositivos existentes
-   Alterações de status em massa (habilitar ou desabilitar dispositivos)
-   Atribuição em massa de novas chaves de autenticação de dispositivo
-   Nova geração automática em massa de chaves de autenticação de dispositivo

É possível executar qualquer combinação das operações acima em uma única chamada **ImportDevicesAsync**. Por exemplo, é possível registrar novos dispositivos e excluir ou atualizar dispositivos existentes ao mesmo tempo. Quando usado com o método **ExportDevicesAsync**, é possível migrar completamente todos os seus dispositivos de um hub IoT para outro.

Você pode controlar o processo de importação por dispositivo usando a propriedade opcional **importMode** nos dados de serialização de importação para cada dispositivo. A propriedade **importMode** tem as seguintes opções:

| importMode | Descrição |
| -------- | ----------- |
| **createOrUpdate** | Se não houver um dispositivo com a **id** especificada, isso significará que ele foi registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag**. |
| **create** | Se não houver um dispositivo com a **id** especificada, isso significará que ele foi registrado recentemente. <br/>Se o dispositivo já existir, um erro será gravado no arquivo de log. |
| **atualizar** | Se já existir um dispositivo com a **id** especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **updateIfMatchETag** | Se já existir um dispositivo com a **id** especificada, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **ETag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se não houver uma correspondência de **ETag**, um erro será gravado no arquivo de log. |
| **createOrUpdateIfMatchETag** | Se não houver um dispositivo com a **id** especificada, isso significará que ele foi registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **ETag**. <br/>Se não houver uma correspondência de **ETag**, um erro será gravado no arquivo de log. |
| **delete** | Se já existir um dispositivo com a **id** especificada, ele será excluído sem considerar o valor de **ETag**. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **deleteIfMatchETag** | Se já existir um dispositivo com a **id** especificada, ele será excluído somente se houver uma correspondência de **ETag**. Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se não houver uma correspondência de ETag, um erro será gravado no arquivo de log. |

> [AZURE.NOTE] Se os dados de serialização não definirem explicitamente um sinalizador **importMode** para um dispositivo, eles usarão **createOrUpdate** como padrão durante a operação de importação.

## Exemplo de importação de dispositivos – provisionamento de dispositivo em massa 

O exemplo de código de C# a seguir ilustra como gerar várias identidades de dispositivo que incluem chaves de autenticação, como gravar essas informações de dispositivo em um blob de blocos do armazenamento do Azure e como importar os dispositivos no registro de identidade do dispositivo:

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## Exemplo de importação de dispositivos – exclusão em massa

O exemplo de código a seguir mostra como excluir os dispositivos que você adicionou usando o exemplo de código anterior:

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## Obtendo o URI de SAS do contêiner


O exemplo de código a seguir mostra como gerar um [URI SAS](../storage/storage-dotnet-shared-access-signature-part-2.md) com as permissões de leitura, gravação e exclusão para um contêiner de blobs:

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## Próximas etapas

Neste artigo, você aprendeu a realizar operações em massa no registro de identidade do dispositivo em um Hub IoT. Você pode continuar explorando recursos do Hub IoT e outros cenários IoT nos seguintes artigos:

- [Criar um Hub IoT usando um programa](iot-hub-rm-template.md)
- [Métricas de uso do Hub IoT](iot-hub-metrics.md)
- [Monitoramento de operações do Hub IoT](iot-hub-operations-monitoring.md)

<!---HONumber=AcomDC_0504_2016-->