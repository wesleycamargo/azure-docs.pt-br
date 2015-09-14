<properties 
	pageTitle="Criando um instantâneo de um Blob"
	description="Um guia de introdução para a criação de instantâneos de Blob de Armazenamento do Azure"
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags 
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="tamram"/>

# Criar um instantâneo de blob

## Visão geral

Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Os instantâneos são úteis para fazer backup de blobs. Quando um instantâneo tiver sido criado, você pode ler, copiar ou excluí-lo, mas não modificá-lo.

Um instantâneo de um blob tem o mesmo nome que o blob de base do qual o instantâneo é criado, com um valor de **DateTime** adicionado para indicar a hora em que o instantâneo foi tirado. Por exemplo, se um URI de blob de páginas for `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o URI do instantâneo será semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. Todos os instantâneos do blob compartilham seu URI e são diferenciados apenas pelo valor **DateTime** acrescentado.

Um blob pode ter qualquer número de instantâneos. Os instantâneos persistem até que sejam explicitamente excluídos. Observe que um instantâneo não pode durar mais que seu blob de origem. Você pode enumerar os instantâneos associados a seu blob para acompanhar seus instantâneos atuais.

Quando você cria um instantâneo de um blob, as propriedades do sistema são copiadas para o instantâneo com os mesmos valores.

Quaisquer concessões associadas ao blob básico não afetam o instantâneo. Não é possível adquirir uma concessão em um instantâneo.

## Copiando instantâneos 

As operações de cópia que envolvem blobs e instantâneos seguem estas regras:

- Você pode copiar um instantâneo sobre seu blob de base. Promovendo um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob. O instantâneo permanece, mas a base de dados de blob é substituída por uma cópia gravável do instantâneo.

- Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável, e não um instantâneo.

- Quando um blob de origem é copiado, todos os instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído por uma cópia, todos os instantâneos associados ao blob de destino no momento em que ele foi substituído permanecem intactos sob seu nome.

- Quando você cria um instantâneo de um blob de blocos, a lista de blocos confirmados do blob também é copiada no instantâneo. Todos os blocos não confirmados não são copiados.

## Especificando uma condição de acesso 

Você pode especificar uma condição de acesso para que o instantâneo seja criado somente se uma condição for atendida. Para especificar uma condição de acesso, use a propriedade AccessCondition. Se a condição especificada não for atendida, o instantâneo não é criado e o serviço Blob retornará o código de status HTTPStatusCode.PreconditionFailed.

## Excluindo instantâneos 

Não é possível excluir um blob que tem instantâneos, a menos que os instantâneos também sejam excluídos. Você pode excluir um instantâneo individualmente ou informar ao serviço de armazenamento para excluir todos os instantâneos ao excluir o blob de origem. Se você tentar excluir um blob que ainda possua instantâneos, receberá um erro.

## Instantâneos com armazenamento Premium
Usando instantâneos com armazenamento Premium, siga estas regras:

- O número de instantâneos por blob de páginas em uma conta de armazenamento Premium é limitado a 100. Se esse limite for excedido, a operação de Blob de Instantâneo retornará o código de erro 409 (**SnapshotCountExceeded**).

- Um instantâneo de um blob de páginas em uma conta de armazenamento Premium pode ser retirado uma vez a cada dez minutos. Se essa taxa for excedida, a operação de Blob de Instantâneo retornará o código de erro 409 (**SnaphotOperationRateExceeded**).

- Não há suporte para a leitura de um instantâneo de um blob de páginas em uma conta de armazenamento Premium através do Blob de Get. Chamar o blob Get em um instantâneo em uma conta de armazenamento Premium retorna o código de erro 400 (**InvalidOperation**). No entanto, há suporte para chamar propriedades de blob de Get e Metadados de blob de Get em um instantâneo.

- Para ler um instantâneo, você pode usar a operação de cópia de Blob para copiar um instantâneo para outro blob de páginas na conta. O blob de destino da operação de cópia não deve ter todos os instantâneos existentes. Se o blob de destino tiver instantâneos, Copiar Blob retornará o código de erro 409 (**SnapshotsPresent**).

## Retornando o URI absoluto para um instantâneo 

Este exemplo de código C# cria um novo instantâneo e grava o URI absoluto para o local principal.

    //Create the blob service client object.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## Noções básicas sobre como os instantâneos acumulam cobranças

Criar um instantâneo, que é uma cópia somente leitura de um blob, pode resultar em cobranças de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos desnecessários.

### Considerações importantes sobre cobrança

A lista a seguir inclui os principais pontos a considerar ao criar um instantâneo.

- As cobranças são incorridas para blocos exclusivos ou páginas, estejam eles no blob ou no instantâneo. Sua conta não incorre em cobranças adicionais para instantâneos associados a um blob até que você atualize o blob no qual eles se baseiam. Depois que atualizar o blob de base, ele divergirá dos instantâneos e você será cobrado por blocos exclusivos ou páginas em cada blob ou instantâneo.

- Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte em qualquer instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com dados idênticos.

- Substituir um blob de blocos chamando os métodos UploadFile, UploadText, UploadStream ou UploadByteArray substitui todos os blocos nesse blob. Se você tiver um instantâneo associado a esse blob, todos os blocos no blob de base e instantâneo agora divergirão e você será cobrado por todos os blocos em ambos os blobs. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecerem idênticos.

- O serviço Blob do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Como cobranças se acumulam para blocos exclusivos, é importante considerar que a atualização de um blob que tem um instantâneo resultará em blocos exclusivos adicionais e cobranças adicionais.

> [AZURE.NOTE]As práticas recomendadas ditam que você gerencie instantâneos com cuidado para evitar cobranças extras. É recomendável que você gerencie instantâneos da seguinte maneira:

> - Exclua e recrie instantâneos associados a um blob sempre que você atualiza o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do seu aplicativo requer que você mantenha os instantâneos. Ao excluir e recriar os instantâneos do blob, você pode garantir que o blob e os instantâneos não divirjam.

> - Se você estiver mantendo instantâneos para um blob, evite chamar UploadFile, UploadText, UploadStream ou UploadByteArray para atualizar o blob, pois esses métodos substituem todos os blocos no blob. Em vez disso, atualize o menor número possível de blocos usando os métodos PutBlock e PutBlockList.


### Cenários de cobrança de instantâneo


Os cenários a seguir demonstram como cobranças se acumulam para um blob de blocos e seus instantâneos.

No cenário 1, o blob de base não foi atualizado desde que o instantâneo foi tirado, portanto as cobranças incorrem apenas para blocos exclusivos 1, 2 e 3.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

No Cenário 2, o blob de base foi atualizado, mas o instantâneo não. O bloco 3 foi atualizado e mesmo que ele contenha os mesmos dados e a mesma ID, não é igual ao bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

No cenário 3, o blob de base foi atualizado, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.
 
![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos. Este cenário pode ocorrer se você estiver usando um método de atualização como UploadFile, UploadText, UploadFromStream ou UploadByteArray, pois esses métodos substituem todo o conteúdo de um blob.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=September15_HO1-->