---
title: Registros e zonas DNS | Microsoft Docs
description: "Visão geral do suporte à hospedagem de zonas e registros DNS no DNS do Microsoft Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: jtuliani
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: b99bc20b66787c7a87fa49143b4cbf861e0e68a2

---

# <a name="dns-zones-and-records"></a>Registros e zonas DNS

Esta página explica os principais conceitos de domínios, zonas DNS, conjuntos de registros e registros DNS, e como eles têm suporte no DNS do Azure.

## <a name="domain-names"></a>Nomes de domínio

O Sistema de Nomes de Domínio é uma hierarquia de domínios. A hierarquia começa no domínio 'raiz', cujo nome é simplesmente '**.**'.  Abaixo dele vêm domínios de nível superior, como 'com', 'net', 'org', 'uk' ou 'jp'.  Abaixo desses estão domínios de segundo nível, como 'org.uk' ou 'co.jp'. Os domínios na hierarquia de DNS são distribuídos globalmente, hospedados por servidores de nomes DNS em todo o mundo.

Um registrador de nomes de domínio é uma organização que permite a você adquirir um nome de domínio, como 'contoso.com'.  A aquisição de um nome de domínio lhe dá o direito de controlar a hierarquia de DNS com esse nome, por exemplo, permitindo que você direcione o nome 'www.contoso.com' para o site da sua empresa. O registrador pode hospedar o domínio em seus próprios servidores de nomes em seu nome ou você pode especificar os servidores de nomes alternativos.

O DNS do Azure fornece uma infraestrutura de servidores de nomes de alta disponibilidade e distribuída globalmente que você pode usar para hospedar seu domínio. Ao hospedar seus domínios no DNS do Azure, você poderá gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas, cobrança e suporte que seus outros serviços do Azure.

O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir um nome de domínio, precisa usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas DNS

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS.

Por exemplo, o domínio 'contoso.com' pode conter vários registros DNS, como 'mail.contoso.com' (para um servidor de email) e 'www.contoso.com' (para um site da Web).

Ao criar uma zona DNS no DNS do Azure, o nome da zona deverá ser exclusivo no grupo de recursos. O mesmo nome de zona pode ser reutilizado em outro grupo de recursos ou uma assinatura do Azure diferente. Quando várias zonas têm o mesmo nome, cada instância recebe endereços de servidor de nomes diferentes. Apenas um conjunto de endereços pode ser configurado com o registrador de nome de domínio.

> [!NOTE]
> Você não precisa ter um nome de domínio para criar uma zona DNS com esse nome de domínio no DNS do Azure. No entanto, você precisa ser o proprietário do domínio para configurar os servidores de nomes do DNS do Azure como os servidores de nome corretos para o nome de domínio no registrador de nome de domínio.

Confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)para saber mais.

## <a name="dns-records"></a>Registros DNS

### <a name="record-types"></a>Tipos de registro

Cada registro DNS tem um nome e um tipo. Os registros são organizados em vários tipos de acordo com os dados que eles contêm. O tipo mais comum é um registro 'A', que mapeia um nome para um endereço IPv4. Outro tipo comum é um registro 'MX', que mapeia um nome para um servidor de email.

O DNS do Azure dá suporte a todos os tipos de registro DNS comuns: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

### <a name="record-names"></a>Nomes de registros

No DNS do Azure, os registros são especificados usando nomes relativos. Um FQDN (nome de domínio *totalmente qualificado*) inclui o nome da zona, enquanto um nome *relativo* não o inclui. Por exemplo, o nome relativo do registro 'www' na zona 'contoso.com' fornece o nome totalmente qualificado do registro 'www.contoso.com'.

Um registro *apex* é um registro DNS na raiz (ou *apex*) de uma zona DNS. Por exemplo, na zona DNS ‘contoso.com’, um registro apex também tem o nome totalmente qualificado 'contoso.com' (ele às vezes é chamado de domínio *sem www*).  Por convenção, o nome relativo '@' é usado para criar registros apex.

### <a name="record-sets"></a>Conjuntos de registros

Às vezes, você precisa criar mais de um registro DNS com determinado nome e tipo. Por exemplo, vamos supor que o site 'www.contoso.com' seja hospedado em dois endereços IP diferentes. O site exige dois registros A diferentes, um para cada endereço IP. Este é um exemplo de um conjunto de registros:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

O DNS do Azure gerencia registros DNS usando *conjuntos de registros*. Um conjunto de registros (também conhecido como conjunto de registros de *recurso*) é uma coleção de registros DNS em uma zona que tem o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registros contém um único registro, mas exemplos como este, nos quais um conjunto de registros contém mais de um registro de exemplos, não são incomuns.

Por exemplo, digamos que você já criou um registro 'www' A na zona 'contoso.com' apontando para o endereço IP '134.170.185.46' (o primeiro registro acima).  Para criar o segundo registro, você deve adicioná-lo ao conjunto de registros existente em vez de criar um novo conjunto de registros.

Os conjuntos de registros SOA e CNAME são exceções. Os padrões DNS não permitem vários registros com o mesmo nome para esses tipos e, portanto, esses conjuntos de registros podem conter apenas um único registro.

### <a name="time-to-live"></a>Vida útil

O tempo de vida, ou TTL, especifica quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado novamente. No exemplo acima, o TTL tem 3600 segundos ou 1 hora.

No DNS do Azure, o TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros no conjunto de registros.  Você pode especificar qualquer valor de TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Registros curinga são retornados como resposta a consultas com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga). Conjuntos de registros curinga têm suporte para todos os tipos de registro, exceto NS e SOA.

Para criar um conjunto de registros curinga, use o nome do conjunto de registros '\*'. Como alternativa, você também pode usar um nome com '\*'como seu rótulo na extremidade esquerda, por exemplo,'\*.foo '.

### <a name="cname-records"></a>Registros CNAME

Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um conjunto de registros CNAME com o nome relativo 'www' e um registro A com o nome relativo 'www' ao mesmo tempo.

Como o apex de zona (nome = '@')) sempre contém os conjuntos de registro NS e SOA criados quando a zona foi criada, não é possível criar um conjunto de registros CNAME no apex da zona.

Essas restrições são provenientes dos padrões DNS e não são limitações do DNS do Azure.

### <a name="ns-records"></a>Registros NS

Um conjunto de registros NS é criado automaticamente no apex de cada zona (nome = '@'),) e é excluído automaticamente quando a zona é excluída (ele não pode ser excluído separadamente).  Você pode modificar o TTL desse conjunto de registros, mas não pode modificar os registros que são pré-configurados para se referirem aos servidores de nomes do DNS do Azure atribuídos à zona.

Você pode criar e excluir outros registros NS dentro da zona, não no apex da zona.  Isso permite que você configure zonas filho (confira [Delegando subdomínios no DNS do Azure](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Registros SOA

Um conjunto de registros SOA é criado automaticamente no apex de cada zona (nome = '@'),) e é excluído automaticamente quando a zona é excluída.  Os registros SOA não podem ser criados ou excluídos separadamente.

Você pode modificar todas as propriedades do registro SOA, exceto a propriedade 'host', que é pré-configurada para se referir ao nome do servidor de nomes primário fornecido pelo DNS do Azure.

### <a name="spf-records"></a>Registros SPF

Os registros SPF (Sender Policy Framework) são usados para especificar quais servidores de email têm permissão para enviar email em nome de um nome de domínio específico.  A configuração correta dos registros SPF é importante para evitar que os destinatários marquem seu email como 'lixo'.

As RFCs do DNS originalmente introduziram um novo tipo de registro 'SPF' para dar suporte a esse cenário. Para dar suporte a servidores de nomes mais antigos, eles também permitiram o uso do tipo de registro TXT para especificar registros SPF.  Essa ambiguidade causou confusão, que foi resolvida pela [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Ela definiu que os registros SPF só devem ser criados com o tipo de registro TXT e substituiu o tipo de registro SPF.

**Os registros SPF têm suporte do DNS do Azure e devem ser criados usando o tipo de registro TXT.** Não há suporte para o tipo de registro SPF obsoleto. Ao [importar um arquivo de zona DNS](dns-import-export.md), eventuais registros SPF que usam o tipo de registro SPF serão convertidos para o tipo de registro TXT.

### <a name="srv-records"></a>Registros SRV

Os [registros SRV](https://en.wikipedia.org/wiki/SRV_record) são usados por vários serviços para especificar locais de servidor. Ao especificar um registro SRV no DNS do Azure:

* O *serviço* e o *protocolo* devem ser especificados como parte do nome do conjunto de registros, prefixado com sublinhados.  Por exemplo, '\_sip.\_tcp.nome'.  Para um registro no apex da zona, não é necessário especificar '@' no nome do registro; basta usar o serviço e o protocolo, por exemplo, '\_sip.\_tcp'.
* A *prioridade*, o *peso*, a *porta* e o *destino* são especificados como parâmetros de cada registro no conjunto de registros.

## <a name="tags-and-metadata"></a>Marcas e metadados

### <a name="tags"></a>Marcas

As marcas consistem em uma lista de pares de nome/valor e são usadas pelo Azure Resource Manager na rotulagem de recursos.  O Azure Resource Manager usa marcas para habilitar exibições filtradas de sua fatura do Azure e também permite que você defina uma política sobre as marcas que são obrigatórias. Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

O DNS do Azure dá suporte ao uso de marcas do Azure Resource Manager em recursos de zona DNS.  Ele não dá suporte a marcas em conjuntos de registros DNS.

### <a name="metadata"></a>Metadados

Como uma alternativa às marcas de conjunto de registros, o DNS do Azure dá suporte à anotação de conjuntos de registros usando 'metadados'.  Semelhante às marcas, os metadados permitem que você associe pares de nome-valor a cada conjunto de registros.  Isso pode ser útil, por exemplo, para registrar a finalidade de cada conjunto de registros.  Diferentemente das marcas, os metadados não podem ser usados para fornecer uma exibição filtrada de sua fatura do Azure e não podem ser especificados em uma política do Azure Resource Manager.

## <a name="limits"></a>limites

Os limites padrão abaixo se aplicam ao usar o DNS do Azure:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar o DNS do Azure, aprenda a [criar uma zona DNS](dns-getstarted-create-dnszone-portal.md) e a [criar registros DNS](dns-getstarted-create-recordset-portal.md).
* Para migrar uma zona DNS existente, aprenda a [importar um arquivo de zona DNS](dns-import-export.md).




<!--HONumber=Nov16_HO3-->


