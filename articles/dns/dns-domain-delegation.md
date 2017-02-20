---
title: "Delegar seu domínio ao Azure DNS | Microsoft Docs"
description: "Entenda como alterar a delegação de domínio e usar os servidores de nomes do Azure DNS para fornecer hospedagem do domínio."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 5145418159aa457be6d1fc9ed5bb1a43a955791c

---

# <a name="delegate-a-domain-to-azure-dns"></a>Delegar um domínio ao Azure DNS

O Azure DNS permite hospedar uma zona DNS e gerenciar os registros DNS para um domínio no Azure. Para que consultas DNS para um domínio acessem o Azure DNS, o domínio deve ser delegado ao Azure DNS por meio do domínio pai. Tenha em mente que o Azure DNS não é o registrador de domínio. Esta página explica como funciona a delegação de domínio e como delegar domínios ao Azure DNS.

## <a name="how-dns-delegation-works"></a>Como funciona a delegação de DNS

### <a name="domains-and-zones"></a>Zonas e domínios

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa no domínio 'raiz', cujo nome é simplesmente '**.**'.  Abaixo dele vêm domínios de nível superior, como 'com', 'net', 'org', 'uk' ou 'jp'.  Abaixo desses estão domínios de segundo nível, como 'org.uk' ou 'co.jp'.  E assim por diante. Os domínios na hierarquia de DNS são hospedados usando zonas DNS separadas. Essas zonas são distribuídas globalmente, hospedadas por servidores de nomes DNS pelo mundo.

**Zona DNS**

Um domínio é um nome exclusivo no Sistema de Nomes de Domínio, por exemplo, 'contoso.com'. Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio 'contoso.com' pode conter uma série de registros DNS, como 'mail.contoso.com' (para um servidor de email) e 'www.contoso.com' (para um site).

**Registrador de domínio**

Um registrador de domínio é uma empresa que pode fornecer nomes de domínio da Internet. A empresa verificará se o domínio de Internet que você deseja usar está disponível e permitirá que você o compre. Uma vez registrado o nome de domínio, você será seu o proprietário legal. Se você já tiver um domínio da Internet, usará o registrador de domínio atual para delegar ao Azure DNS.

> [!NOTE]
> Para saber mais sobre quem possui um nome de domínio específico ou sobre como comprar um domínio, veja [Gerenciamento de domínio de Internet no Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

* Um servidor DNS *autoritativo* hospeda zonas DNS. Ele responde a consultas DNS apenas para registros nessas zonas.
* Um servidor DNS *recursivo* não hospeda zonas DNS. Ele responde a todas as consultas DNS chamando os servidores DNS autoritativos para coletar os dados de que precisa.

> [!NOTE]
> O Azure DNS fornece um serviço DNS autoritativo.  Ele não fornece um serviço DNS recursivo.
>
> No Azure, os Serviços de Nuvem e as VMs são automaticamente configurados para usar os serviços DNS recursivos que são fornecidos separadamente como parte da infraestrutura do Azure.  Para saber mais sobre como alterar essas configurações de DNS, confira [Resolução de nomes no Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Os clientes DNS em computadores ou em dispositivos móveis normalmente chamam um servidor DNS recursivo para executar consultas DNS das quais os aplicativos cliente precisam.

Quando um servidor DNS recursivo recebe uma consulta de um registro DNS como 'www.contoso.com', ele precisa localizar o servidor de nomes que hospeda a zona do domínio 'contoso.com'. Para fazer isso, ele começa em servidores-raiz de nomes e daí localiza os servidores de nomes que hospeda a zona 'com'. Em seguida, ele consulta os servidores de nomes 'com' para localizar os servidores de nomes que hospedam a zona 'contoso.com'.  Finalmente, é possível consultar os servidores de nomes para 'www.contoso.com'.

Isso é chamado de resolução de nome DNS. Na verdade, a resolução de DNS inclui etapas adicionais, como as CNAMEs a seguir, mas isso não é importante para compreender como funciona a delegação de DNS.

Como uma zona pai 'aponta' os servidores de nomes para uma zona filho? Ele faz isso usando um tipo especial de registro DNS chamado de registro NS (NS significa 'name server', servidor de nomes). Por exemplo, a zona raiz contém registros NS para 'com', mostrando os servidores de nomes da zona 'com'. A zona 'com', por sua vez, contém registros NS para 'contoso.com', que mostra os servidores de nomes da zona 'contoso.com'. Configurar os registros NS para uma zona filho em uma zona pai é chamado de delegar o domínio.

![Servidor de nomes DNS](./media/dns-domain-delegation/image1.png)

Cada delegação realmente tem duas cópias dos registros NS: uma na zona pai apontando para a zona filho e outra na própria zona filho. A zona 'contoso.com' contém os registros NS para contoso.com (juntamente com os registros NS em 'com'). Estes são chamados de registros NS autoritativos e ficam no ápice da zona filho.

## <a name="delegating-a-domain-to-azure-dns"></a>Delegando um domínio ao Azure DNS
Depois de criar sua zona DNS no Azure DNS, você precisa configurar os registros NS na zona pai para tornar o Azure DNS a origem autoritativa para resolução de nomes em sua zona. Para domínios comprados de um registrador, seu registrador oferecerá a opção de configurar esses registros NS.

> [!NOTE]
> Você não precisa ter um domínio para criar uma zona DNS com esse nome de domínio no Azure DNS. No entanto, você precisa ter o domínio para poder configurar a delegação para o Azure DNS com o registrador.

Por exemplo, suponha que você compre o domínio 'contoso.com' e crie uma zona chamada 'contoso.com' no Azure DNS. Como o proprietário do domínio, seu registrador oferecerá a opção de configurar os endereços de servidor de nomes (ou seja, os registros NS) para seu domínio. O registrador armazenará esses registros NS no domínio pai, neste caso, '.com'. Os clientes em todo o mundo serão direcionados ao seu domínio na zona do Azure DNS quando tentarem os registros DNS em 'contoso.com'.

### <a name="finding-the-name-server-names"></a>Localização dos nomes do servidor de nomes
Antes que você possa delegar a zona DNS ao Azure DNS, precisará saber os nomes do servidor de nomes para sua zona. O Azure DNS aloca os servidores de nomes de um pool sempre que uma zona é criada.

A maneira mais fácil de ver os servidores de nomes atribuídos à zona é por meio do portal do Azure.  Neste exemplo, os servidores de nomes 'ns1-01.azure-dns.com', 'ns2-01.azure-dns .net', 'ns3-01.azure-dns.org' e 'ns4-01.azure-dns.info' foram atribuídos à zona 'contoso.net':

 ![Servidor de nomes DNS](./media/dns-domain-delegation/viewzonens500.png)

O Azure DNS cria automaticamente os registros NS autoritativos na zona que contém os servidores de nomes atribuídos.  Para ver os nomes de servidor de nomes por meio do Azure PowerShell ou da CLI do Azure, bastará obter esses registros.

Usando o Azure PowerShell, os registros NS autoritativos podem ser recuperados da maneira a seguir. Observe que o nome do registro "@" é usado para fazer referência a registros no ápice da zona.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName MyResourceGroup
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

O exemplo a seguir é a resposta.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : MyResourceGroup
Ttl               : 3600
Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
RecordType        : NS
Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                    ns4-01.azure-dns.info}
Tags              : {}
```

Você também pode usar a CLI do Azure de plataforma cruzada para recuperar os registros NS autoritativos e, assim, descobrir os servidores de nomes atribuídos à zona:

```azurecli
azure network dns record-set show MyResourceGroup contoso.net @ NS
```

O exemplo a seguir é a resposta.

```
info:    Executing command network dns record-set show
    + Looking up the DNS Record Set "@" of type "NS"
data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
data:    Name                            : @
data:    Type                            : Microsoft.Network/dnszones/NS
data:    Location                        : global
data:    TTL                             : 172800
data:    NS records
data:        Name server domain name     : ns1-01.azure-dns.com.
data:        Name server domain name     : ns2-01.azure-dns.net.
data:        Name server domain name     : ns3-01.azure-dns.org.
data:        Name server domain name     : ns4-01.azure-dns.info.
data:
info:    network dns record-set show command OK
```

### <a name="to-set-up-delegation"></a>Para configurar a delegação

Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento do DNS do registrador, edite os registros NS e substitua-os por aqueles criados pelo Azure DNS.

Ao delegar um domínio ao Azure DNS, você deverá usar os nomes do servidor de nomes fornecidos pelo Azure DNS.  Você sempre deve usar todos os quatro nomes de servidores, independentemente do nome do domínio.  A delegação de domínio não requer que o nome do servidor use o mesmo domínio de nível superior do que seu domínio.

Você não deve usar 'registros cola' para apontar para endereços IP do servidor de nomes do Azure DNS, pois esses endereços IP podem mudar no futuro. Atualmente, o Azure DNS não dá suporte às delegações usando nomes do servidores de nomes em sua própria zona, às vezes chamados de 'servidores de nome intuitivos'.

### <a name="to-verify-name-resolution-is-working"></a>Para verificar se a resolução de nomes está funcionando

Após concluir a delegação, você poderá verificar se a resolução de nomes está funcionando, usando uma ferramenta como 'nslookup' para consultar o registro SOA da zona (que também é criado automaticamente quando a zona é criada).

Observe que você não precisa especificar os servidores de nomes do Azure DNS, pois o processo normal de resolução DNS localizará os servidores de nomes automaticamente se a delegação tiver sido configurada corretamente.

```
nslookup -type=SOA contoso.com

Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegating-sub-domains-in-azure-dns"></a>Delegar subdomínios no Azure DNS

Se você quiser configurar uma zona filho separada, poderá delegar um subdomínio no Azure DNS. Por exemplo, tendo configurado e delegado 'contoso.com' no Azure DNS, suponha que você queira configurar uma zona filho separada, 'partners.contoso.com'.

A configuração de um subdomínio segue um processo semelhante ao de uma delegação normal. A única diferença é que, na etapa 3, os registros NS devem ser criados na zona pai 'contoso.com' no Azure DNS, em vez de serem configurados por meio de um registrador de domínio.

1. Crie a zona filho 'partners.contoso.com' no Azure DNS.
2. Pesquise os registros NS de autoridade na zona filho para obter os servidores de nomes que hospedam a zona filho no Azure DNS.
3. Delegue a zona filho ao configurar registros NS na zona pai que apontem para a zona filho.

### <a name="to-delegate-a-sub-domain"></a>Para delegar um subdomínio

O exemplo do PowerShell a seguir demonstra como isso funciona. As mesmas etapas podem ser executadas usando o Portal do Azure ou por meio da CLI do Azure de plataforma cruzada.

#### <a name="step-1-create-the-parent-and-child-zones"></a>Etapa 1. Criar as zonas pai e filho
Primeiro, criamos as zonas pai e filho. Elas podem estar no mesmo grupo de recursos ou em grupos de recursos diferentes.

```powershell
$parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
$child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1
```

#### <a name="step-2-retrieve-ns-records"></a>Etapa 2. Recuperar registros NS

Em seguida, recuperamos os registros NS autoritativos da zona filho como mostrado no exemplo a seguir.  Contém os servidores de nomes atribuídos à zona filho.

```powershell
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS
```

#### <a name="step-3-delegate-the-child-zone"></a>Etapa 3. Delegar a zona filho

Crie o registro NS correspondente definido na zona pai para concluir a delegação. Observe que o nome do conjunto de registros na zona pai corresponde ao nome da zona filho, neste caso, "partners".

```powershell
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

### <a name="to-verify-name-resolution-is-working"></a>Para verificar se a resolução de nomes está funcionando

Você pode verificar se tudo está configurado corretamente examinando o registro SOA da zona filho.

```
nslookup -type=SOA partners.contoso.com

Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Próximas etapas

[Gerenciar zonas DNS](dns-operations-dnszones.md)

[Gerenciar registros DNS](dns-operations-recordsets.md)




<!--HONumber=Feb17_HO2-->


