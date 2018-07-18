---
title: DNS na pilha do Azure | Microsoft Docs
description: Usando o DNS na pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="using-dns-in-azure-stack"></a>Usando o DNS na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Azure suporta os seguintes recursos do sistema de nome de domínio (DNS):

* Resolução de nome de host DNS
* Criar e gerenciar zonas DNS e registros de uso da API

## <a name="support-for-dns-hostname-resolution"></a>Suporte para a resolução de nome de host DNS

Você pode especificar um rótulo de nome de domínio DNS para recursos do IP público. A pilha do Azure usa *domainnamelabel.location*. cloudapp.azurestack.external para o nome de rótulo e mapas de endereço para o IP público na pilha do Azure gerenciados servidores DNS.

Por exemplo, se você criar um recurso IP público com **contoso** como um rótulo de nome de domínio no local de pilha do Local do Azure, o [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **Contoso.local.cloudapp.azurestack.external** resolve para o endereço IP público do recurso. Você pode usar o FQDN para criar um domínio personalizado registro CNAME que aponte para o endereço IP público na pilha do Azure.

Para obter mais informações sobre resolução de nomes, consulte o [resolução DNS](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artigo.

> [!IMPORTANT]
> Cada rótulo de nome de domínio que você criar deve ser exclusivo dentro de seu local de pilha do Azure.

Captura de tela seguinte mostra o **criar endereço IP público** caixa de diálogo para criar um endereço IP público usando o portal.

![Criar um endereço IP público](media/azure-stack-whats-new-dns/image01.png)

**Cenário de exemplo**

Você tem um balanceador de carga de processamento de solicitações de um aplicativo web. Por trás da carga balanceador é um site da web em execução em uma ou mais máquinas virtuais. Você pode acessar o site com balanceamento de carga usando um nome DNS, em vez de um endereço IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Criar e gerenciar as zonas DNS e registros de uso da API

Você pode criar e gerenciar as zonas DNS e registros na pilha do Azure.

Pilha do Azure fornece um serviço DNS como o Azure, usando as APIs que são consistentes com as APIs do DNS do Azure.  Ao hospedar seus domínios no DNS de pilha do Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs e ferramentas. Você também pode usar a mesma cobrança e oferecem suporte a como os outros serviços do Azure.

A infraestrutura de DNS de pilha do Azure é mais compacta do que a do Azure. O tamanho e o local de uma implantação do Azure pilha afetará escopo, escala e desempenho do DNS. Isso também significa que o desempenho, disponibilidade, distribuição global e alta disponibilidade podem variar de implantação para implantação.

## <a name="comparison-with-azure-dns"></a>Comparação com o DNS do Azure

DNS na pilha do Azure é semelhante ao DNS no Azure, mas há exceções importantes que você precisa entender.

* **Não dá suporte a registros AAAA**

    A pilha do Azure não oferece suporte a registros AAAA porque a pilha do Azure não oferece suporte a endereços IPv6.  Isso é uma diferença importante entre DNS no Azure e a pilha do Azure.
* **Não é multilocatário**

    O serviço DNS no Azure pilha não é multilocatário. Cada locatário não é possível criar a mesma zona DNS. Somente a primeira assinatura que tenta criar a zona for bem-sucedida e solicitações subsequentes falham.  É um problema conhecido e uma diferença importante entre o Azure e DNS da pilha do Azure. Esse problema será resolvido em uma versão futura.
* **Etags, metadados e marcas**

    Há pequenas diferenças em como a pilha do Azure lida com marcas, metadados, Etags e limites.

Para saber mais sobre o DNS do Azure, consulte [as zonas DNS e os registros](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Etags, metadados e marcas

**Marcas**

DNS de pilha do Azure oferece suporte ao uso do Azure Resource Manager marcas em recursos de zona DNS. Ele não oferece suporte a marcas em conjuntos de registros de DNS, embora como uma alternativa 'metadata' é suportada no registro DNS define conforme explicado a seguir.

**Metadados**

Como uma alternativa às marcas do conjunto de registros, DNS de pilha do Azure oferece suporte a anotações conjuntos de registros usando 'metadata'. Semelhante às marcas, os metadados permitem que você associe pares de nome-valor a cada conjunto de registros. Por exemplo, isso pode ser útil para gravar a finalidade de cada conjunto de registros. Diferentemente das marcas, os metadados não podem ser usados para fornecer uma exibição filtrada de sua fatura do Azure e não podem ser especificados em uma política do Azure Resource Manager.

**ETags**

Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor sabe que substituiu as alterações criadas por outra pessoa?

DNS de pilha do Azure usa Etags para lidar com as alterações simultâneas para o mesmo recurso com segurança. ETags são separadas do Azure Resource Manager 'Tags'. Cada recurso DNS (zona ou conjunto de registros) tem uma Etag associada a ele. Sempre que um recurso é recuperado, a Etag também é recuperada. Ao atualizar um recurso, você pode escolher devolver o Etag para que o DNS de pilha do Azure pode verificar se a Etag no coincide com o servidor. Uma vez que cada atualização a um recurso resulta em uma Etag sendo gerada novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. As Etags também podem ser usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, o DNS de pilha do Azure PowerShell usa Etags para bloquear as alterações simultâneas para zonas e conjuntos de registro. O switch opcional *-Overwrite* pode ser usado para suprimir as verificações de Etag. Nesse caso, as alterações simultâneas que ocorrerem são substituídas.

O nível da API de REST do DNS do Azure pilha, Etags são especificadas usando cabeçalhos HTTP. Seu comportamento é descrito na tabela a seguir:

| Cabeçalho | Comportamento|
|--------|---------|
| Nenhum   | PUT sempre terá êxito (nenhuma verificação de Etag)|
| If-match| PUT só terá êxito se o recurso existir e a Etag corresponder|
| If-match *| PUT só terá êxito se houver recursos|
| If-none-match *| PUT só terá êxito se não houver recursos|

### <a name="limits"></a>limites

Os seguintes limites padrão se aplicam ao usar o DNS de pilha do Azure:

| Recurso| Limite padrão|
|---------|--------------|
| Zonas por assinatura| 100|
| Conjuntos de registros por zona| 5.000|
| Registros por conjunto de registros| 20|

## <a name="next-steps"></a>Próximas etapas

[Introdução ao iDNS pilha do Azure](azure-stack-understanding-dns.md)
