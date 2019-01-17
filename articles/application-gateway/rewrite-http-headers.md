---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da funcionalidade de reconfiguração de cabeçalhos HTTP no Gateway de Aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 3e40dbb96b27df4b228c52e7a8f70d047a556c31
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198518"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Reescreva os cabeçalhos HTTP com o Gateway de Aplicativo (visualização pública)

Os cabeçalhos HTTP permitem que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta. A reescrita desses cabeçalhos HTTP ajuda você a realizar vários cenários importantes, como a adição de campos de cabeçalho relacionados à Segurança, como HSTS/X-XSS-Protection, ou a remoção de campos de cabeçalho que podem revelar informações confidenciais, como o nome do servidor back-end.

O Gateway de Aplicativo agora dá suporte à capacidade de rescrever cabeçalhos das solicitações HTTP de entrada, bem como das respostas HTTP de saída. Você poderá adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP, enquanto os pacotes de solicitação/resposta são transferidos entre os pools de back-end e do cliente. Você pode reescrever campos de cabeçalho padrão, bem como não padrão.

> [!NOTE] 
>
> O suporte à reescrita de cabeçalho HTTP só está disponível para [nova SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Ofertas de suporte de reescrita de cabeçalho do Gateway de Aplicativo:

- **reescrita do cabeçalho global**: Você pode reescrever cabeçalhos específicos para todas as solicitações e respostas que pertencem ao site.
- **Cabeçalho com base no caminho de reescrita**: esse tipo de reescrita permite a reescrita de cabeçalho para somente essas solicitações e respostas que pertencem a apenas uma área específica do site, por exemplo uma área de carrinho de compras que é denotada por /cart/*.

Com essa alteração, você precisa:

1. Criar os novos objetos necessários para reescrever os cabeçalhos http: 
   - **RequestHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
   - **ResponseHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
   - **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e de resposta especificados acima. 
   - **RewriteRule**: este objeto contém todos os *actionSets* especificados acima. 
   - **RewriteRuleSet**- este objeto contém todos os *rewriteRules* e deverá ser anexado a uma regra de roteamento de solicitação - básica ou com base no caminho.
2. Em seguida, você precisará anexar o conjunto de regras de reescrita à uma regra de roteamento. Depois de criada, essa configuração de reescrita é anexada ao ouvinte de origem por meio de uma regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de redirecionamento é associada a um ouvinte de origem e será uma reescrita de cabeçalho global. Quando uma regra de roteamento com base em caminho é usada, a configuração de redirecionamento será definida no mapa de caminho de URL. Então, ela se aplica somente a área de caminho específico de um site.

Você pode criar vários conjuntos de regras de reescrita para cabeçalho http e cada conjunto de regras de reconfiguração pode ser aplicado a vários ouvintes. No entanto, você pode aplicar apenas uma regra de reescrita de http definida como um ouvinte específico.

Você pode reescrever o valor nos cabeçalhos para:

- Valor de texto. 

  *Exemplo:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Valor de outro cabeçalho. 

  *Exemplo 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Para especificar um cabeçalho de solicitação, você precisa usar a sintaxe: {http_req_headerName}

  *Exemplo 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Para especificar o cabeçalho de resposta, você precisa usar a sintaxe: {http_resp_headerName}

- Valor de suporte para variáveis de servidor.

  *Exemplo:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Para especificar uma variável de servidor, você precisa usar a sintaxe: {var_serverVariable}

- Uma combinação das opções acima.

## <a name="server-variables"></a>Variáveis de servidor

Variáveis de servidor armazenam informações úteis sobre um servidor Web. Essas variáveis fornecem informações sobre o servidor, a conexão com o cliente e a solicitação atual na conexão, como o tipo de navegador da Web ou o endereço IP do cliente. Elas são alteradas dinamicamente, como quando uma nova página é carregada ou um formulário é postado.  Usando essas variáveis, os usuários podem configurar cabeçalhos de solicitação, bem como cabeçalhos de resposta. 

Essa funcionalidade dá suporte à regravação de cabeçalhos para as seguintes variáveis de servidor:

| Variáveis do servidor compatíveis | DESCRIÇÃO                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | retorna a lista de codificações com suporte pelo cliente          |
| ciphers_used               | Retorna a cadeia de caracteres de criptografia usada para uma conexão SSL estabelecida |
| client_latitude            | para determinar o país, região e cidade, dependendo do endereço IP do cliente |
| client_longitude           | para determinar o país, região e cidade, dependendo do endereço IP do cliente |
| client_port                | porta do cliente                                                  |
| client_tcp_rtt             | informações sobre a conexão TCP do cliente; disponível em sistemas que são compatíveis com a opção de soquete TCP_INFO |
| client_user                | ao usar a autenticação de HTTP, o nome de usuário fornecido para autenticação |
| host                       | nessa ordem de precedência: nome do host da linha de solicitação ou nome de host do campo de cabeçalho de solicitação de "Host" ou o nome de servidor corresponde a uma solicitação |
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

## <a name="limitations"></a>Limitações

- No momento, essa capacidade de reconfigurar os cabeçalhos HTTP só está disponível por meio do Azure PowerShell, API do Azure e SDK do Azure. Suporte por meio da CLI e portal do Azure estará disponível em breve.

- O suporte à reescrita de cabeçalho HTTP só está disponível na nova SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). A funcionalidade não terá suporte na SKU antiga.

- Ainda não há suporte para reescrever os cabeçalhos do Connect, Upgrade e Host.

- Duas variáveis importantes do servidor, client_ip (endereço IP do cliente que está fazendo a solicitação) e cookie_*nome* (o *nome* cookie), ainda não são compatíveis. A variável do servidor client_ip é particularmente útil em cenários em que os clientes pretendem reescrever o cabeçalho x-forwarded-for definido pelo Gateway de Aplicativo, de forma que o cabeçalho contenha apenas o endereço IP do cliente e não as informações de porta.

  Em breve, essas variáveis do servidor serão compatíveis.

- A funcionalidade para reescrever condicionalmente os cabeçalhos http estarão disponíveis em breve.

- Nomes de cabeçalho podem conter qualquer caractere alfanumérico e símbolos específicos, conforme definido em [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No entanto, atualmente não temos suporte para o caractere especial “sublinhado” (\_) no nome do Cabeçalho. 

## <a name="need-help"></a>Precisa de ajuda?

Entre em contato conosco em [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) caso você precise de ajuda com essa funcionalidade.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender a respeito da capacidade de reconfigurar os cabeçalhos HTTP, acesse [Criar um Gateway de Aplicativo de dimensionamento automático e redundância de zona que reescreve os cabeçalhos HTTP ](tutorial-http-header-rewrite-powershell.md) ou [Reescrever cabeçalhos HTTP em Gateway de Aplicativo de dimensionamento automático e redundância de zona existente](add-http-header-rewrite-rule-powershell.md)
