<properties 
	pageTitle="Criando um instantâneo de um Blob" 
	description="Um guia de introdução para a criação de instantâneos de Blob de armazenamento do Azure" 
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

# Criando um instantâneo de um Blob

Você pode criar um instantâneo de um blob. Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Quando um instantâneo tiver sido criado, ele pode ser lido, copiado ou excluído, mas não modificado. Os instantâneos fornecem uma maneira de fazer backup de um blob da maneira como ele aparece em um momento específico.

Um instantâneo de um blob tem o mesmo nome que o blob de base do qual o instantâneo é criado, com um valor de data e hora adicionado para indicar a hora em que o instantâneo foi tirado. Por exemplo, se o URI do blob de página é http://storagesample.core.blob.windows.net/mydrives/myvhd, o instantâneo URI será semelhante a http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z. Esse valor pode ser usado para referenciar o instantâneo para outras operações. Os instantâneos do blob compartilham seu URI e são diferenciados apenas por esse valor de data e hora. No código de biblioteca de cliente, a propriedade de instantâneo do blob retorna um valor de data e hora que identifica exclusivamente o instantâneo em relação a seu blob de base. Você pode usar esse valor para executar operações adicionais no instantâneo.

Um blob pode ter qualquer número de instantâneos. Os instantâneos persistem até que sejam excluído explicitamente o instantâneo A que não pode sobreviver além de seu blob de origem. Você pode enumerar os instantâneos associados a seu blob para acompanhar seus instantâneos atuais.

## Herdando propriedades

Quando você cria um instantâneo de um blob, as propriedades do sistema são copiadas no instantâneo com os mesmos valores. Esta lista mostra as propriedades do sistema copiadas para a biblioteca de cliente de armazenamento do .NET:

- ContentType 

- ContentEncoding 

- ContentLanguage

- Comprimento

- CacheControl

- ContentMd5 


Uma concessão associada ao blob de base não é copiada para o instantâneo. Os instantâneos não podem ser concedidos.

## Copiando instantâneos 

As operações de cópia que envolvem blobs e instantâneos seguem estas regras:

- Você pode copiar um instantâneo sobre seu blob de base. Promovendo um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob. O instantâneo permanece, mas sua origem é substituída por uma cópia que pode ser lida e gravada.

- Você pode copiar um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.

- Quando um blob de origem é copiado, todos os instantâneos do blob de origem não são copiados para o destino. Quando um blob de destino é substituído por uma cópia, todos os instantâneos associados ao blob de destino permanecem intactos sob o seu nome. 

- Quando você cria um instantâneo de um blob de bloco, a lista do bloco confirmado de blob também é copiada no instantâneo. Todos os blocos não confirmados não são copiados.

## Especificando uma condição de acesso 

Você pode especificar uma condição de acesso para que o instantâneo seja criado somente se uma condição for atendida. Para especificar uma condição de acesso, use a propriedade AccessCondition. Se a condição especificada não for atendida, o instantâneo não é criado e o serviço Blob retornará o código de status HTTPStatusCode.PreconditionFailed.

## Excluindo instantâneos 

Não é possível excluir um blob que tem instantâneos, a menos que os instantâneos também sejam excluídos. Você pode excluir um instantâneo individualmente ou informar ao serviço de armazenamento para excluir todos os instantâneos ao excluir o blob de origem. Se você tentar excluir um blob que ainda possui instantâneos, sua chamada retornará um erro.

## Instantâneos com armazenamento Premium
Usando instantâneos com armazenamento Premium, siga estas regras:

- O número de instantâneos por blob de página em uma conta de armazenamento Premium é limitado a 100. Se esse limite for excedido, a operação de blob de instantâneo retornará o código de erro 409 (SnapshotCountExceeded).

- Um instantâneo de um blob de página em uma conta de armazenamento Premium pode ser retirado uma vez a cada dez minutos. Se essa taxa for excedida, a operação de Blob de instantâneo retornará o código de erro 409 (SnaphotOperationRateExceeded).

- Não há suporte para a leitura de um instantâneo de um blob de página em uma conta de armazenamento Premium através do Blob de Get. Chamar o blob Get em um instantâneo em uma conta de armazenamento Premium retorna o código de erro 400 (operação inválida). No entanto, chamar propriedades de blob de Get e Metadados de blob de Get em um instantâneo é suportado.

- Para ler um instantâneo, você pode usar a operação de cópia de Blob para copiar um instantâneo para outro blob de página na conta. O blob de destino da operação de cópia não deve ter todos os instantâneos existentes. Se o blob de destino tiver instantâneos, então Copiar Blob retornará o código de erro 409 (SnapshotsPresent).

## Construindo o URI absoluto para um instantâneo 

Este exemplo de código constrói o URI absoluto de um instantâneo de seu objeto blob de base.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
