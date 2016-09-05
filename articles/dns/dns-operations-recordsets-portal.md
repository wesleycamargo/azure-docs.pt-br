<properties
   pageTitle="Gerenciar registros e conjuntos de registros DNS usando o Portal do Azure| Microsoft Azure"
   description="Gerenciando registros e conjuntos de registros DNS ao hospedar seu domínio no DNS do Azure."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Gerenciar registros e conjuntos de registros DNS usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [CLI do Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artigo mostra como gerenciar registros e conjuntos de registros da zona DNS usando o portal do Azure.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é uma coleção de registros em uma zona que tem o mesmo nome e o mesmo tipo. Para obter mais informações, veja [Criar registros e conjuntos de registros DNS usando o portal do Azure](dns-getstarted-create-recordset-portal.md).

## Criar registro e um novo conjunto de registros

Para criar um conjunto de registros no portal do Azure, veja [Create DNS records using the Azure portal](dns-getstarted-create-recordset-portal.md) (Criar registros DNS usando o Portal do Azure).


## Exibir um conjunto de registros

1. No portal do Azure, vá para a folha **Zona DNS**.

2. Procure o conjunto de registros e selecione-o. Isso abrirá as propriedades do conjunto de registros.

	![Pesquisar um conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)


## Adicionar um novo registro a um conjunto de registros

Você pode adicionar até 20 registros em qualquer conjunto de registros. Um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome DNS do Azure. Os conjuntos de registros do tipo CNAME podem conter, no máximo, um registro.


1. Na folha **Propriedades do conjunto de registros** da zona DNS, clique no conjunto de registros ao qual você quer adicionar um registro.

	![Selecionar um conjunto de registros](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especifique as configurações do conjunto de registros preenchendo os campos.

	![Adicionar um registro](./media/dns-operations-recordsets-portal/addrecord500.png)

2. Clique em **Salvar** na parte superior da folha para salvar as configurações. Em seguida, feche a folha.

3. No canto, você verá que o registro está sendo salvo.

	![Salvando o conjunto de registros](./media/dns-operations-recordsets-portal/saving150.png)

Assim que o registro for salvo, os valores da folha **Zona DNS** refletirão o novo registro.


## Atualizar um registro

Ao atualizar um registro em um conjunto de registros existente, os campos disponíveis que poderão ser atualizados dependem do tipo de registro com o qual você está trabalhando.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.

2. Modifique o registro. Quando você modifica um registro, é possível alterar suas configurações disponíveis. No exemplo a seguir, o campo **endereço IP** é selecionado e o endereço IP está sendo modificado.

	![Modificar um registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações. No canto superior direito, você verá a notificação informando que o registro foi salvo.

	![Conjunto de registros salvo](./media/dns-operations-recordsets-portal/saved150.png)


Assim que o registro for salvo, os valores do conjunto de registros na folha **Zona DNS** refletirão o registro atualizado.


## Remover um registro de um conjunto de registros

Você pode usar o Portal do Azure para remover registros de um conjunto de registros. Observe que remover o último registro de um conjunto de registros não exclui o conjunto.

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, procure o registro.

2. Clique no registro que você quer remover. Em seguida, selecione **Remover**.

	![Remover um registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Clique em **Salvar** na parte superior da folha para salvar as configurações.

3. Assim que o registro for removido, os valores do registro na folha **Zona DNS** refletirão a remoção.


## <a name="delete"></a>Excluir um conjunto de registros

1. Na folha **Propriedades do conjunto de registros** do conjunto de registros, clique em **Excluir**.

	![Excluir um conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Será exibida uma mensagem perguntando se você deseja excluir o conjunto de registros.

3. Verifique se o nome corresponde ao conjunto de registros que você quer excluir e clique em **Sim**.

4. Na folha **Zona DNS**, você poderá verificar que o conjunto de registros não está mais visível.


## Trabalhar com registros NS e SOA

Os registros NS e SOA que são criados automaticamente são gerenciados de modo diferente de outros tipos de registro.

### Modificar registros SOA

Não é possível adicionar nem remover registros no conjunto de registros SOA criados automaticamente no apex da zona (nome = “@”). No entanto, é possível modificar qualquer um dos parâmetros no registro SOA (exceto o “Host”) e o TTL do conjunto de registros.

### Modificar registros NS no apex da zona

Não é possível adicionar, remover nem modificar os registros no conjunto de registros NS criados automaticamente no apex da zona (nome = “@”). A única alteração permitida é modificar o TTL do conjunto de registros.

### Excluir conjuntos de registros SOA ou NS

Não é possível excluir os conjuntos de registros SOA e NS no apex da zona (nome = “@”) criados automaticamente quando a zona é criada. Eles são excluídos automaticamente ao excluir a zona.

## Próximas etapas

-   Para obter mais informações sobre o DNS do Azure, confira [Visão geral do DNS do Azure](dns-overview.md).
-   Para obter mais informações sobre como automatizar o DNS, confira [Criando zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).
-   Para obter mais informações sobre registros DNS reversos, veja [Como gerenciar registros DNS reversos para seus serviços usando o PowerShell](dns-reverse-dns-record-operations-ps.md).

<!---HONumber=AcomDC_0824_2016-->