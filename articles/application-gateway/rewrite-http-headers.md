---
title: Reescreva cabeçalhos HTTP com Gateway de aplicativo do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de regravação de cabeçalhos HTTP no Gateway de aplicativo do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 89df3a981ba3710e848f834c303772e94e10b139
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947171"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescreva cabeçalhos HTTP com o Gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cabeçalhos HTTP permitem que um cliente e servidor passar informações adicionais com uma solicitação ou resposta. Reescrevendo esses cabeçalhos, você pode realizar tarefas importantes, como adição de campos de cabeçalho relacionados à segurança, como a HSTS / X-XSS-Protection, removendo campos de cabeçalho de resposta que podem revelar informações confidenciais e remover informações de porta Cabeçalhos X-Forwarded-For.

O Gateway de aplicativo permite que você adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP durante a solicitação e pacotes de resposta se mover entre o cliente e os pools de back-end. E ele permite que você adicione condições para garantir que os cabeçalhos especificados são reescritos somente quando determinadas condições forem atendidas.

O Gateway de aplicativo também dá suporte a vários [variáveis de servidor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) que ajudam a você armazena informações adicionais sobre solicitações e respostas. Isso torna mais fácil para você criar regras de reescrita poderosas.

> [!NOTE]
>
> O suporte à regravação de cabeçalho HTTP só está disponível para o [Standard_V2 e SKU WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Cabeçalhos de regravação](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos com suporte

Você pode reescrever todos os cabeçalhos de solicitações e respostas, exceto para os cabeçalhos de Host, a Conexão e a atualização. Você também pode usar o gateway de aplicativo para criar cabeçalhos personalizados e adicioná-los para as solicitações e respostas que estão sendo roteadas por ele.

## <a name="rewrite-conditions"></a>Condições de regravação

Você pode usar condições de reescrita para avaliar o conteúdo do HTTP (S) de solicitações e respostas e executar uma reescrita de cabeçalho somente quando uma ou mais condições são atendidas. O gateway de aplicativo usa esses tipos de variáveis para avaliar o conteúdo de HTTP (S) de solicitações e respostas:

- Cabeçalhos HTTP na solicitação.
- Cabeçalhos HTTP na resposta.
- Variáveis de servidor de Gateway de aplicativo.

Você pode usar uma condição para avaliar se uma variável especificada estiver presente, se uma variável especificada corresponde a um valor específico, ou se uma variável especificada corresponde a um padrão específico. Você usa o [biblioteca Perl compatível Regular expressões (PCRE)](https://www.pcre.org/) para configurar as condições de correspondência de padrão de expressão regular. Para saber mais sobre a sintaxe de expressão regular, consulte o [página principal de expressões regulares do Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Ações de reconfiguração

Você pode usar ações de reescrita para especificar os cabeçalhos de solicitação e resposta que querem regravar e o novo valor para os cabeçalhos. Você pode criar um novo cabeçalho, modifique o valor de um cabeçalho existente ou excluir um cabeçalho existente. O valor de um novo cabeçalho ou um cabeçalho existente pode ser definido para esses tipos de valores:

- Texto.
- Cabeçalho da solicitação. Para especificar um cabeçalho de solicitação, você precisará usar a sintaxe {http_req_*headerName*}.
- Cabeçalho de resposta. Para especificar um cabeçalho de resposta, você precisará usar a sintaxe {http_resp_*headerName*}.
- Variável de servidor. Para especificar uma variável de servidor, você precisará usar a sintaxe {var_*serverVariable*}.
- Uma combinação de texto, um cabeçalho de solicitação, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

O Gateway de aplicativo usa variáveis de servidor para armazenar informações úteis sobre o servidor, a conexão com o cliente e a solicitação atual em que a conexão. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador da web. Variáveis de servidor alteram dinamicamente, por exemplo, quando uma nova página for carregada ou quando um formulário é enviado. Você pode usar essas variáveis para avaliar as condições de regravação e reescreva cabeçalhos.

O gateway de aplicativo dá suporte a essas variáveis de servidor:

| Nome da variável | DESCRIÇÃO                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de solicitação X-Forwarded-For cliente com o `client_ip` variável (veja a explicação posteriormente nesta tabela) anexado a ele no formato IP1, IP2, lt;ip3 e assim por diante. Se o campo ' X-Forwarded-For não está no cabeçalho da solicitação do cliente, o `add_x_forwarded_for_proxy` variável é igual ao `$client_ip` variável. Essa variável é particularmente útil quando você desejar reescrever o cabeçalho X-Forwarded-For definido por Gateway de aplicativo para que o cabeçalho contém somente o endereço IP sem as informações de porta. |
| ciphers_supported          | Uma lista das codificações com suporte no cliente.          |
| ciphers_used               | A cadeia de caracteres de criptografia usado para uma conexão SSL estabelecida. |
| client_ip                  | O endereço IP do cliente do qual o gateway de aplicativo recebeu a solicitação. Se não houver um proxy reverso antes do gateway de aplicativo e o cliente de origem, *client_ip* retornará o endereço IP do proxy reverso. |
| client_port                | A porta do cliente.                                                  |
| client_tcp_rtt             | Informações sobre o cliente de conexão TCP. Disponível em sistemas que dão suporte a opção de soquete TCP_INFO. |
| client_user                | Quando a autenticação HTTP é usada, o nome de usuário fornecido para autenticação. |
| host                       | Essa ordem de precedência: o nome do host da linha de solicitação, o nome do host do campo de cabeçalho de solicitação do Host ou o nome de servidor corresponde a uma solicitação. |
| cookie_*nome*              | O *nome* cookie.                                            |
| http_method                | o método usado para fazer a solicitação de URL. Por exemplo, GET ou POST. |
| http_status                | O status da sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de solicitação. Normalmente, HTTP/1.0, 1.1/HTTP ou HTTP/2.0. |
| query_string               | A lista de pares de variável/valor que segue o "?" na URL solicitada. |
| received_bytes             | O comprimento da solicitação (inclusive a linha de solicitação, o cabeçalho e o corpo da solicitação). |
| request_query              | Os argumentos na linha da solicitação.                                |
| request_scheme             | O esquema da solicitação: http ou https.                            |
| request_uri                | A solicitação original completo URI (com argumentos).                   |
| sent_bytes                 | O número de bytes enviados para um cliente.                             |
| server_port                | A porta do servidor que aceita uma solicitação.                 |
| ssl_connection_protocol    | O protocolo de uma conexão SSL estabelecida.        |
| ssl_enabled                | "Ativado" se a conexão está funcionando no modo SSL. Caso contrário, uma cadeia de caracteres vazia. |

## <a name="rewrite-configuration"></a>Configuração de regravação

Para configurar a reconfiguração do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reconfiguração do cabeçalho HTTP:

   - **Reescreva a ação**: Usado para especificar a solicitação e campos de cabeçalho de solicitação que você deseja reescrever e o novo valor para os cabeçalhos. Você pode associar um ou mais condições com uma ação de regravação de reconfiguração.

   - **Reescreva a condição**: Uma configuração opcional. Condições de regravação avaliar o conteúdo das solicitações de HTTP (S) e as respostas. A ação de regravação ocorrerá se a resposta ou solicitação de HTTP (S) corresponde à condição de regravação.

     Se você associar mais de uma condição com uma ação, a ação ocorre somente quando todas as condições são atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **Regra de reescrita de**: Contém várias ação de regravação / reescrever combinações de condição.

   - **Sequência de regra**: Ajuda a determinar a ordem na qual executar regras de regravação. Essa configuração é útil quando você tiver várias regras de reescrita em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo será executado primeira. Se você atribuir a mesma sequência de regra a duas regras de reescrita, a ordem de execução é não determinística.

   - **Reescreva o conjunto**: Contém várias regras de reconfiguração que serão associadas uma regra de roteamento de solicitação.

2. Anexe o conjunto de reconfiguração (*rewriteRuleSet*) para uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de regravação de cabeçalho está associada um ouvinte de origem e é uma reformulação de cabeçalho global. Quando você usa uma regra de roteamento com base no caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específico de um site.

Você pode criar vários conjuntos de reconfiguração do cabeçalho HTTP e aplicar cada reescrita definida para vários ouvintes. Mas você pode aplicar somente um reescrever definido como um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Aqui estão alguns cenários comuns para usar a regravação de cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta do cabeçalho X-Forwarded-For

O Gateway de aplicativo insere um cabeçalho X-Forwarded-For em todas as solicitações antes de encaminhar as solicitações para o back-end. Esse cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver cenários em que os servidores de back-end precisam apenas os cabeçalhos para conter endereços IP. Você pode usar a regravação de cabeçalho para remover as informações de porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isso é definir o cabeçalho para a variável de servidor add_x_forwarded_for_proxy:

![Remover porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificar uma URL de redirecionamento

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquele especificado pelo aplicativo de back-end. Por exemplo, você talvez queira fazer isso quando um serviço de aplicativo é hospedado atrás de um gateway de aplicativo e requer que o cliente fazer um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Porque o serviço de aplicativo é um serviço multilocatário, ele usa o cabeçalho de host na solicitação para rotear a solicitação para o ponto de extremidade correto. Serviços de aplicativos tem um nome de domínio padrão de *. azurewebsites.net (digamos, contoso.azurewebsites.net) que é diferente do nome de domínio de gateway de aplicativo (digamos, contoso.com). Como a solicitação original do cliente tem um nome de domínio de gateway de aplicativo (contoso.com) como o nome do host, o gateway de aplicativo altera o nome do host para contoso.azurewebsites.net. Ele faz essa alteração para que o serviço de aplicativo pode rotear a solicitação para o ponto de extremidade correto.

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho location da resposta como a solicitação, que ele recebe do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar por meio do gateway de aplicativo (contoso.com/path2). Ignorar o gateway de aplicativo não é desejável.

Você pode resolver esse problema definindo o nome do host no cabeçalho de local para o nome de domínio do gateway de aplicativo.

Aqui estão as etapas para substituir o nome do host:

1. Crie uma regra de reescrita com uma condição que avalia se o cabeçalho location na resposta contém azurewebsites.net. Insira o padrão de `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Execute uma ação para reescrever o cabeçalho de localização para que ele tenha o nome do host do gateway de aplicativo. Faça isso inserindo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho.

![Modificar cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar os cabeçalhos HTTP de segurança para evitar vulnerabilidades

Você pode corrigir várias vulnerabilidades de segurança com a implementação de cabeçalhos necessários na resposta do aplicativo. Esses cabeçalhos de segurança incluem a política de segurança de conteúdo, segurança de transporte estrito e X-XSS-Protection. Você pode usar o Gateway de aplicativo para definir esses cabeçalhos de todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Excluir cabeçalhos indesejados

Você talvez queira remover os cabeçalhos que revelam informações confidenciais de uma resposta HTTP. Por exemplo, você talvez queira remover informações como o nome do servidor de back-end, o sistema operacional ou os detalhes da biblioteca. Você pode usar o gateway de aplicativo para remover esses cabeçalhos:

![Excluir cabeçalho](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificar a presença de um cabeçalho

Você pode avaliar um cabeçalho de resposta ou solicitação HTTP a presença de uma variável de cabeçalho ou no servidor. Essa avaliação é útil quando você deseja executar uma reescrita de cabeçalho somente quando um determinado cabeçalho estiver presente.

![Verificando a presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Atualmente não há suporte a regravação de cabeçalhos de Host, atualização e Conexão.

- Nomes de cabeçalho podem conter quaisquer caracteres alfanuméricos e símbolos específicos, conforme definido em [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Não há suporte no momento, o sublinhado (\_) caractere especial nos nomes de cabeçalho.

## <a name="need-help"></a>Precisa de ajuda?

Entre em contato conosco em [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) caso você precise de ajuda com esse recurso.

## <a name="next-steps"></a>Próximas etapas

Para saber como reescreva cabeçalhos HTTP, consulte:

- [Reescreva cabeçalhos HTTP usando o portal do Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Reescreva cabeçalhos HTTP usando o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
