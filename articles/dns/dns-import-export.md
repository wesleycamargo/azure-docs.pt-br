<properties
   pageTitle="Como importar e exportar um arquivo de zona de domínio com o DNS do Azure | Microsoft Azure"
   description="Saiba como importar e exportar um arquivo de zona DNS para o DNS do Azure usando a CLI do Azure"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# Como importar e exportar um arquivo de zona DNS


Este guia mostra como importar e exportar arquivos de zona DNS do DNS do Azure.

## Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro DNS na zona. Ele segue um formato padrão, tornando-o adequado para transferir registros DNS entre diferentes sistemas DNS. O uso de um arquivo de zona é uma maneira rápida, confiável e conveniente de transferir uma zona DNS para dentro ou fora do DNS do Azure.

O DNS do Azure dá suporte à importação e à exportação de arquivos de zona por meio da CLI do Azure. Este artigo explica como usar esse recurso.

A CLI do Azure é uma ferramenta de linha de comando de plataforma cruzada usada para gerenciar os serviços do Azure. Ela está disponível para as plataformas Windows, Mac e Linux por meio da [página de downloads do Azure](https://azure.microsoft.com/downloads/). Esse suporte de plataforma cruzada é particularmente importante para a importação e a exportação do arquivo de zona, pois o software de servidor de nomes mais comum, [BIND](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

## Como obter o arquivo de zona DNS existente

Antes de importar o arquivo de zona DNS para o DNS do Azure, você precisará obter uma cópia desse arquivo. A origem do arquivo depende de onde a zona DNS está hospedada no momento. Por exemplo:

- Se a zona DNS for hospedada por um serviço de terceiros (como um registrador de domínio, um hoster de DNS dedicado ou um provedor de nuvem alternativo), esse serviço deverá fornecer a capacidade de baixar o arquivo de zona DNS.
-	Se a zona DNS for hospedada usando o servidor DNS do Windows, por padrão, os arquivos de zona poderão ser encontrados na pasta **'%systemroot%\\system32\\dns'**. O caminho completo para cada arquivo de zona também é mostrado na guia 'Geral' do console de gerenciamento do serviço DNS.
-	Se a zona DNS for hospedada usando o BIND, o local do arquivo de zona para cada zona será especificado no arquivo de configuração do BIND, **'named.conf'**. 

>[AZURE.NOTE]Os arquivos de zona baixados do 'GoDaddy' têm um formato ligeiramente diferente do padrão, que precisa ser corrigido antes da importação desses arquivos de zona para o DNS do Azure. Especificamente, os nomes DNS no RDATA de cada registro DNS são especificados como nomes totalmente qualificados, mas sem um '.' de terminação. Isso significa que eles são interpretados por outros sistemas DNS como nomes relativos. Você precisará editar o arquivo de zona para acrescentar o '.' de terminação aos nomes antes de importá-los para o DNS do Azure.

## Como importar um arquivo de zona DNS para o DNS do Azure

O formato do comando da CLI do Azure para importar uma zona DNS é o seguinte: `azure network dns zone import [options] <resource group> <zone name> <zone file name>` em que:

- **grupo de recursos** - é o nome do grupo de recursos para a zona no DNS do Azure.
- **nome da zona** - é o nome da zona.
- **nome de arquivo de zona** - é o caminho/nome do arquivo de zona a ser importado.

Se uma zona com esse nome não existir no grupo de recursos, ela será criada para você. Se a zona já existir, conjuntos de registros importados serão mesclados a conjuntos de registros existentes. Para substituir os conjuntos de registros existentes em vez disso, use a opção '--force'. Mais detalhes sobre [como conjuntos de registro são mesclados](#merging-with-existing-data) são fornecidas abaixo.

Para verificar o formato de um arquivo de zona sem realmente importá-lo, use a opção '--parse-only'.

## Passo a passo para importar um arquivo de zona para o DNS do Azure

Vejamos um exemplo. Suponha que você deseje importar um arquivo para a zona 'contoso.com'.

### Etapa 1
Faça logon em sua assinatura do Azure usando a CLI do Azure.

	azure login

### Etapa 2
Selecione a assinatura em que você deseja criar a nova zona DNS.

	azure account set <subscription name>

### Etapa 3
O DNS do Azure é um serviço somente do ARM (Gerenciador de Recursos do Azure). A CLI do Azure deve ser alternada para o modo ARM.

	azure config mode arm

### Etapa 4
Antes de usar o serviço DNS do Azure, você deve registrar sua assinatura para usar o provedor de recursos Microsoft.Network (essa é uma operação única para cada assinatura).

	azure provider register Microsoft.Network

### Etapa 5 
Se não o tiver, você também precisará criar um grupo de recursos do ARM.
	
	azure group create myresourcegroup westeurope

### Etapa 6	
Para importar a zona 'contoso.com' do arquivo 'contoso.com.txt' para uma nova zona DNS no grupo de recursos 'myresourcegroup', execute o comando `azure network dns zone import`.

	azure network dns zone import myresourcegroup contoso.com contoso.com.txt

O comando carregará o arquivo de zona, o analisará e executará uma série de comandos no serviço DNS do Azure para criar a zona e todos os conjuntos de registros nela.

Ele relatará o progresso na janela do console, bem como erros ou avisos. Como conjuntos de registros são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

## Como verificar a zona DNS após a importação

Você pode listar os registros usando o comando da CLI do Azure a seguir (você também pode fazer isso por meio do PowerShell, usando `Get-AzureRmDnsRecordSet`).

	azure network dns record-set list myresourcegroup contoso.com

Você também pode usar 'nlookup' para verificar a resolução de nomes para os registros. Como a zona ainda não é delegada, você precisará especificar os servidores de nomes DNS do Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes de servidores de nomes atribuídos à zona e consultar o registro 'www' usando 'nslookup':

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

Após verificar se a zona foi importada corretamente, você precisará [atualizar a delegação DNS](dns-domain-delegation.md) para apontar para os servidores de nomes DNS do Azure.

## Como mesclar a dados existentes

A importação de um arquivo de zona criará uma nova zona no DNS do Azure, se ela ainda não existir. Se a zona já existir, os conjuntos de registros no arquivo de zona deverão ser mesclados a conjuntos de registros existentes. O comportamento de mesclagem é o seguinte:

1.	Por padrão, os conjuntos de registros novos e existentes são mesclados. Registros idênticos em um conjunto de registros mesclados têm a duplicação eliminada.
2.	Como alternativa, se a opção '--force' for especificada, o processo de importação substituirá os conjuntos de registros existentes por novos conjuntos de registros. Os conjuntos de registros existentes que não tiverem um registro correspondente definido no arquivo de zona importado não serão removidos.
3.	Quando os conjuntos de registros são mesclados, é usado o TTL dos conjuntos de registros já existentes. Quando '--force' é usado, o TTL do novo conjunto de registros é usado.
4.	Parâmetros SOA (exceto 'host') sempre são extraídos do arquivo de zona importado, independentemente do uso de '--force'. Da mesma forma, para o registro NS no ápice da zona, o TTL do conjunto de registros sempre é obtido do arquivo de zona importado.
5.	Um registro CNAME importado não substituirá um registro CNAME existente com o mesmo nome, a menos que o parâmetro '--force' seja especificado.
6.	Quando ocorre um conflito entre um registro CNAME e outro registro com o mesmo nome, mas com tipo diferente (independentemente de qual deles é existente ou novo), o registro existente é mantido. Isso é independente do uso de '--force'.

## Detalhes técnicos adicionais
As observações a seguir fornecem detalhes técnicos adicionais sobre o processo de importação de zona:

1.	A diretiva $TTL é opcional e tem suporte. Quando nenhuma diretiva $TTL for especificada, serão importados registros sem um TTL explícito, usando um TTL padrão de 3600 segundos. Quando dois registros no mesmo conjunto de registros especificarem TTLs diferentes, o menor valor será usado.
2.	A diretiva $ORIGIN é opcional e tem suporte. Quando nenhuma $ORIGIN for definida, o valor padrão usado será o nome da zona, conforme especificado na linha de comando (além do '.' de terminação).
3.	Não há suporte para as diretivas $INCLUDE e $GENERATE.
4.	Há suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.  
5.	O registro SOA é criado automaticamente pelo DNS do Azure quando uma zona é criada. Quando um arquivo de zona é importado, todos os parâmetros SOA são obtidos do arquivo de zona, EXCETO o parâmetro 'host', que usa o valor fornecido pelo DNS do Azure. Isso ocorre porque esse parâmetro deve referir-se ao servidor de nomes primário fornecido pelo DNS do Azure.
6.	O registro NS definido no ápice da zona também é criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o TTL desse conjunto de registros é importado. Esses registros contêm os nomes do servidor de nomes fornecido pelo DNS do Azure. Portanto, os dados de registro não são substituídos pelos valores contidos no arquivo de zona importado.
7.	Durante a Visualização Pública, o DNS do Azure dá suporte apenas a registros TXT de cadeia de caracteres única. Assim, registros TXT com várias cadeias de caracteres serão concatenados e truncados para 255 caracteres.

## Como exportar um arquivo de zona DNS do DNS do Azure

O formato do comando da CLI do Azure para importar uma zona DNS é o seguinte: `azure network dns zone export [options] <resource group> <zone name> <zone file name>` em que:

- **grupo de recursos** - é o nome do grupo de recursos para a zona no DNS do Azure.
- **nome da zona** - é o nome da zona.
- **nome de arquivo de zona** - é o caminho/nome do arquivo de zona a ser exportado.

## Passo a passo para exportar um arquivo do DNS do Azure
 
Assim como ocorre com a importação de zona, primeiro precisamos fazer logon, escolher nossa assinatura e configurar a CLI do Azure para usar o modo 'ARM':

### Etapa 1
Faça logon em sua assinatura do Azure usando a CLI do Azure.

	azure login

### Etapa 2
Selecione a assinatura em que você deseja criar a nova zona DNS.

	azure account set <subscription name>

### Etapa 3
O DNS do Azure é um serviço somente do ARM (Gerenciador de Recursos do Azure). A CLI do Azure deve ser alternada para o modo ARM.

	azure config mode arm
### Etapa 4
Para exportar a zona DNS do Azure 'contoso.com' existente no grupo de recursos 'myresourcegroup' para o arquivo 'contoso.com.txt' (na pasta atual), execute `azure network dns zone export`.

	azure network dns zone export myresourcegroup contoso.com contoso.com.txt

Esse comando chamará o serviço DNS do Azure para enumerar os conjuntos de registros na zona e exportará os resultados para um arquivo de zona compatível com BIND.

<!---HONumber=AcomDC_0114_2016-->