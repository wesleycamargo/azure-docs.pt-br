<properties 
   pageTitle="Operações em zonas DNS usando a CLI | Microsoft Azure" 
   description="Você pode gerenciar zonas DNS usando a CLI do Azure. Como atualizar, excluir e criar zonas DNS no DNS do Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/02/2015"
   ms.author="joaoma"/>

# Como gerenciar as zonas DNS usando a CLI

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)

Este guia mostrará como gerenciar sua zona DNS. Ele ajudará a entender a sequência de operações a serem realizadas para administrar sua zona DNS.

## Criar uma nova zona DNS

Para criar uma nova zona DNS para hospedar seu domínio, use o `azure network dns zone create`:

		Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

A operação cria uma nova zona DNS no DNS do Azure. Opcionalmente, você pode especificar uma matriz de marcas do Gerenciador de Recursos do Azure. Para obter mais informações, consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags).

O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não deve existir, caso contrário, a operação falhará.

O mesmo nome de zona pode ser reutilizado em outro grupo de recursos ou uma assinatura do Azure diferente. Quando várias zonas compartilharem o mesmo nome, a cada instância serão atribuídos endereços de servidor de nome diferentes, e somente uma instância pode ser delegada do domínio pai. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

## Obter uma zona DNS

Para recuperar uma zona DNS, use o `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

A operação retorna uma zona DNS com sua id, o número de conjuntos de registros e marcas.


## Listar as zonas DNS

Para recuperar as zonas DNS dentro de um grupo de recursos, use o `azure network dns zone list`:

	azure network dns zone list myresourcegroup


## Atualizar uma zona DNS

As alterações a um recurso de zona DNS podem ser feitas usando o `azure network dns zone set`. Isso não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Isso é atualmente limitado às “marcas” do Gerenciador de Recursos do Azure para o recurso de zona. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o `azure network dns zone delete`.
 
Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

	azure network dns zone delete myresourcegroup contoso.com 

Esta operação tem um comutador opcional “-q” que suprime o prompt para confirmar que deseja remover a zona DNS.


## Próximas etapas


[Gerenciar registros DNS](dns-operations-recordsets-cli.md)

[Automatizar operações usando o SDK do .NET](dns-sdk.md)

<!---HONumber=Oct15_HO3-->