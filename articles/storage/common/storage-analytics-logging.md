---
title: Log de análise de armazenamento do Azure
description: Saiba como registrar em log os detalhes sobre solicitações feitas no armazenamento do Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 3daea17a491af8950b38f1e3183583fef63751e2
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926483"
---
# <a name="azure-storage-analytics-logging"></a>Log da análise de armazenamento do Azure

A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

 Log do Storage Analytics não está habilitado por padrão para sua conta de armazenamento. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [Monitorar uma conta de armazenamento no portal do Azure](/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use o [obter propriedades do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [obter propriedades do serviço fila](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), e [obter propriedades do serviço tabela](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) operações para habilitar a análise de armazenamento para cada serviço.

 As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades em seu ponto de extremidade de Blob, mas não em seus pontos de extremidade de tabela ou fila, somente os logs referentes ao serviço Blob serão criados.

> [!NOTE]
>  Log do Storage Analytics está disponível atualmente apenas para os serviços Blob, fila e tabela. No entanto, não há suporte para a conta de armazenamento premium.

## <a name="requests-logged-in-logging"></a>Solicitações registradas em log
### <a name="logging-authenticated-requests"></a>Solicitações de registro em log autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma assinatura de acesso compartilhado (SAS) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações de dados de análise

  As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

### <a name="logging-anonymous-requests"></a>Registro em log de solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e servidor
- Solicitações GET com falha com o código de erro 304 (Não Modificado)

  Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format) (Formato do log do Storage Analytics).

## <a name="how-logs-are-stored"></a>Como os logs são armazenados

Todos os logs são armazenados em blobs de blocos em um contêiner denominado `$logs`, que é criado automaticamente quando a análise de armazenamento está habilitada para uma conta de armazenamento. O `$logs` recipiente está localizado no namespace de blob da conta de armazenamento, por exemplo: `http://<accountname>.blob.core.windows.net/$logs`. Este contêiner não pode ser excluído quando a análise de armazenamento tiver sido habilitada, embora seu conteúdo possa ser excluído. Se você usar sua ferramenta de navegação de armazenamento para navegar diretamente até o contêiner, você verá todos os blobs que contêm dados de log.

> [!NOTE]
>  O `$logs` contêiner não é exibido quando uma operação de listagem do contêiner é executado, como a operação de lista de contêineres. Ele deve ser acessado diretamente. Por exemplo, você pode usar a operação de listar Blobs para acessar os blobs no `$logs` contêiner.

Como as solicitações são registradas, a análise de armazenamento carrega resultados intermediários como blocos. Periodicamente, a análise de armazenamento confirmará esses blocos e os disponibilizará como um blob. Pode demorar até uma hora para dados de log sejam exibidos em blobs na **$logs** contêiner porque a frequência em que o serviço de armazenamento libera os gravadores de log. Podem existir registros duplicados para os logs criados na mesma hora. Você pode determinar se um registro é uma duplicata verificando **RequestId** e o número da **Operação**.

Se você tiver um alto volume de dados com vários arquivos de log para cada hora, em seguida, você pode usar os metadados de blob para determinar quais dados o log contém examinando os campos de metadados de blob. Isso também é útil porque pode, às vezes, haver um atraso enquanto os dados são gravados nos arquivos de log: os metadados de blob fornece uma indicação mais precisa do conteúdo do blob que o nome do blob.

A maioria das ferramentas de navegação de armazenamento que você possa exibir os metadados de blobs; Você também pode ler essas informações usando o PowerShell ou programaticamente. O seguinte trecho do PowerShell é um exemplo de filtragem da lista de blobs de log usando o nome para especificar uma hora e pelos metadados para identificar apenas os logs que contêm **gravar** operações.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Para obter informações sobre como listar blobs de forma programática, consulte [enumerando recursos de Blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) e [configuração e recuperando propriedades e metadados para recursos de Blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Convenções de nomenclatura de log

 Cada log será gravado no seguinte formato:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A tabela a seguir descreve cada atributo no nome do log:

|Atributo|DESCRIÇÃO|
|---------------|-----------------|
|`<service-name>`|O nome do serviço de armazenamento. Por exemplo: `blob`, `table`, ou `queue`|
|`YYYY`|O ano de quatro dígitos para o log. Por exemplo: `2011`|
|`MM`|O mês de dois dígitos para o log. Por exemplo: `07`|
|`DD`|O dia de dois dígitos para o log. Por exemplo: `31`|
|`hh`|A hora de dois dígitos que indica a hora inicial dos logs, no UTC de 24 horas de formato. Por exemplo: `18`|
|`mm`|O número de dois dígitos que indica o minuto inicial dos logs. **Observação:**  Esse valor não é suportado na versão atual do Storage Analytics, e seu valor será sempre `00`.|
|`<counter>`|Um contador baseado em zero com seis dígitos que indica o número de blobs de log gerado para o serviço de armazenamento em um período de uma hora. Esse contador começa em `000000`. Por exemplo: `000001`|

 Este é um nome de log de exemplo completo que combina os exemplos anteriores:

 `blob/2011/07/31/1800/000001.log`

 Este é um exemplo de URI que pode ser usado para acessar o log anterior:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Quando uma solicitação de armazenamento estiver conectada, o nome do log resultante se correlaciona com a hora quando concluir a operação solicitada. Por exemplo, se uma solicitação GetBlob foi concluída às 6:30 PM 31/7/2011, o log seria gravado com o seguinte prefixo: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadados de log

 Todos os blobs de log são armazenados com metadados que podem ser usados para identificar os dados de log que contém o blob. A tabela a seguir descreve cada atributo de metadados:

|Atributo|DESCRIÇÃO|
|---------------|-----------------|
|`LogType`|Descreve se o log contém informações referentes a operações de ler, gravar ou de exclusão. Esse valor pode incluir um tipo ou uma combinação dos três, separados por vírgulas.<br /><br /> Exemplo 1: `write`<br /><br /> Exemplo 2: `read,write`<br /><br /> Exemplo 3: `read,write,delete`|
|`StartTime`|A primeira hora de uma entrada no log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:21:46Z`|
|`EndTime`|A hora mais recente de uma entrada no log, na forma de `YYYY-MM-DDThh:mm:ssZ`. Por exemplo: `2011-07-31T18:22:09Z`|
|`LogVersion`|A versão do formato do log.|

 A lista a seguir exibe exemplos de metadados que usam os exemplos anteriores:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Habilitar o log de armazenamento

Você pode habilitar o log de armazenamento com o portal do Azure, PowerShell e SDKs de armazenamento.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Habilitar o log de armazenamento usando o portal do Azure  

No portal do Azure, use o **as configurações de diagnóstico (clássico)** folha para controlar o log de armazenamento, acessível a partir de **monitoramento (clássico)** seção de uma conta de armazenamento **folha de Menu** .

Você pode especificar os serviços de armazenamento que você deseja fazer logon e o período de retenção (em dias) para os dados registrados.  

### <a name="enable-storage-logging-using-powershell"></a>Habilitar o log de armazenamento usando o PowerShell  

 Você pode usar o PowerShell no computador local para configurar o log de armazenamento na sua conta de armazenamento usando o cmdlet do PowerShell do Azure **Get-AzureStorageServiceLoggingProperty** para recuperar as configurações atuais e o cmdlet  **Set-AzureStorageServiceLoggingProperty** para alterar as configurações atuais.  

 Os cmdlets que controlam o log de armazenamento usam um **LoggingOperations** parâmetro que é uma cadeia de caracteres que contém uma lista separada por vírgulas dos tipos de solicitação para fazer logon. Os três tipos de solicitação possíveis são **leia**, **escrever**, e **excluir**. Para desativar o registro em log, use o valor **none** para o **LoggingOperations** parâmetro.  

 O seguinte comando ativa o registro em log para leitura, gravação e exclusão de solicitações no serviço de fila em sua conta de armazenamento padrão com retenção definido para cinco dias:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 O comando a seguir desativa o registro em log para o serviço de tabela em sua conta de armazenamento padrão:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Para saber mais sobre como configurar os cmdlets do Azure PowerShell para funcionar com sua assinatura do Azure e como escolher a conta de armazenamento padrão para usar, confira: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Habilitar o armazenamento de registro em log programaticamente  

 Além de usar o portal do Azure ou os cmdlets do PowerShell do Azure para controlar o log de armazenamento, você também pode usar uma das APIs de armazenamento do Azure. Por exemplo, se você estiver usando uma linguagem .NET, você pode usar a biblioteca de cliente de armazenamento.  

 As classes **CloudBlobClient**, **CloudQueueClient**, e **CloudTableClient** têm métodos como **SetServiceProperties** e **SetServicePropertiesAsync** que utilizam um **ServiceProperties** objeto como um parâmetro. Você pode usar o **ServiceProperties** objeto para configurar o log de armazenamento. Por exemplo, a seguinte C# trecho de código mostra como alterar o que é registrado e o período de retenção para o log de fila:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Para obter mais informações sobre como usar uma linguagem .NET para configurar o log de armazenamento, consulte [referência de biblioteca de cliente de armazenamento](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Para obter informações gerais sobre como configurar o log de armazenamento usando a API REST, consulte [habilitando e configurando a análise de armazenamento](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Baixe o log de dados de log de armazenamento

 Para exibir e analisar os dados de log, você deve baixar os blobs que contêm os dados de log que você está interessado em um computador local. Muitas ferramentas de navegação de armazenamento permitem baixar blobs de sua conta de armazenamento. Você também pode usar a equipe de armazenamento do Azure fornecida a ferramenta de linha de comando de cópia do Azure (**AzCopy**) para baixar os dados de log.  

 Para certificar-se de que baixar os dados de log que você está interessado e para evitar o download os mesmos dados de log mais de uma vez:  

-   Usar a data e a convenção de nomenclatura de hora para blobs contendo dados de log para controlar quais blobs você já baixou para análise evitar o download novamente os mesmos dados de mais de uma vez.  

-   Use os metadados nos blobs contendo dados de log para identificar o período específico para o qual o blob contém dados de log para identificar o blob exato que você precisa baixar.  

> [!NOTE]
>  AzCopy faz parte do SDK do Azure, mas você sempre pode baixar a versão mais recente do [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Por padrão, o AzCopy é instalado na pasta **C:\Program Files (x86) sdks\windows Azure\AzCopy**, e você deve adicionar essa pasta ao seu caminho antes de tentar executar a ferramenta em um prompt de comando ou a janela do PowerShell.  

 O exemplo a seguir mostra como você pode baixar os dados de log para o serviço de fila para as horas, começando em 09 AM, 10h e 11h forem em 20 de maio de 2014. O **/S** parâmetro faz com que o AzCopy criar uma estrutura de pasta local com base em datas e horários nos nomes de arquivo de log; a **/V** parâmetro faz com que o AzCopy produzir saída detalhada; o **/Y** parâmetro faz com que o AzCopy substituir todos os arquivos locais. Substitua **< yourstorageaccount\>**  pelo nome da sua conta de armazenamento e substitua **< yourstoragekey\>**  com sua chave de conta de armazenamento.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 O AzCopy também tem alguns parâmetros úteis que controlam como ele define a hora da última modificação em arquivos que baixa e se ele tentará baixar os arquivos que são mais antigos ou mais recente do que todos os arquivos que já existem em seu computador local. Você também pode executá-lo no modo reiniciável. Para obter detalhes completos, exiba a Ajuda executando o **AzCopy /?** comando.  

 Para obter um exemplo de como baixar os dados de log programaticamente, consulte o postagem no blog [log de armazenamento do Windows Azure: Usando Logs para rastrear solicitações de armazenamento](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) e procure a palavra "DumpLogs" na página.  

 Quando você tiver baixado os dados de log, você pode exibir as entradas de log nos arquivos. Esses arquivos de log usam um formato de texto delimitado que muitas log lendo as ferramentas são capazes de analisar, incluindo Microsoft Message Analyzer (para obter mais informações, consulte o guia [monitorando, diagnosticando e Solucionando problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Ferramentas diferentes têm recursos diferentes para formatação, filtragem, classificação, pesquisar o conteúdo dos arquivos de log do ad. Para obter mais informações sobre o formato de arquivo de log do log de armazenamento e o conteúdo, consulte [formato de Log de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format) e [operações registradas do Storage Analytics e mensagens de Status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Próximas etapas

* [Formato de Log de análise de armazenamento](/rest/api/storageservices/storage-analytics-log-format)
* [Mensagens de operações e status registradas de análise de armazenamento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métricas de análise de armazenamento (clássico)](storage-analytics-metrics.md)