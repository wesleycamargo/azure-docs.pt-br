<properties
	pageTitle="Criar um instantâneo somente leitura de um blob | Microsoft Azure"
	description="Saiba como criar um instantâneo de um blob para fazer backup de dados de blob em um determinado momento. Entenda como é realizada a cobrança pelos instantâneos e como usá-los para minimizar os encargos de capacidade."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="jwillis;tamram"/>

# Criar um instantâneo de blob

## Visão geral

Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Os instantâneos são úteis para fazer backup de blobs. Depois de criar um instantâneo, você pode lê-lo, copiá-lo ou excluí-lo, mas não é possível modificá-lo.

O instantâneo de um blob é idêntico ao respectivo blob de base, exceto pelo fato de que o URI do blob tem um valor **DateTime** para indicar a hora em que o instantâneo foi criado. Por exemplo, se um URI de blob de páginas for `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o URI do instantâneo será semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [AZURE.NOTE] Todos os instantâneos compartilham o URI do blob de base. A única diferença entre o blob de base e o instantâneo é o acréscimo do valor **DateTime**.

Um blob pode ter qualquer número de instantâneos. Os instantâneos persistem até que sejam explicitamente excluídos. Um instantâneo não pode durar mais que seu blob de base. Você pode enumerar os instantâneos associados ao blob de base para acompanhar seus instantâneos atuais.

Quando você cria um instantâneo de um blob, as propriedades do sistema são copiadas para o instantâneo com os mesmos valores. Os metadados do blob de base também são copiados no instantâneo, a menos que você especifique metadados separados para o instantâneo ao criá-lo.

Quaisquer concessões associadas ao blob básico não afetam o instantâneo. Não é possível adquirir uma concessão em um instantâneo.

## Criar um instantâneo

O exemplo de código a seguir mostra como criar um instantâneo no .NET. Este exemplo especifica metadados separados para o instantâneo quando ele é criado.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## Copiar instantâneos

As operações de cópia que envolvem blobs e instantâneos seguem estas regras:

- Você pode copiar um instantâneo sobre seu blob de base. Promovendo um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob. O instantâneo permanece, mas a base de dados de blob é substituída por uma cópia gravável do instantâneo.

- Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável, e não um instantâneo.

- Quando um blob de origem é copiado, todos os instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído por uma cópia, todos os instantâneos associados ao blob de destino original permanecem intactos.

- Quando você cria um instantâneo de um blob de blocos, a lista de blocos confirmados do blob também é copiada no instantâneo. Todos os blocos não confirmados não são copiados.

## Especificar uma condição de acesso

Você pode especificar uma condição de acesso para que o instantâneo seja criado somente se uma condição for atendida. Para especificar uma condição de acesso, use a propriedade **AccessCondition**. Se a condição especificada não for atendida, o instantâneo não é criado e o serviço Blob retornará o código de status HTTPStatusCode.PreconditionFailed.

## Excluir instantâneos

Não é possível excluir um blob com instantâneos, a menos que os instantâneos também sejam excluídos. Você pode excluir um instantâneo individualmente ou especificar que todos os instantâneos sejam excluídos quando o blob de origem for excluído. Se você tentar excluir um blob que ainda possua instantâneos, um erro será gerado.

O exemplo de código a seguir mostra como excluir um blob e seus instantâneos no .NET, onde `blockBlob` é uma variável do tipo **CloudBlockBlob**:

	await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## Instantâneos com Armazenamento Premium do Azure

Usando instantâneos com armazenamento Premium, siga estas regras:

- O número máximo de instantâneos por blob de páginas em uma conta de armazenamento premium é 100. Se esse limite for excedido, a operação de Blob de Instantâneo retornará o código de erro 409 (**SnapshotCountExceeded**).

- Você pode criar um instantâneo de um blob de páginas em uma conta de armazenamento premium uma vez a cada 10 minutos. Se essa taxa for excedida, a operação de Blob de Instantâneo retornará o código de erro 409 (**SnaphotOperationRateExceeded**).

- Não é possível chamar Blob Get para ler um instantâneo de um blob de páginas em uma conta de armazenamento premium. Chamar o Blob Get em um instantâneo em uma conta de armazenamento Premium retorna o código de erro 400 (**InvalidOperation**). No entanto, você pode chamar as Propriedades de Blob Get e os Metadados de Blob Get em um instantâneo em uma conta de armazenamento premium.

- Para ler um instantâneo, você pode usar a operação de cópia de Blob para copiar um instantâneo para outro blob de páginas na conta. O blob de destino da operação de cópia não deve ter todos os instantâneos existentes. Se o blob de destino tiver instantâneos, a operação Copiar Blob retornará o código de erro 409 (**SnapshotsPresent**).

## Retornar o URI absoluto para um instantâneo

Este exemplo de código C# cria um instantâneo e grava o URI absoluto para o local principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
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

## Entender como instantâneos acumulam cobranças

Criar um instantâneo, que é uma cópia somente leitura de um blob, pode resultar em cobranças de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos desnecessários.

### Considerações importantes sobre cobrança

A lista a seguir inclui os principais pontos a considerar ao criar um instantâneo.

- Sua conta de armazenamento é cobrada por páginas ou blocos exclusivos, estejam eles no blob ou no instantâneo. Sua conta não incorre em cobranças adicionais para instantâneos associados a um blob até que você atualize o blob no qual eles se baseiam. Depois de atualizar o blob de base, ele divergirá dos respectivos instantâneos. Quando isso acontece, você é cobrado por páginas ou blocos exclusivos em cada blob ou instantâneo.

- Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte em qualquer instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com dados idênticos.

- Substituir um blob de blocos chamando os métodos **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** substitui todos os blocos no blob. Se você tiver um instantâneo associado a esse blob, todos os blocos no blob de base e o instantâneo agora divergirão, e você será cobrado por todos os blocos em ambos os blobs. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecerem idênticos.

- O serviço Blob do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Como cobranças se acumulam para blocos exclusivos, é importante considerar que a atualização de um blob que tenha um instantâneo resulta em blocos exclusivos adicionais e cobranças adicionais.

> [AZURE.NOTE] As práticas recomendadas ditam que você gerencie instantâneos com cuidado para evitar cobranças extras. É recomendável gerenciar instantâneos da seguinte maneira:

> - Exclua e recrie instantâneos associados a um blob sempre que você atualiza o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do seu aplicativo requer que você mantenha os instantâneos. Ao excluir e recriar os instantâneos do blob, você pode garantir que o blob e os instantâneos não divirjam.

> - Se você estiver mantendo instantâneos para um blob, evite chamar **UploadFile**, **UploadText**, **UploadStream** ou **UploadByteArray** para atualizar o blob. Esses métodos substituem todos os blocos no blob, de modo que o blob de base e os instantâneos divergem consideravelmente. Em vez disso, atualize o menor número possível de blocos usando os métodos **PutBlock** e **PutBlockList**.


### Cenários de cobrança de instantâneo


Os cenários a seguir demonstram como cobranças se acumulam para um blob de blocos e seus instantâneos.

No cenário 1, o blob de base não foi atualizado depois que o instantâneo foi tirado, portanto, as cobranças incorrem apenas para os blocos exclusivos 1, 2 e 3.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

No cenário 2, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi atualizado e mesmo que ele contenha os mesmos dados e a mesma ID, não é igual ao bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

No cenário 3, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos. Este cenário poderá ocorrer se você estiver usando um método de atualização como **UploadFile**, **UploadText**, **UploadFromStream** ou **UploadByteArray**, pois esses métodos substituem todo o conteúdo de um blob.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## Próximas etapas

Para obter exemplos adicionais usando o Armazenamento de Blobs, confira [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Você pode baixar um aplicativo de exemplo e executá-lo ou procurar o código no GitHub.

<!---HONumber=AcomDC_0928_2016-->