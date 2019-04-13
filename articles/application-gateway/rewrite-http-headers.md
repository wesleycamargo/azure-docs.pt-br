---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da funcionalidade de reconfiguração de cabeçalhos HTTP no Gateway de Aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544136"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Reescreva os cabeçalhos HTTP com o Gateway de Aplicativo (visualização pública)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cabeçalhos HTTP permitem que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta. Regravação esses cabeçalhos HTTP ajuda a realizar vários cenários importantes, como a adição de campos de cabeçalho relacionados à segurança, como a HSTS / X-XSS-Protection, remover o cabeçalho de resposta de campos que pode revelar informações confidenciais, informações de porta de remoção Cabeçalhos X-Forwarded-For, etc. O gateway de aplicativo oferece suporte a capacidade de adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP durante a solicitação e pacotes de resposta se mover entre os pools de back-end e de cliente. Ele também fornece a capacidade de adicionar condições para garantir que os cabeçalhos especificados são reescritos somente quando determinadas condições forem atendidas.
> [!NOTE]
>
> O suporte à reescrita de cabeçalho HTTP só está disponível para [nova SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>Reescreva cabeçalhos com suporte para

O recurso permite que você reescrever todos os cabeçalhos de solicitação e resposta bloqueando os cabeçalhos de Host, a Conexão e a atualização. Você também pode usar o gateway de aplicativo para criar cabeçalhos personalizados e adicioná-los para a solicitação e as respostas que estão sendo roteadas por ele. 

## <a name="rewrite-conditions"></a>Condições de regravação

Usando a reescrita condições, você pode avaliar o conteúdo das solicitações de HTTP (S) e as respostas e realizar um cabeçalho de reescrever somente quando uma ou mais condições são atendidas. Os seguintes tipos de 3 de variáveis são usados pelo gateway de aplicativo para avaliar o conteúdo das solicitações de HTTP (S) e as respostas:

- Cabeçalhos HTTP na solicitação
- Cabeçalhos HTTP na resposta
- Variáveis de servidor de gateway de aplicativo

Uma condição pode ser usada para avaliar se a variável especificada estiver presente, se a variável especificada corresponde exatamente a um valor específico, ou se a variável especificada corresponde exatamente a um padrão específico. [Biblioteca do Perl compatível Regular expressões (PCRE)](https://www.pcre.org/) é usado para implementar as condições de correspondência de padrão de expressão regular. Para saber mais sobre a sintaxe de expressão regular, consulte o [página do manual de expressões regulares do Perl](http://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Ações de reconfiguração

Reescreva as ações são usadas para especificar os cabeçalhos de solicitação e resposta que você pretende reescrever e o novo valor precisam ser reescrito para os cabeçalhos originais. Você pode criar um novo cabeçalho, modifique o valor de um cabeçalho existente ou excluir um cabeçalho existente. O valor de um novo cabeçalho ou um cabeçalho existente pode ser definido para os seguintes tipos de valores:

- Texto 
- Cabeçalho da solicitação: Para especificar um cabeçalho de solicitação, você precisa usar a sintaxe {http_req_*headerName*}
- Cabeçalho de resposta: Para especificar um cabeçalho de resposta, você precisa usar a sintaxe {http_resp_*headerName*}
- Variável de servidor: Para especificar uma variável de servidor, você precisa usar a sintaxe {var_*serverVariable*}
- Combinação de texto, o cabeçalho de solicitação, o cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

O gateway de aplicativo usa variáveis de servidor para armazenar informações úteis sobre o servidor, a conexão com o cliente e a solicitação atual na conexão, como o endereço IP do cliente ou o tipo de navegador da web. Essas variáveis são alteradas dinamicamente, como quando uma nova página é carregada ou um formulário é postado. Você pode usar essas variáveis de servidor para avaliar as condições de regravação e reescreva cabeçalhos. 

O gateway de aplicativo dá suporte aos seguintes variáveis de servidor:

| Variáveis do servidor compatíveis | DESCRIÇÃO                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Contém o "X-Forwarded-For" cliente solicitação campo de cabeçalho com o `client_ip` (explicado a tabela abaixo) variável anexado a ele no formato (IP1, IP2, lt;ip3,...). Se o campo "X-Forwarded-For" não está presente no cabeçalho da solicitação do cliente, o `add_x_forwarded_for_proxy` variável é igual ao `$client_ip` variável. Essa variável é particularmente útil em cenários em que os clientes pretendem reescreva o cabeçalho X-Forwarded-For definido pelo Gateway de aplicativo, de modo que o cabeçalho contém somente o endereço IP sem as informações de porta. |
| ciphers_supported          | retorna a lista de codificações com suporte pelo cliente          |
| ciphers_used               | Retorna a cadeia de caracteres de criptografia usada para uma conexão SSL estabelecida |
| client_ip                  | Endereço IP do cliente do qual o gateway de aplicativo recebeu a solicitação. Se houver um proxy reverso antes do gateway de aplicativo e o cliente de origem, então *client_ip* retornará o endereço IP do proxy reverso. |
| client_port                | porta do cliente                                                  |
| client_tcp_rtt             | informações sobre a conexão TCP do cliente; disponível em sistemas que são compatíveis com a opção de soquete TCP_INFO |
| client_user                | ao usar a autenticação de HTTP, o nome de usuário fornecido para autenticação |
| host                       | nessa ordem de precedência: nome do host da linha de solicitação ou nome de host do campo de cabeçalho de solicitação de "Host" ou o nome de servidor corresponde a uma solicitação |
| cookie_*nome*              | o *nome* cookie                                            |
| http_method                | o método usado para fazer a solicitação de URL. Por exemplo GET, POST etc. |
| http_status                | estado da sessão, por exemplo: 200, 400, 403 etc.                       |
| http_version               | protocolo de solicitação, normalmente, "HTTP/1.0", "HTTP 1.1" ou "HTTP 2.0" |
| query_string               | a lista de valores de variável de pares que seguem o "?" na URL solicitada. |
| received_bytes             | comprimento da solicitação (inclusive a linha de solicitação, o cabeçalho e o corpo da solicitação) |
| request_query              | argumentos na linha da solicitação                                |
| request_scheme             | esquema da solicitação, "http" ou "https"                            |
| request_uri                | URI da solicitação original completa (com argumentos)                   |
| sent_bytes                 | número de bytes enviados a um cliente                             |
| server_port                | porta do servidor, que aceita uma solicitação                 |
| ssl_connection_protocol    | retorna o protocolo de uma conexão SSL estabelecida        |
| ssl_enabled                | "on" se a conexão opera no modo SSL, ou uma cadeia de caracteres vazia caso contrário |

## <a name="rewrite-configuration"></a>Configuração de regravação

Para configurar a reconfiguração do cabeçalho HTTP, você precisará:

1. Criar os novos objetos necessários para reescrever os cabeçalhos http:

   - **Reescreva a ação**: usado para especificar a solicitação e campos de cabeçalho de solicitação que você pretende reescrever e o novo valor precisam ser reescrito para os cabeçalhos originais. Você pode optar por associar uma ou mais condições de regravação com uma ação de regravação.

   - **Reescreva a condição**: É uma configuração opcional. Se uma condição de regravação é adicionada, ela avaliará o conteúdo das solicitações de HTTP (S) e as respostas. A decisão de executar a ação de regravação associada com a condição de regravação se baseará se a resposta ou solicitação de HTTP (S) de correspondência com a condição de regravação. 

     Se mais de um condições são associados com uma ação e, em seguida, a ação será executada somente quando todas as condições forem atendidas, ou seja, será executada uma operação AND lógica.

   - **Regra de reescrita de**: regra de regravação contém vários ação de regravação - reescrever combinações de condição.

   - **Sequência de regra**: ajuda a determinar a ordem na qual as diferentes regras de reescrita seja executado. Isso é útil quando há várias regras de reescrita em um conjunto de regravação. A regra de reconfiguração com o menor valor de sequência de regra é executada pela primeira vez. Se você fornecer a mesma sequência de regra para duas regras de regravação a ordem de execução será não determinística.

   - **Reescreva o conjunto**: contém várias regras de reconfiguração que serão associadas a uma regra de roteamento de solicitação.

2. Será necessário anexar o conjunto de reconfiguração (*rewriteRuleSet*) com uma regra de roteamento. Isso ocorre porque a configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de redirecionamento é associada a um ouvinte de origem e será uma reescrita de cabeçalho global. Quando uma regra de roteamento com base em caminho é usada, a configuração de redirecionamento será definida no mapa de caminho de URL. Então, ela se aplica somente a área de caminho específico de um site.

Você pode criar vários conjuntos de regravação de cabeçalho http e cada conjunto de reconfiguração pode ser aplicado a vários ouvintes. No entanto, você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Alguns dos cenários comuns que exigem reconfiguração do cabeçalho são mencionadas a seguir.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta do cabeçalho X-Forwarded-For

O gateway de aplicativo insere o cabeçalho X-Forwarded-For para todas as solicitações antes de encaminhar as solicitações para o back-end. O formato para esse cabeçalho é uma lista separada por vírgulas de IP: porta. No entanto, pode haver situações em que os servidores de back-end exigem o cabeçalho para conter apenas endereços IP. Para a realização de tais cenários, reescrita de cabeçalho pode ser usada para remover as informações de porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isso é definir o cabeçalho para a variável de servidor add_x_forwarded_for_proxy. 

![Remover porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Modificar a URL de redirecionamento

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquele especificado pelo aplicativo de back-end. Um cenário é quando um serviço de aplicativo é hospedado atrás de um gateway de aplicativo e requer que o cliente fazer um redirecionamento para seu caminho relativo (redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2). 

Como o serviço de aplicativo é um serviço multilocatário, ele usa o cabeçalho de host na solicitação para rotear para o ponto de extremidade correto. Serviços de aplicativos tem um nome de domínio padrão de *. azurewebsites.net (digamos, contoso.azurewebsites.net), que é diferente do nome de domínio de gateway de aplicativo (digamos, contoso.com). Como a solicitação original do cliente tem um nome de domínio contoso.com do gateway de aplicativo como o nome do host, o gateway de aplicativo altera o nome do host para contoso.azurewebsites.net, para que o serviço de aplicativo pode encaminhá-lo para o ponto de extremidade correto. Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho location da resposta como a solicitação, que ele recebe do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2, em vez de passar por meio do gateway de aplicativo (contoso.com/path2). Ignorar o gateway de aplicativo não é desejável. 

Esse problema pode ser resolvido, definindo o nome do host no cabeçalho de local para o nome de domínio do gateway de aplicativo. Para fazer isso, você pode criar uma regra de reescrita com uma condição que avalia se o cabeçalho location na resposta contém azurewebsites.net inserindo `(https?):\/\/.*azurewebsites\.net(.*)$` como o padrão e executa uma ação para reescrever o cabeçalho de local para que o gateway de aplicativo nome do host, inserindo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho.

![Modificar cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar os cabeçalhos HTTP de segurança para evitar vulnerabilidades

Várias vulnerabilidades de segurança podem ser corrigidas com a implementação de cabeçalhos necessários na resposta do aplicativo. Alguns desses cabeçalhos de segurança são X-XSS-Protection, segurança de transporte estrito, Content-Security-Policy, etc. Você pode usar o gateway de aplicativo para definir esses cabeçalhos de todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>Limitações

- Reescrever os cabeçalhos de Conexão, atualização e o Host ainda não é suportado.

- Nomes de cabeçalho podem conter qualquer caractere alfanumérico e símbolos específicos, conforme definido em [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No entanto, atualmente não temos suporte para o caractere especial “sublinhado” (\_) no nome do Cabeçalho. 

## <a name="need-help"></a>Precisa de ajuda?

Entre em contato conosco em [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) caso você precise de ajuda com essa funcionalidade.

## <a name="next-steps"></a>Próximas etapas

Para saber como reescreva cabeçalhos HTTP, consulte:

-  [Reescreva cabeçalhos HTTP usando o portal do Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Reescreva cabeçalhos HTTP usando o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)