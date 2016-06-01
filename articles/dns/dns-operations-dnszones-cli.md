<properties 
   pageTitle="Gerenciar zonas DNS usando a CLI | Microsoft Azure" 
   description="Você pode gerenciar zonas DNS usando a CLI do Azure. Como atualizar, excluir e criar zonas DNS no DNS do Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Como gerenciar as zonas DNS usando a CLI

> [AZURE.SELECTOR]
- [CLI do Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


Este guia mostrará como gerenciar seus recursos de zona DNS usando a CLI do Azure de plataforma cruzada.

Essas instruções usam a CLI do Microsoft Azure. Certifique-se de atualizar para a versão mais recente da CLI (0.9.8 ou posterior) do Azure para usar os comandos do DNS do Azure. Digite `azure -v`para verificar qual versão da CLI do Azure está instalada atualmente em seu computador. Você pode instalar a CLI do Azure para Windows, Linux ou Mac. Mais informações estão disponíveis em [Instalar a CLI do Azure](../xplat-cli-install.md).

O Azure DNS é um serviço somente do Gerenciador de Recursos do Azure. Ele não tem uma API do ASM. Você precisará verificar se a CLI do Azure está configurada para usar o modo do Gerenciador de Recursos. Você pode fazer isso usando o comando `azure config mode arm`.<BR> Se você vir a mensagem *Erro: 'dns' não é um comando do Azure*, isso provavelmente significa que você está usando a CLI do Azure no modo ASM, não no modo do Gerenciador de Recursos.

## Criar uma nova zona DNS

Para criar uma nova zona DNS para hospedar seu domínio, consulte [Criar uma zona DNS do Azure usando a CLI](dns-getstarted-create-dnszone-cli.md).

## Obter uma zona DNS

Para recuperar uma zona DNS, use `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

A operação retorna uma zona DNS com sua id, o número de conjuntos de registros e marcas.


## Listar as zonas DNS

Para recuperar as zonas DNS em um grupo de recursos, use `azure network dns zone list`.

	azure network dns zone list myresourcegroup

## Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `azure network dns zone set`. Isso não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Isso é atualmente limitado às “marcas” do Gerenciador de Recursos do Azure para o recurso de zona. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Excluir uma zona DNS

As zonas DNS podem ser excluídas por meio do `azure network dns zone delete`. Esta operação tem um comutador opcional *-q* que suprime o prompt para confirmar que deseja remover a zona DNS.
 
Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

	azure network dns zone delete myresourcegroup contoso.com 



## Próximas etapas
Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) para iniciar a resolução de nomes do seu domínio da Internet.

<!---HONumber=AcomDC_0518_2016-->