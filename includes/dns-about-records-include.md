## Sobre registros

Cada registro DNS tem um nome e um tipo. Há vários tipos de registros de acordo com os dados que eles contêm. O tipo mais comum é um registro "A", que mapeia um nome para um endereço IPv4. Outro tipo é um registro “MX”, que mapeia um nome para um servidor de email.

O DNS do Azure dá suporte a todos os tipos de registro DNS comuns: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT. Conjuntos de registro do tipo SOA são criados automaticamente com cada zona. Eles não podem ser criados separadamente. Observe que registros SPF devem ser criados usando o tipo de registro TXT. Confira [esta página](http://tools.ietf.org/html/rfc7208#section-3.1) para saber mais.

No DNS do Azure, os registros são especificados usando nomes relativos. Um FQDN (nome de domínio "totalmente qualificado") inclui o nome da zona, enquanto um nome "relativo" não o inclui. Por exemplo, o nome relativo do registro "www" na zona "contoso.com" fornece o nome totalmente qualificado do registro "www.contoso.com".

## Sobre conjuntos de registros

Às vezes, você precisa criar mais de um registro DNS com determinado nome e tipo. Por exemplo, suponha que o site www.contoso.com seja hospedado em dois endereços IP diferentes. Isso requer dois registros A diferentes, um para cada endereço IP. Este é um exemplo de um conjunto de registros.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

O DNS do Azure gerencia registros DNS usando conjuntos de registros. Um conjunto de registros é a coleção de registros DNS de uma zona com o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registros contêm um único registro, mas exemplos como o mostrado acima no qual um conjunto de registros contém mais de um registro de exemplos não são incomuns.

Conjuntos de registros do tipo SOA e CNAME são uma exceção. Os padrões do DNS não permitem vários registros com o mesmo nome para esses tipos.

O tempo de vida, ou TTL, especifica quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado novamente. No exemplo acima, o TTL tem 3600 segundos ou 1 hora. O TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros no conjunto de registros.

#### Conjuntos de registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Eles são retornados para qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga). Conjuntos de registros curinga têm suporte para todos os tipos de registro, exceto NS e SOA.

Para criar um conjunto de registros curinga, use o nome do conjunto de registros "*", ou um nome cujo primeiro rótulo seja "*", por exemplo, "*.foo".

#### Conjuntos de registros CNAME

Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um CNAME com o nome relativo "www" e um registro A com o nome relativo "www" ao mesmo tempo. Uma vez que o apex de zona (nome = "@") sempre contém os conjuntos de registro NS e SOA criados quando a zona é criada, isso significa que você não pode criar um conjunto de registros CNAME no apex da zona. Essas restrições são provenientes dos padrões DNS. Elas não são limitações do DNS do Azure.