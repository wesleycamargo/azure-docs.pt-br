<properties 
   pageTitle="Gerenciar registros e conjuntos de registros DNS usando o Portal do Azure| Microsoft Azure" 
   description="Gerenciando registros e conjuntos de registros DNS ao hospedar seu domínio no DNS do Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/29/2016"
   ms.author="cherylmc"/>

# Como criar e gerenciar registros e conjuntos de registros DNS usando o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [CLI do Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este guia mostrará como criar e gerenciar conjuntos de registros e registros da zona DNS usando o Portal do Azure.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é uma coleção de registros em uma zona com o mesmo nome e o mesmo tipo. Para obter mais detalhes, confira [Noções básicas sobre conjuntos de registros e registros](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Criar um novo conjunto de registros e um registro

Para criar um conjunto de registros no Portal do Azure, confira [Create DNS records using the Azure portal](dns-getstarted-create-recordset-portal.md) (Criar registros DNS usando o Portal do Azure).


## Exibir um conjunto de registros

1. No Portal do Azure, navegue até a folha da zona DNS.

2. Você pode procurar o conjunto de registros e selecioná-lo nos itens listados. Clique no conjunto de registros para selecioná-lo. Isso abrirá as propriedades do conjunto de registros.

	![pesquisar conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)


## Adicionar um novo registro a um conjunto de registros existente

Você pode adicionar até 20 registros em qualquer conjunto de registros. Um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome do DNS do Azure. Os conjuntos de registros do tipo CNAME podem conter, no máximo, um registro.


1. Na folha **Propriedades do conjunto de registros** da zona DNS, clique no conjunto de registros ao qual você quer adicionar um registro.

	![selecionar conjunto](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as configurações de registro preenchendo os campos.

	![adicionar registro](./media/dns-operations-recordsets-portal/addrecord500.png)

2. Clique em **Salvar** na parte superior da folha para salvar as configurações antes de fechar a folha.

3. No canto, você verá que o registro está sendo salvo.

	![salvar registro](./media/dns-operations-recordsets-portal/saving150.png)

4. Assim que o registro tiver sido salvo, os valores do conjunto de registros na folha DNS refletirão o novo registro.


## Atualizar um registro

Ao atualizar um registro em um conjunto de registros existente, os campos disponíveis que você pode atualizar dependem do tipo de registro com o qual você está trabalhando.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.

2. Modifique o registro. Quando você modifica um registro, é possível alterar as respectivas configurações disponíveis. No exemplo a seguir, clicamos no campo de endereço IP e o endereço IP está sendo modificado.

	![modificar registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações. Você verá a notificação de que o registro foi salvo no canto superior direito.

	
	![salvar registro](./media/dns-operations-recordsets-portal/saved150.png)



3. Assim que o registro tiver sido salvo, os valores do conjunto de registros na folha DNS refletirão o registro atualizado.



## Remover um registro de um conjunto de registros

Você pode usar o Portal do Azure para remover registros de um conjunto de registros. Observe que remover o último registro de um conjunto de registros não exclui o conjunto.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.

2. Clique no registro que você quer remover. Em seguida, clique em **Remover**.

	![remover registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações.

3. Assim que o registro tiver sido removido, os valores do conjunto de registros na folha DNS refletirão o registro que foi removido.


## <a name="delete"></a>Excluir um conjunto de registros

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, clique em **Excluir**. 

	![excluir conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Uma mensagem será exibida perguntando se você quer excluir o conjunto de registros 'nome\_ do \_ conjunto\_de\_registros'.

3. Verifique se o nome corresponde ao conjunto de registros que você quer excluir e clique em **Sim**.

4. Na folha da zona DNS, você poderá verificar que o conjunto de registros não está mais visível.


## Trabalhando com registros NS e SOA

Os registros NS e SOA que são criados automaticamente são gerenciados de modo diferente de outros tipos de registro.

### Modificando registros SOA

Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no apex da zona (nome = "@"), mas você pode modificar qualquer um dos parâmetros no registro SOA (exceto por "Host") e o TTL do conjunto de registros.

### Modificando registros NS no ápice da zona

Não é possível adicionar, remover ou modificar os registros no conjunto de registros NS criados automaticamente no apex da zona (nome = "@"). A única alteração permitida é modificar o TTL do conjunto de registros.

### Excluindo os conjuntos de registros SOA ou NS

Você não pode excluir os conjuntos de registro SOA e NS no apex da zona (nome = “@”) que são criados automaticamente quando a zona é criada. Eles serão excluídos automaticamente ao excluir a zona.

## Próximas etapas

Para saber mais sobre o DNS do Azure, confira [Visão geral do DNS do Azure](dns-overview.md). Para saber mais sobre como automatizar o DNS, confira [Criar zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).

<!---HONumber=AcomDC_0406_2016-->