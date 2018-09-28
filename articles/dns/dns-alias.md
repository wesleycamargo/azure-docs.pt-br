---
title: Visão geral dos registros de alias do DNS do Azure
description: Visão geral do suporte para registros de alias no DNS do Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957534"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificados em um conjunto de registros DNS que permite fazer referência a outros recursos do Azure na sua zona DNS. Os registros de alias do DNS do Azure podem ser encontrados em um conjunto de domínios DNS. Como seu recordset de alias aponta para uma instância do serviço de endereço IP público do Azure dinamicamente, o conjunto de registros de alias se atualiza continuamente durante a resolução de DNS.

Um conjunto de registros de alias é suportado para os seguintes tipos de registro em uma zona DNS do Azure: A, AAAA e CNAME. 

> [!NOTE]
> Registros de alias para os tipos de registro A ou AAAA para o Gerenciador de Tráfego são suportados apenas para os tipos de Nó de Extremidade Externa. Você deve fornecer o endereço IPv4 ou IPv6 (idealmente IPs estáticos), conforme apropriado para pontos de extremidade externos no Gerenciador de Tráfego.

## <a name="capabilities"></a>Funcionalidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA**. Você pode criar um conjunto de registros A / AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público.
- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME**. Além de poder apontar para o CNAME de um perfil do Gerenciador de Tráfego (por exemplo: contoso.trafficmanager.net) a partir de um conjunto de registros DNS CNAME, agora você também pode apontar para um perfil do Gerenciador de Tráfego que tenha pontos de extremidade externos, de um A ou Conjunto de registros AAAA na sua zona DNS.
   > [!NOTE]
   > Registros de alias para os tipos de registro A ou AAAA para o Gerenciador de Tráfego são suportados apenas para os tipos de Nó de Extremidade Externa. Você deve fornecer o endereço IPv4 ou IPv6 (idealmente IPs estáticos), conforme apropriado para pontos de extremidade externos no Gerenciador de Tráfego.
- **Apontar para outro conjunto de registros DNS dentro da mesma zona**. Registros de alias podem fazer referência a outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros DNS CNAME como um alias para outro conjunto de registros CNAME do mesmo tipo. Isso é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não sejam aliases em termos de comportamento.

## <a name="scenarios"></a>Cenários
Há alguns cenários comuns para registros de Alias:

### <a name="prevent-dangling-dns-records"></a>Impedir que os registros DNS pendentes
De dentro das zonas DNS do Azure, os registros de alias podem ser usados para acompanhar de perto o ciclo de vida dos recursos do Azure, como um perfil de IP Público e Gerenciador de Tráfego. Um dos problemas comuns com os registros DNS tradicionais é "registros pendentes", especialmente com os tipos de registro A / AAAA ou CNAME. 

Com um registro de zona DNS tradicional, se o IP de destino ou CNAME não existir mais, o registro de zona DNS não tem conhecimento desse fato e precisa ser atualizado manualmente. Em algumas organizações, essa atualização manual pode não acontecer a tempo ou pode ser problemática devido à separação de funções e níveis de permissão associados.

Por exemplo, a função que tem autoridade para excluir um CNAME ou endereço IP pertencente a um aplicativo pode não ter autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Como resultado, pode haver um atraso entre o momento em que o IP ou o CNAME é excluído e o registro DNS que aponta para ele é removido, o que poderia causar uma interrupção para os usuários finais.

Os registros de alias removem a complexidade associada a esse cenário e ajudam a evitar essas referências pendentes. Quando um registro DNS é qualificado como um registro de alias para apontar para um IP Público ou um perfil do Gerenciador de Tráfego, e se esses recursos subjacentes forem excluídos, o registro de alias do DNS também será removido ao mesmo tempo. Isso garante que os usuários finais nunca sofram uma interrupção.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Atualizar as zonas DNS automaticamente quando os IPs do aplicativo mudam

Semelhante ao cenário anterior, se um aplicativo for movido ou se a máquina virtual subjacente for reiniciada, um registro de alias será atualizado automaticamente quando o endereço IP for alterado para o recurso IP público subjacente. Você pode evitar possíveis riscos de segurança se os usuários finais forem direcionados para outro aplicativo que tenha o endereço IP antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Aplicativos de carga balanceada de host no ápice da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no ápice da zona (por exemplo: contoso.com). Isso apresenta um problema para os proprietários do aplicativo que têm a carga balanceada aplicativos por trás de um Gerenciador de tráfego, não era possível apontar o perfil do Gerenciador de tráfego do registro de apex de zona. Como resultado, os proprietários do aplicativo eram forçados a usar uma solução alternativa. Por exemplo, um redirecionamento na camada de aplicativo para redirecionar o ápice da zona para outro domínio (de contoso.com para www.contoso.com). Isso apresenta um ponto único de falha em termos de funcionalidade de redirecionamento.

Com registros de alias, esse problema não existe mais. Os proprietários do aplicativo agora podem apontar seu registro apex de zona para um perfil do Gerenciador de tráfego que tem pontos de extremidade externos. Com essa funcionalidade, os proprietários do aplicativo podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS. Por exemplo, contoso.com e www.contoso.com podem apontam para o mesmo perfil do Gerenciador de tráfego, desde que o perfil do Gerenciador de tráfego tenha apenas pontos de extremidade externos configurados.

## <a name="next-steps"></a>Próximas etapas

Para Leia mais sobre a registros de alias, consulte os artigos a seguir:

- [Tutorial: configure um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
