<properties
   pageTitle="Criar um conjunto de registros e registros para uma zona DNS usando o portal do Azure | Microsoft Azure"
   description="Como criar registros de host para o DNS do Azure e criar conjuntos de registros e registros usando o portal do Azure"
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
   ms.date="03/29/2016"
   ms.author="cherylmc"/>


# Criar registros e conjuntos de registros DNS usando o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI do Azure](dns-getstarted-create-recordset-cli.md)


Depois de criar a zona DNS, você precisa adicionar os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

## Noções básicas sobre conjuntos de registros e registros

### Sobre registros

Cada registro DNS tem um nome e um tipo.

Um FQDN (nome de domínio "totalmente qualificado") inclui o nome da zona, enquanto um nome "relativo" não o inclui. Por exemplo, o nome relativo do registro "www" na zona "contoso.com" fornece o nome totalmente qualificado do registro "www.contoso.com".

>[AZURE.NOTE] No DNS do Azure, os registros são especificados usando nomes relativos.

Há vários tipos de registros de acordo com os dados que eles contêm. O tipo mais comum é um registro "**A**", que mapeia um nome para um endereço IPv4. Outro tipo é um registro "**MX**", que mapeia um nome para um servidor de email.

O DNS do Azure dá suporte a todos os tipos de registro DNS comuns: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT. Observe que registros SPF devem ser criados usando o tipo de registro TXT. Confira [esta página](http://tools.ietf.org/html/rfc7208#section-3.1) para obter mais informações.


### Sobre conjuntos de registros

Às vezes, você precisa criar mais de um registro DNS com determinado nome e tipo. Por exemplo, suponha que o site www.contoso.com seja hospedado em dois endereços IP diferentes. Isso requer dois registros A diferentes, um para cada endereço IP. Este é um exemplo de um conjunto de registros.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

O DNS do Azure gerencia registros DNS usando conjuntos de registros. Um conjunto de registros é a coleção de registros DNS de uma zona com o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registros contêm um único registro, mas exemplos como o mostrado acima no qual um conjunto de registros contém mais de um registro de exemplos não são incomuns.

Conjuntos de registros do tipo SOA e CNAME são uma exceção. Os padrões do DNS não permitem vários registros com o mesmo nome para esses tipos.

O tempo de vida, ou TTL, especifica quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado novamente. No exemplo acima, o TTL tem 3600 segundos ou 1 hora. O TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros no conjunto de registros.

#### Conjuntos de registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Eles são retornados para qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga). Conjuntos de registros curinga têm suporte para todos os tipos de registro, exceto NS e SOA.

Para criar um conjunto de registros curinga, use o nome do conjunto de registros "\*", ou um nome cujo primeiro rótulo seja "\*", por exemplo, "\*.foo".

#### Conjuntos de registros CNAME

Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um CNAME com o nome relativo "www" e um registro A com o nome relativo "www" ao mesmo tempo. Uma vez que o apex de zona (nome = "@") sempre contém os conjuntos de registro NS e SOA criados quando a zona é criada, isso significa que você não pode criar um conjunto de registros CNAME no apex da zona. Essas restrições são provenientes dos padrões DNS. Elas não são limitações do DNS do Azure.


## Para criar um novo conjunto de registros e um registro

O exemplo a seguir explica como criar um conjunto de registros e registros usando o portal do Azure. Usaremos o tipo de registro de DNS 'A'.

1. Faça logon no Portal do Azure.

2. Navegue até a folha da **zona do DNS** na qual você deseja criar um conjunto de registros.

3. Na folha da zona do DNS, na parte superior da folha, clique em **Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.
 
	![novo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Na folha **Adicionar conjunto de registros**, nomeie o conjunto de registros. Por exemplo, você poderia nomear o conjunto de registros como "**www**".
  
	![adicionar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selecione o tipo de registro que deseja criar. Por exemplo, **A**.

6. Defina o **TTL**. O padrão no portal é 1 hora.

7. Adicione os endereços IP, um endereço IP por linha. O uso do nome de conjunto de registros e do tipo de registro sugeridos acima adiciona os endereços IP IPv4 ao registro para o conjunto de registros www.

8. Ao terminar de adicionar endereços IP, clique em **OK** na parte inferior da folha. O conjunto de registros DNS será criado.

## Teste sua zona do DNS usando ferramentas de DNS


Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisará direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são mostrados no painel Essentials da folha de zona do DNS. Confira o artigo [Delegar seu domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Você pode testar a zona do DNS usando ferramentas do DNS como nslookup, dig ou o [cmdlet do PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).


## Próximas etapas

Para saber mais sobre o DNS do Azure, confira [Visão geral do DNS do Azure](dns-overview.md). Para saber mais sobre como automatizar o DNS, confira [Criar zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).

<!----HONumber=AcomDC_0406_2016-->