<properties 
   pageTitle="Criar registros DNS personalizados para um aplicativo Web | Microsoft Azure" 
   description="Como criar registros DNS de domínio personalizado para o aplicativo Web usando o DNS do Azure. Passo a passo para verificar sua propriedade de domínio usando o registro CNAME ou A" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/23/2015"
   ms.author="joaoma"/>

# Criar registros DNS para o aplicativo Web em um domínio personalizado

Você pode usar o DNS do Azure para hospedar um domínio personalizado para seus aplicativos Web. Por exemplo, imagine que você está criando um aplicativo Web do Azure e deseja que os usuários o acessem usando contoso.com ou www.contoso.com como um FQDN. Nesse cenário, você precisaria criar dois registros: um registro de raiz A apontando para contoso.com e um registro CNAME para o nome www, apontando para o registro A.

> [AZURE.NOTE]Tenha em mente que, se você criar um registro A para um aplicativo Web no Azure, o registro A deve ser manualmente atualizado se o endereço IP subjacente para o aplicativo Web for alterado.

Antes de criar registros para seu domínio personalizado, você deve criar uma zona DNS no DNS do Azure e delegar a zona no seu registrador ao DNS do Azure. Para criar uma zona DNS, siga as etapas em [Introdução ao DNS do Azure](../dns-getstarted-create-dnszone/#Create-a-DNS-zone). Para delegar o DNS ao DNS do Azure, siga as etapas em [Delegar domínio ao DNS do Azure](../dns-domain-delegation).
 
## Criar um registro A para seu domínio personalizado

Um registro A é usado para mapear um nome para seu endereço IP. No exemplo a seguir, atribuiremos @ como um registro A para um endereço IPv4:

### Etapa 1
 
Crie um registro A e atribuir a uma variável $rs
	
	PS C:\>$rs=New-AzureDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 

### Etapa 2

Adicione um valor IPv4 a um conjunto de registros "@" criado anteriormente usando a variável $rs atribuída. O valor IPv4 atribuído será o endereço IP do seu aplicativo Web.

> [AZURE.NOTE]Para localizar o endereço IP para um aplicativo Web, siga as etapas em [Configurar um nome de domínio personalizado no serviço de aplicativo do Azure](../web-sites-custom-domain-name/#Find-the-virtual-IP-address)

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### Etapa 3

Confirme as alterações no conjunto de registros. Use Set-AzureDnsRecordSet para carregar as alterações no conjunto de registros para o DNS do Azure:

	Set-AzureDnsRecordSet -RecordSet $rs

## Adicionar um registro CNAME para seu domínio personalizado

Supondo que o domínio já seja gerenciado pelo DNS do Azure (consulte [Delegação de domínio ao DNS](../dns-domain-delegation)), você pode usar o seguinte exemplo para criar um registro CNAME para contoso.azurewebsites.net:

### Etapa 1

Abra o PowerShell e crie um novo conjunto de registros CNAME e atribua-o a uma variável $rs:

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}

Isso cria um tipo de conjunto de registros CNAME com uma "vida útil" de 600 segundos na zona DNS denominada "contoso.com".

### Etapa 2

Depois de criar o conjunto de registros CNAME, você precisa criar um valor de alias que apontará para o aplicativo Web.

Usando a variável "$rs" atribuída anteriormente, você pode usar o comando PowerShell a seguir para criar o alias para o aplicativo Web contoso.azurewebsites.net.

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
 
	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {contoso.azurewebsites.net}
	Tags              : {}

### Etapa 3

Confirme as alterações usando o cmdlet Set-AzureDnsRecordSet:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

Você pode validar se o registro foi criado corretamente consultando "www.contoso.com" usando o nslookup, conforme mostrado abaixo:

	PS C:\> nslookup
	Default Server:  Default
	Address:  192.168.0.1
 
	> www.contoso.com
	Server:  default server
	Address:  192.168.0.1
	 
	Non-authoritative answer:
	Name:    <instance of web app service>.cloudapp.net
	Address:  <ip of web app service>
	Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## Criar um registro awverify para aplicativos Web (apenas registros A)

Se você decidir usar um registro A do seu aplicativo Web, você deve passar por um processo de verificação para permitir que o Azure garanta que você tem o domínio personalizado. Essa etapa de verificação é feita criando um registro CNAME especial chamado "awverify".

No exemplo abaixo, o registro "awverify" será criado para contoso.com verificar a propriedade do domínio personalizado:

### Etapa 1

	PS C:\> $rs = New-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
	RecordType        : CNAME
	Records           : {}
	Tags              : {}


### Etapa 2

Depois de criar o conjunto de registros awverify, você deve atribuir o alias do conjunto de registros CNAME a awverify.contoso.azurewebsites.net, conforme mostrado no comando a seguir:

	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
 
	Name              : awverify
	ZoneName          : contoso.com
	ResourceGroupName : myresourcegroup
	Ttl               : 600
	Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
	RecordType        : CNAME
	Records           : {awverify.contoso.azurewebsites.net}
	Tags              : {}

### Etapa 3

Confirme as alterações usando o cmdlet Set-AzureDnsRecordSet., conforme mostrado no comando abaixo:

	PS C:\>Set-AzureDnsRecordSet -RecordSet $rs

Agora você pode continuar a seguir as etapas em [Configurando um nome de domínio personalizado para o serviço de aplicativo](../web-sites-custom-domain-name) para configurar seu aplicativo Web para usar um domínio personalizado.

## Consulte também

[Gerenciar zonas DNS](../dns-operations-dnszones)

[Gerenciar registros DNS](../dns-operations-recordsets)

[Visão geral do Gerenciador de Tráfego](../traffic-manager-overview)

[Automatizar operações do Azure com o SDK do .NET](../dns-sdk)


 

<!---HONumber=Oct15_HO3-->