<properties
   pageTitle="Importar e exportar um arquivo de zona de domínio para o DNS do Azure usando a CLI | Microsoft Azure"
   description="Saiba como importar e exportar um arquivo de zona DNS para o DNS do Azure usando a CLI do Azure"
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
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Importar e exportar um arquivo de zona DNS usando a CLI do Azure


Este artigo explica como importar e exportar arquivos da zona DNS para o DNS do Azure usando a CLI do Azure.

## Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro DNS (Sistema de Nomes de Domínio ) na zona. Ele segue um formato padrão, tornando-o adequado para transferir registros DNS entre sistemas DNS. O uso de um arquivo de zona é uma maneira rápida, confiável e conveniente de transferir uma zona DNS para dentro ou fora do DNS do Azure.

O DNS do Azure oferece suporte à importação e exportação de arquivos de zona usando a interface de linha de comando (CLI) do Azure. A CLI do Azure é uma ferramenta de linha de comando de plataforma cruzada usada para gerenciar os serviços do Azure. Ela está disponível para as plataformas Windows, Mac e Linux por meio da [página de downloads do Azure](https://azure.microsoft.com/downloads/). Esse suporte de plataforma cruzada é particularmente importante para a importação e a exportação de arquivos de zona, pois o software de servidor de nomes mais comum, [BIND](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

## Obtenha seu arquivo de zona DNS existente

Antes de importar um arquivo de zona DNS para o DNS do Azure, você precisará obter uma cópia do arquivo de zona. A origem do arquivo depende de onde a zona DNS está hospedada no momento.

- Se a zona DNS for hospedada por um serviço de parceiro (como um registrador de domínio, um provedor de host DNS dedicado ou um provedor de nuvem alternativo), esse serviço deverá fornecer a capacidade de baixar o arquivo de zona DNS.

- Se a zona DNS estiver hospedada no DNS do Windows, a pasta padrão para os arquivos de zona será **%systemroot%\\system32\\dns**. O caminho completo para cada arquivo de zona também é mostrado na guia **Geral** do console de gerenciamento do serviço DNS.

- Se a zona DNS for hospedada usando o BIND, o local do arquivo de zona para cada zona será especificado no arquivo de configuração do BIND, **named.conf**.

**Trabalhar com arquivos de zona do GoDaddy**<BR> Arquivos de zona baixados do GoDaddy têm um formato ligeiramente diferente do padrão. Você precisa corrigir o problema antes de importar esses arquivos de zona DNS do Azure. Nomes DNS no RData de cada registro DNS são especificados como nomes totalmente qualificados, mas não têm um encerramento "." Isso significa que eles são interpretados por outros sistemas DNS como nomes relativos. Você precisa editar o arquivo de zona para acrescentar o '.' de terminação aos nomes antes de importá-los para o DNS do Azure.

## Importar um arquivo de zona DNS para o DNS do Azure


A importação de um arquivo de zona criará uma nova zona no DNS do Azure, se ela ainda não existir. Se a zona já existir, os conjuntos de registros no arquivo de zona deverão ser mesclados a conjuntos de registros existentes.

### Comportamento de mesclagem

- Por padrão, os conjuntos de registros novos e existentes são mesclados. Registros idênticos em um conjunto de registros mesclados têm a duplicação eliminada.

- Como alternativa, se a opção `--force` for especificada, o processo de importação substituirá os conjuntos de registros existentes por novos conjuntos de registros. Os conjuntos de registros existentes que não tiverem um registro correspondente definido no arquivo de zona importado não serão removidos.

- Quando os conjuntos de registros são mesclados, é usado o TTL dos conjuntos de registros já existentes. Quando `--force` é usado, o TTL do novo conjunto de registros é usado.

- Parâmetros SOA (Início de Autoridade) (exceto `host`) sempre são obtidos do arquivo de zona importado, independentemente do uso de `--force`. Da mesma forma, para o conjunto do registro de nome do servidor no ápice da zona, o TTL sempre é obtido do arquivo de zona importado.

- Um registro CNAME importado não substituirá um registro CNAME existente com o mesmo nome, a menos que o parâmetro `--force` seja especificado.

- Quando ocorre um conflito entre um registro CNAME e outro registro com o mesmo nome, mas com tipo diferente (independentemente de qual deles é existente ou novo), o registro existente é mantido. Isso é independente do uso de `--force`.

### Informações adicionais sobre importação

As observações a seguir fornecem detalhes técnicos adicionais sobre o processo de importação de zona.

- A diretiva `$TTL` é opcional e tem suporte. Quando nenhuma diretiva `$TTL` for especificada, serão importados registros sem um TTL explícito, definidos com um TTL padrão de 3600 segundos. Quando dois registros no mesmo conjunto de registros especificarem TTLs diferentes, o menor valor será usado.

- A diretiva `$ORIGIN` é opcional e tem suporte. Quando nenhuma `$ORIGIN` for definida, o valor padrão usado será o nome da zona, conforme especificado na linha de comando (além do "." de terminação).

- As diretivas `$INCLUDE` e `$GENERATE` não têm suporte.

- Há suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

- O registro SOA é criado automaticamente pelo DNS do Azure quando uma zona é criada. Quando você importa um arquivo de zona, todos os parâmetros SOA são extraídos do arquivo de zona, *exceto* o parâmetro `host`. Esse parâmetro usa o valor fornecido pelo DNS do Azure. Isso ocorre porque esse parâmetro deve referir-se ao servidor de nomes primário fornecido pelo DNS do Azure.

- O registro de nome do servidor definido no ápice da zona também é criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o TTL desse conjunto de registros é importado. Esses registros contêm os nomes de servidor de nome fornecidos pelo DNS do Azure. Os dados de registro não são substituídos pelos valores contidos no arquivo de zona importado.

- Durante a visualização pública, o DNS do Azure suporta apenas os registros TXT de cadeia de caracteres única. Registros TXT de cadeia de caracteres múltiplas serão concatenados e truncados para 255 caracteres.

### Valores e formato da CLI


O formato do comando da CLI do Azure para importar uma zona DNS é:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
- `<zone name>` é o nome da zona.
- `<zone file name>` é o caminho/nome do arquivo de zona a ser importado.

Se uma zona com esse nome não existir no grupo de recursos, ela será criada para você. Se a zona já existir, conjuntos de registros importados serão mesclados a conjuntos de registros existentes. Para substituir os conjuntos de registros existentes, use a opção `--force`.

Para verificar o formato de um arquivo de zona sem realmente importá-lo, use a opção `--parse-only`.

### Etapa 1. Importar um arquivo de zona

Para importar um arquivo de zona para a zona **contoso.com**.

1. Entre em sua assinatura do Azure usando a CLI do Azure.

		azure login

2. Selecione a assinatura em que você deseja criar a nova zona DNS.

		azure account set <subscription name>

3. O DNS do Azure é um serviço somente do Gerenciador de Recursos do Azure. A CLI do Azure deve ser alternada para o modo do Gerenciador de Recursos.

		azure config mode arm

4. Antes de usar o serviço DNS do Azure, você deve registrar sua assinatura para usar o provedor de recursos Microsoft.Network. (Essa operação deve ser executa apenas uma vez para cada assinatura.)

		azure provider register Microsoft.Network

5. Se ainda não o tiver, você também precisará criar um grupo de recursos do Gerenciador de Recursos.

		azure group create myresourcegroup westeurope

6. Para importar a zona **contoso.com** do arquivo **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup**, execute o comando `azure network dns zone import`.<BR>Esse comando carregará o arquivo de zona e o analisará. O comando executará uma série de comandos no serviço DNS do Azure para criar a zona e todos os conjuntos do registro na zona. Ele relatará o progresso na janela do console, bem como erros ou avisos. Como os conjuntos de registros são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

		azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### Etapa 2. Verificar a zona

Para verificar a zona DNS após importar o arquivo, você pode usar qualquer um dos seguintes métodos:

- Você pode listar os registros usando o seguinte comando CLI do Azure.

		azure network dns record-set list myresourcegroup contoso.com

- Você pode listar os registros usando o cmdlet do PowerShell `Get-AzureRmDnsRecordSet`.

- Você pode usar `nslookup` para verificar a resolução de nomes para os registros. Como a zona ainda não é delegada, você precisará especificar os servidores de nomes DNS do Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes do servidor de nomes atribuídos à zona. Também mostra como consultar o registro "www" usando `nslookup`.

    	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
    	info:Executing command network dns record-set show
    	+ Looking up the DNS Record Set "@" of type "NS"
    	data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    	data:Name: @
    	data:Type: Microsoft.Network/dnszones/NS
    	data:Location: global
    	data:TTL : 3600
    	data:NS records
    	data:Name server domain name : ns1-01.azure-dns.com
    	data:Name server domain name : ns2-01.azure-dns.net
    	data:Name server domain name : ns3-01.azure-dns.org
    	data:Name server domain name : ns4-01.azure-dns.info
    	data:
    	info:network dns record-set show command OK
    
    	C:\> nslookup www.contoso.com ns1-01.azure-dns.com
    
    	Server: ns1-01.azure-dns.com
    	Address:  40.90.4.1
    
    	Name:www.contoso.com
    	Addresses:  134.170.185.46
    	134.170.188.221

### Etapa 3. Atualizar a delegação DNS

Após verificar se a zona foi importada corretamente, você precisará atualizar a delegação DNS para apontar para os servidores de nomes DNS do Azure. Para saber mais, confira o artigo [Atualizar a delegação DNS](dns-domain-delegation.md).

## Como exportar um arquivo de zona DNS do DNS do Azure

O formato do comando da CLI do Azure para importar uma zona DNS é:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
- `<zone name>` é o nome da zona.
- `<zone file name>` é o caminho/nome do arquivo de zona a ser exportado.

Como acontece com a importação de zona, você primeiro precisa fazer logon, escolher sua assinatura e configurar a CLI do Azure para usar o modo do Gerenciador de Recursos.

### Para exportar um arquivo de zona


1. Entre em sua assinatura do Azure usando a CLI do Azure.

		azure login

2. Selecione a assinatura em que você deseja criar a nova zona DNS.

		azure account set <subscription name>

3. O Azure DNS é um serviço somente do Gerenciador de Recursos do Azure. A CLI do Azure deve ser alternada para o modo do Gerenciador de Recursos.

		azure config mode arm

4. Para exportar a zona DNS do Azure **contoso.com** existente no grupo de recursos **myresourcegroup** para o arquivo **contoso.com.txt** (na pasta atual), execute `azure network dns zone export`. Esse comando chamará o serviço DNS do Azure para enumerar os conjuntos de registros na zona e exportará os resultados para um arquivo de zona compatível com BIND.

		azure network dns zone export myresourcegroup contoso.com contoso.com.txt

<!---HONumber=AcomDC_0817_2016-->