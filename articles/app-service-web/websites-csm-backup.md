<properties
	pageTitle="Usar REST para fazer backup e restaurar aplicativos do Serviço de Aplicativo"
	description="Saiba como usar chamadas à API RESTful para fazer backup e restaurar um aplicativo no Serviço de Aplicativo do Azure"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="nicking"/>
# Usar REST para fazer backup e restaurar aplicativos do Serviço de Aplicativo

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

O backup dos [aplicativos do Serviço de Aplicativo](https://azure.microsoft.com/services/app-service/web/) pode ser feito como blobs no armazenamento do Azure. O backup também pode conter bancos de dados do aplicativo. Se o aplicativo for excluído por acidente, ou se precisar ser revertido para uma versão anterior, ele poderá ser restaurado a partir de qualquer backup anterior. Os backups podem ser realizados a qualquer momento e sob demanda ou podem ser agendados em intervalos adequados.

Este artigo explica como fazer backup e restaurar um aplicativo com solicitações de API RESTful. Se você quiser criar e gerenciar backups do aplicativo graficamente no Portal do Azure, confira [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## Introdução
Para enviar solicitações REST, você precisa saber o **nome**, o **grupo de recursos** e a **ID da assinatura** do aplicativo. Essas informações podem ser encontradas clicando no aplicativo, na folha **Serviço de Aplicativo** do [Portal do Azure](https://portal.azure.com). Para os exemplos deste artigo, vamos configurar o site **backuprestoreapiexamples.azurewebsites.net**. Ele está armazenado no grupo de recursos Default-Web-WestUS e está em execução em uma assinatura com a ID 00001111-2222-3333-4444-555566667777.

![Informações do site de exemplo][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## Fazer backup e restaurar uma API REST
Agora, mostraremos vários exemplos de como usar a API REST para fazer backup e restaurar um aplicativo. Cada exemplo inclui uma URL e um corpo de solicitação HTTP. A URL de exemplo contém espaços reservados entre chaves, por exemplo, {subscription-id}. Substitua-os por informações correspondentes ao seu aplicativo. Para referência, veja uma explicação de cada espaço reservado que aparece nas URLs de exemplo.

* subscription-id: a ID da assinatura do Azure que contém o aplicativo
* resource-group-name: o nome do grupo de recursos que contém o aplicativo
* name: nome do aplicativo
* backup-id: ID do backup do aplicativo

Para obter a documentação completa da API, incluindo vários parâmetros opcionais que podem ser incluídos na solicitação HTTP, consulte o [Gerenciador de Recursos do Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## Fazer backup de um aplicativo sob demanda
Para fazer backup de um aplicativo imediatamente, envie uma solicitação **POST** para **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Esta é a aparência da URL quando usamos nosso site de exemplo. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

Forneça um objeto JSON no corpo da solicitação para especificar qual conta de armazenamento deve ser usada para armazenar o backup. O objeto JSON deve ter uma propriedade chamada **storageAccountUrl**, que mantém uma [URL SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) concedendo acesso de gravação ao contêiner de Armazenamento do Azure que armazena o blob de backup. Se você quiser fazer backup de seus bancos de dados, também deverá fornecer uma lista contendo os nomes, tipos e cadeias de conexão dos bancos de dados a ser copiado em backup.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Um backup do aplicativo começa imediatamente após o recebimento da solicitação. O processo de backup pode demorar muito tempo para ser concluído. A resposta HTTP contém uma ID que você pode usar em outra solicitação para ver o status do backup. Veja um exemplo de corpo da resposta HTTP para nossa solicitação de backup.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] É possível encontrar mensagens de erro na propriedade de registro da resposta HTTP.

<a name="schedule-automatic-backups"></a>
## Agendar backups automáticos
Além de fazer backup de um aplicativo sob demanda, você também pode agendar a execução automática de um backup.

### Configurar um novo agendamento de backup automático
Para agendar um backup, envie uma solicitação **PUT** a **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Em nosso site de exemplo, a URL parecerá com a seguinte. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

O corpo da solicitação deve conter um objeto JSON que especifica a configuração do backup. Veja um exemplo com todos os parâmetros necessários.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Este exemplo configura o backup automático do aplicativo a cada sete dias. Os parâmetros **frequencyInterval** e **frequencyUnit** determinam juntos com que frequência os backups ocorrem. Os valores válidos para **frequencyUnit** são **hora** e **dia**. Por exemplo, para fazer backup de um aplicativo a cada 12 horas, defina frequencyInterval como 12 e frequencyUnit como hora.

Os backups antigos são removidos automaticamente da conta de armazenamento. Você pode controlar por quanto tempo os backups são mantidos definindo o parâmetro **retentionPeriodInDays**. Se você quiser sempre ter pelo menos um backup salvo, independentemente da idade dele, defina **keepAtLeastOneBackup** como true.

### Configurar o agendamento de backup automático
Para obter a configuração de backup de um aplicativo, envie uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

A URL de nosso site de exemplo é **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## Obter o status de um backup
Dependendo do tamanho do aplicativo, o backup pode demorar para ser concluído. Os backups também podem falhar, o tempo limite pode ser ultrapassado ou ocorrer parcialmente. Para ver o status de todos os backups de um aplicativo, envie uma solicitação **GET** para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Para ver o status de um backup específico, envie uma solicitação GET para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Em nosso site de exemplo, a URL parecerá com a seguinte. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

O corpo da resposta contém um objeto JSON parecido com este exemplo.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

O status de um backup é um tipo enumerado. Veja abaixo cada estado possível.

* 0 – InProgress: o backup foi iniciado, mas ainda não foi concluído.
* 1 – Failed: o backup não foi bem-sucedido.
* 2 – Succeeded: o backup foi concluído com êxito.
* 3 – TimedOut: o backup não foi concluído dentro do prazo e foi cancelado.
* 4 – Created: a solicitação de backup está na fila, mas ele não foi iniciado.
* 5 – Skipped: o backup não prosseguiu devido a uma agenda que disparou muitos backups.
* 6 – PartiallySucceeded: o backup foi bem-sucedido, mas alguns arquivos não foram copiados no backup, pois não puderam ser lidos. Isso geralmente ocorre devido a um bloqueio exclusivo dos arquivos.
* 7 – DeleteInProgress: a exclusão do backup foi solicitada, mas ele ainda não foi excluído.
* 8 - DeleteFailed: não foi possível excluir o backup. Isso pode acontecer porque a URL do SAS usada para criar o backup expirou.
* 9 – Deleted: o backup foi excluído com êxito.

<a name="restore-app"></a>
## Restaurar um aplicativo usando um backup
Se o seu aplicativo tiver sido excluído ou se quiser reverter o aplicativo para uma versão anterior, você poderá restaurar o aplicativo de um backup. Para invocar uma restauração, envie uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Em nosso site de exemplo, a URL parecerá com a seguinte. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

No corpo da solicitação, envie um objeto JSON que contém as propriedades da operação de restauração. Veja um exemplo que contém todas as propriedades necessárias:

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### Restaurar para um novo aplicativo
Talvez você queira criar um novo aplicativo ao restaurar um backup, em vez de substituir um aplicativo existente. Para fazer isso, altere a URL da solicitação para apontar para o novo aplicativo que você deseja criar e altere a propriedade **overwrite** em JSON para **false**.

<a name="delete-app-backup"></a>
## Excluir um backup de aplicativo
Se você quiser excluir um backup, envie uma solicitação **DELETE** para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Em nosso site de exemplo, a URL parecerá com a seguinte. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>
## Gerenciar a URL de SAS de um backup
O Serviço de Aplicativo do Azure tentará excluir o backup do Armazenamento do Azure usando a URL SAS que foi fornecida quando o backup foi criado. Se essa URL de SAS não for mais válida, o backup não será excluído por meio da API REST. No entanto, você pode atualizar a URL de SAS associada a um backup enviando uma solicitação **POST** para a URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Em nosso site de exemplo, a URL parecerá com a seguinte. **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

No corpo da solicitação, envie um objeto JSON que contém a nova URL de SAS. Aqui está um exemplo.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Por motivos de segurança, a URL de SAS associada a um backup não é retornada quando você envia uma solicitação GET para um backup específico. Se quiser exibir a URL de SAS associada a um backup, envie uma solicitação POST para a mesma URL acima. Inclua um objeto JSON vazio no corpo da solicitação. A resposta do servidor contém todas as informações do backup, incluindo sua URL de SAS.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_0810_2016-->