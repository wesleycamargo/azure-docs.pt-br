---
title: DNS no Azure Stack | Microsoft Docs
description: Usando o DNS no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 3a4efb6282a0aca8173403009fd58e9154a91b2b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728939"
---
# <a name="using-dns-in-azure-stack"></a>Usando o DNS no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack dá suporte aos seguintes recursos de sistema de nome de domínio (DNS):

* Resolução de nome de host DNS
* Criar e gerenciar zonas DNS e registros usando a API

## <a name="support-for-dns-hostname-resolution"></a>Suporte para a resolução de nome de host DNS

Você pode especificar um rótulo de nome de domínio DNS para recursos de IP público. Usa o Azure Stack **domainnamelabel.location.cloudapp.azurestack.external** para o nome de rótulo e mapeia-o para o endereço IP público no Azure Stack gerenciados servidores DNS.

Por exemplo, se você criar um recurso IP público com **contoso** como um rótulo de nome de domínio no local do Azure Stack local, o [o nome de domínio totalmente qualificado (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **Contoso.local.cloudapp.azurestack.external** resolve para o endereço IP público do recurso. Você pode usar este FQDN para criar um registro CNAME que aponta para o endereço IP público no Azure Stack de domínio personalizado.

Para saber mais sobre a resolução de nome, consulte o [resolução de DNS](../../dns/dns-for-azure-services.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artigo.

> [!IMPORTANT]
> Cada rótulo de nome de domínio que você cria deve ser exclusivo dentro de seu local do Azure Stack.

A captura de tela a seguir mostra a **criar endereço IP público** caixa de diálogo para criar um endereço IP público usando o portal:

![Criar um endereço IP público](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Cenário de exemplo

Você tem um balanceador de carga de processamento de solicitações de um aplicativo web. Por trás da carga balanceador é um site da web em execução em uma ou mais máquinas virtuais. Você pode acessar o site da web com balanceamento de carga usando um nome DNS, em vez de um endereço IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Criar e gerenciar zonas DNS e registros usando a API

Você pode criar e gerenciar zonas DNS e registros no Azure Stack.

O Azure Stack fornece um serviço de DNS semelhante ao Azure, usando as APIs que são consistentes com as APIs de DNS do Azure.  Ao hospedar domínios no DNS do Azure Stack, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs e ferramentas. Você pode também usar a mesma cobrança e suporte que outros serviços do Azure.

A infraestrutura do DNS do Azure Stack é mais compacta do que o Azure. O tamanho e local de uma implantação do Azure Stack afeta o escopo, escala e desempenho do DNS. Isso também significa que o desempenho, disponibilidade, distribuição global e alta disponibilidade podem variar de implantação para implantação.

## <a name="comparison-with-azure-dns"></a>Comparação com o DNS do Azure

O DNS no Azure Stack é semelhante do DNS no Azure, mas há algumas exceções importantes:

* **Não oferece suporte a registros AAAA**: O Azure Stack não dá suporte a registros AAAA, porque o Azure Stack não dê suporte a endereços IPv6. Isso é uma diferença importante entre o DNS no Azure e o Azure Stack.

* **Não é multilocatário**: O serviço DNS no Azure Stack não é multilocatário. Cada locatário não é possível criar a mesma zona DNS. Somente a primeira assinatura que tenta criar a zona for bem-sucedida e as solicitações subsequentes falham. Isso é uma diferença importante entre o Azure e o DNS do Azure Stack.

* **Marcas, metadados e as Etags**: Há pequenas diferenças em como a pilha do Azure lida com os limites, metadados, as Etags e marcas.

Para saber mais sobre o DNS do Azure, consulte [zonas e registros DNS](../../dns/dns-zones-records.md).

### <a name="tags"></a>Marcas

O DNS do Azure Stack dá suporte ao marcas do Azure Resource Manager em recursos de zona DNS. Ele não oferece suporte a marcas em conjuntos de registros de DNS, embora, como alternativa, **metadados** tem suporte em conjuntos de registros de DNS, conforme explicado na próxima seção.

### <a name="metadata"></a>Metadados

Como alternativa às marcas de conjunto de registros, o DNS do Azure Stack dá suporte à anotação usando os conjuntos de registros *metadados*. Semelhante às marcas, os metadados permitem que você associe pares de nome-valor a cada conjunto de registros. Por exemplo, isso pode ser útil para registrar a finalidade de cada conjunto de registros. Diferentemente das marcas, você não pode usar metadados para fornecer uma exibição filtrada de sua fatura do Azure e metadados não podem ser especificado em uma política do Azure Resource Manager.

### <a name="etags"></a>ETags

Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor sabe que substituiu as alterações criadas por outra pessoa?

Usa o DNS do Azure Stack *Etags* para manipular com segurança as alterações simultâneas para o mesmo recurso. As Etags são diferentes do Azure Resource Manager *marcas*. Cada recurso DNS (zona ou conjunto de registros) tem uma Etag associada a ele. Quando um recurso for recuperado, a Etag também é recuperada. Ao atualizar um recurso, você pode escolher devolver a Etag para que o DNS do Azure Stack pode verificar se a Etag no servidor é correspondente. Uma vez que cada atualização a um recurso resulta em uma Etag sendo gerada novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. As Etags também podem ser usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, os cmdlets do PowerShell do DNS do Azure Stack usar Etags bloquear alterações simultâneas às zonas e conjuntos de registros. Você pode usar a opção `-Overwrite` alternar para suprimir as verificações de Etag, que faz com que as alterações simultâneas que ocorreram para ser substituído.

O nível da API de REST do DNS do Azure Stack, as Etags são especificadas usando cabeçalhos HTTP. Seu comportamento é descrito na tabela a seguir:

| Cabeçalho | Comportamento|
|--------|---------|
| Nenhum   | PUT sempre terá êxito (nenhuma verificação de Etag)|
| If-match| PUT só terá êxito se o recurso existir e a Etag corresponder|
| If-match *| PUT só terá êxito se houver recursos|
| If-none-match *| PUT só terá êxito se não houver recursos|

### <a name="limits"></a>limites

Os limites padrão a seguir se aplicam ao usar o DNS do Azure Stack:

| Recurso| Limite padrão|
|---------|--------------|
| Zonas por assinatura| 100|
| Conjuntos de registros por zona| 5.000|
| Registros por conjunto de registros| 20|

## <a name="next-steps"></a>Próximas etapas

- [Apresentando os iDNS para o Azure Stack](azure-stack-understanding-dns.md)
