---
title: Visão geral dos registros de alias do DNS do Azure
description: Visão geral do suporte para registros de alias no DNS do Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092835"
---
# <a name="azure-dns-alias-records-overview"></a>Visão geral dos registros de alias do DNS do Azure

Os registros de alias do DNS do Azure são qualificações em um conjunto de registros DNS. Eles podem fazer referência a outros recursos do Azure dentro de sua zona DNS. Por exemplo, você pode criar um conjunto de registros de alias que faça referência a um endereço IP público do Azure em vez de um registro A. Seu alias registra os pontos definidos em uma instância do serviço de endereço IP público do Azure dinamicamente. Como resultado, o conjunto de registros de alias se atualiza continuamente durante a resolução de DNS.

Um conjunto de registros de alias é suportado para os seguintes tipos de registro em uma zona DNS do Azure: 

- O  
- AAAA 
- CNAME 

> [!NOTE]
> Registros de alias para os tipos de registro A ou AAAA do Azure Traffic Manager são suportados apenas para tipos de terminal externos. Você deve fornecer o endereço IPv4 ou IPv6, conforme apropriado, para terminais externos no Gerenciador de Tráfego. Idealmente, use IPs estáticos para o endereço.

## <a name="capabilities"></a>Funcionalidades

- **Aponte para um recurso IP público de um conjunto de registros DNS A / AAAA.** Você pode criar um conjunto de registros A / AAAA e torná-lo um conjunto de registros de alias para apontar para um recurso IP público.

- **Aponte para um perfil do Gerenciador de Tráfego de um conjunto de registros DNS A / AAAA / CNAME.** Você pode apontar para o CNAME de um perfil do Gerenciador de Tráfego a partir de um conjunto de registros CNAME do DNS. Um exemplo é contoso.trafficmanager.net. Agora, você também pode apontar para um perfil do Gerenciador de Tráfego que tenha pontos de extremidade externos de um registro A ou AAAA definido em sua zona DNS.

   > [!NOTE]
   > Registros de alias para os tipos de registro A ou AAAA para o Gerenciador de Tráfego são suportados apenas para tipos de terminal externos. Você deve fornecer o endereço IPv4 ou IPv6, conforme apropriado, para terminais externos no Gerenciador de Tráfego. Idealmente, use IPs estáticos para o endereço.
   
- **Apontar para outro conjunto de registros DNS dentro da mesma zona.** Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, um conjunto de registros DNS CNAME pode ser um alias para outro conjunto de registros CNAME do mesmo tipo. Essa organização é útil se você quiser que alguns conjuntos de registros sejam aliases e alguns não-aliases.

## <a name="scenarios"></a>Cenários
Existem alguns cenários comuns para registros de alias.

### <a name="prevent-dangling-dns-records"></a>Impedir que os registros DNS pendentes
 Nas zonas DNS do Azure, os registros de alias podem ser usados para acompanhar de perto o ciclo de vida dos recursos do Azure. Os recursos incluem um IP público ou um perfil do Gerenciador de Tráfego. Um problema comum nos registros DNS tradicionais é o registro de registros pendentes. Esse problema ocorre especialmente com tipos de registro A / AAAA ou CNAME. 

Com um registro de zona DNS tradicional, se o IP ou CNAME de destino não existir mais, o registro da zona DNS não o saberá. Como resultado, o registro deve ser atualizado manualmente. Em algumas organizações, essa atualização manual pode não acontecer a tempo. Também pode ser problemático devido à separação de funções e níveis de permissão associados.

Por exemplo, uma função pode ter autoridade para excluir um CNAME ou endereço IP que pertence a um aplicativo. Mas não tem autoridade suficiente para atualizar o registro DNS que aponta para esses destinos. Um atraso ocorre entre o momento em que o IP ou o CNAME é excluído e o registro DNS que aponta para ele é removido. Esse atraso de tempo pode causar uma interrupção nos usuários.

Os registros de alias removem a complexidade associada a esse cenário. Eles ajudam a evitar referências pendentes. Tomemos, por exemplo, um registro DNS qualificado como um registro de alias para apontar para um IP público ou um perfil do Gerenciador de tráfego. Se esses recursos subjacentes forem excluídos, o registro do alias do DNS será removido ao mesmo tempo. Esse processo garante que os usuários nunca sofram uma interrupção.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Atualizar as zonas DNS automaticamente quando os IPs do aplicativo mudam

Este cenário é semelhante ao anterior. Talvez um aplicativo seja movido ou a máquina virtual subjacente seja reiniciada. Um registro de alias é atualizado automaticamente quando o endereço IP é alterado para o recurso IP público subjacente. Para evitar possíveis riscos de segurança, direcione os usuários para outro aplicativo que tenha o endereço IP antigo.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hospedar aplicativos com balanceamento de carga no ápice da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no ápice da zona. Um exemplo é contoso.com. Essa restrição apresenta um problema para os proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do Gerenciador de Tráfego. Não é possível apontar para o perfil do Gerenciador de Tráfego no registro do ápice da zona. Como resultado, os proprietários do aplicativo devem usar uma solução alternativa. Um redirecionamento na camada de aplicativo deve redirecionar do ápice da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com para www.contoso.com. Esse arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registros de alias, esse problema não existe mais. Agora, os proprietários do aplicativo podem apontar seu registro do ápice da zona para um perfil do Gerenciador de Tráfego que tenha nós de extremidade externos. Os proprietários de aplicativos podem apontar para o mesmo perfil do Gerenciador de tráfego que é usado para qualquer outro domínio em sua zona DNS. Por exemplo, contoso.com e www.contoso.com podem apontar para o mesmo perfil do Gerenciador de Tráfego. Esse é o caso, desde que o perfil do Gerenciador de Tráfego tenha apenas endpoints externos configurados.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre registros de alias, consulte os seguintes artigos:

- [Tutorial: configure um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configurar um registro de alias para dar suporte a nomes de domínio com o Gerenciador de tráfego do apex](tutorial-alias-tm.md)
- [PERGUNTAS FREQUENTES SOBRE O DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
