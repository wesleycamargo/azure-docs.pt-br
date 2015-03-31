<properties 
	pageTitle="Noções básicas sobre como os instantâneos acumulam cobranças" 
	description="Um guia para entender a cobrança de instantâneos de blob de armazenamento do Azure" 
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
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Noções básicas sobre como os instantâneos acumulam cobranças

Criar um instantâneo, que é uma cópia somente leitura de um blob, pode resultar em cobranças de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos desnecessários.

## Considerações importantes sobre cobrança

A lista a seguir inclui os principais pontos a considerar ao criar um instantâneo.

- As cobranças são incorridas para blocos exclusivos ou páginas, estejam eles no blob ou no instantâneo. Sua conta não incorre em cobranças adicionais para instantâneos associados a um blob até que você atualize o blob no qual eles se baseiam. Depois que atualizar o blob de base, ele divergirá dos instantâneos, e você será cobrado por blocos exclusivos ou páginas em cada blob ou instantâneo.

- Quando você substituir um bloco em um blob de bloco, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte em qualquer instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de página é atualizada com dados idênticos.

- Substituir um blob de bloco chamando os métodos UploadFile, UploadText, UploadStream ou UploadByteArray substitui todos os blocos nesse blob. Se você tiver um instantâneo associado a esse blob, todos os blocos no blob de base e instantâneo agora divergirão e você será cobrado por todos os blocos em ambos os blobs. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecerem idênticos.

- O serviço Blob do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Como cobranças se acumulam para blocos exclusivos, é importante considerar que a atualização de um blob que tem um instantâneo resultará em blocos exclusivos adicionais e cobranças adicionais.

> [AZURE.NOTE] As práticas recomendadas ditam que você gerencie instantâneos com cuidado para evitar cobranças extras. É recomendável que você gerencie instantâneos da seguinte maneira:

> - Exclua e recrie instantâneos associados a um blob sempre que você atualiza o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do seu aplicativo requer que você mantenha os instantâneos. Ao excluir e recriar os instantâneos do blob, você pode garantir que o blob e os instantâneos não divirjam.

> - Se você estiver mantendo instantâneos para um blob, evite chamar UploadFile, UploadText, UploadStream ou UploadByteArray para atualizar o blob, pois esses métodos substituem todos os blocos no blob. Em vez disso, atualize o menor número possível de blocos usando os métodos PutBlock e PutBlockList.


## Cenários de cobrança de instantâneo


Os cenários a seguir demonstram como cobranças se acumulam para um blob de bloco e seus instantâneos. 

No cenário 1, o blob de base não foi atualizado desde que o instantâneo foi tirado, portanto as cobranças incorrem apenas para blocos exclusivos 1, 2 e 3.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

No cenário 2, o blob de base foi atualizado, mas o instantâneo não tem. O bloco 3 foi atualizado e mesmo que ele contenha os mesmos dados e a mesma ID, não é igual ao bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

No cenário 3, o blob de base foi atualizado, mas o instantâneo não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos. Este cenário pode ocorrer se você estiver usando um método de atualização como UploadFile, UploadText, UploadFromStream ou UploadByteArray, pois esses métodos substituem todo o conteúdo de um blob.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
