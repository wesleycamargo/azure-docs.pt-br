<properties pageTitle="Policies in Azure API Management" metaKeywords="" description="Learn how to create, edit, and configure policies in API Management." metaCanonical="" services="" documentationCenter="API Management" title="Policies in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Políticas do Gerenciamento de API do Azure

No Gerenciamento de API da Azure (visualização), as políticas são uma poderosa funcionalidade que permite ao editor alterar o comportamento da API por meio da configuração. As políticas são um conjunto de instruções executadas em sequência, no momento da solicitação ou da resposta de uma API. Instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor. Muitas políticas estão disponíveis pré-configuradas.

Consulte a [Referência de Política][Referência de Política] para ver uma lista completa das instruções de política e suas configurações.

As políticas são aplicados dentro do proxy que fica entre o consumidor da API e a API gerenciada. O proxy recebe todas as solicitações e normalmente as encaminha inalteradas à API subjacente. No entanto, uma política também pode aplicar mudanças à solicitação de entrada e à resposta de saída.

## Como configurar políticas

As políticas podem ser configuradas globalmente ou no escopo de um [Produto][Produto], [API][API] ou [Operação][Operação]. Para configurar uma política, navegue até o Editor de políticas no Portal do Editor.

![Menu de políticas][Menu de políticas]

O editor de políticas é formado por três seções principais: o escopo da política (superior), a definição da política, onde as políticas são editadas (esquerda) e a lista de instruções (direita):

![Editor de políticas][Editor de políticas]

Para começar a configurar uma política, você precisa antes selecionar o escopo ao qual ela deverá se aplicar. Na captura de tela abaixo, o produto Avaliação Gratuita por 15 Dias está selecionado. Observe que o símbolo de quadrado ao lado do nome da política indica que ela já está aplicada a este nível.

![Escopo][Escopo]

Uma vez que a política já foi aplicada, a configuração é mostrada na exibição de definição.

![Configurar][Configurar]

Primeiro, a política é exibida em formato somente leitura. Para editar a definição, clique na ação Configurar política.

![Editar][Editar]

A definição da política é um documento XML simples que descreve uma sequência de instruções de entrada e de saída. O XML pode ser editado diretamente na janela de definição. Uma lista de instruções é fornecida à direita e as declarações aplicáveis ao escopo atual ficam habilitadas e destacadas, conforme demonstrado pela instrução Limit Call Rate (Taxa limite de chamadas) na captura de tela acima.

Clicar em uma instrução habilitada adicionará o XML adequado ao local onde estiver o cursor na exibição de definição.

Uma lista completa de instruções de políticas e suas configurações está disponível na [Referência de política][Referência de Política].

Por exemplo, para adicionar uma nova instrução para restringir as solicitações de entrada a endereços IP especificados, posicione o cursor dentro do conteúdo do elemento XML "inbound" e clique na instrução correspondente a Restringir IPs de chamada.

![Políticas de restrição][Políticas de restrição]

Isto adicionará um trecho XML ao elemento "inbound" que fornecerá diretrizes de como configurar a instrução.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Para limitar as solicitações de entrada e aceitar somente as provenientes de um endereço IP 1.2.3.4, modifique o XML da seguinte forma:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Salvar][Salvar]

Quando concluir a configuração das instruções da política, clique em Salvar e as alterações serão propagadas para o proxy de Gerenciamento de API imediatamente.

# Noções básicas da configuração de políticas

Uma política é uma série de instruções que são executadas para uma solicitação e uma resposta. A configuração é dividida adequadamente entre de entrada (solicitação) e saída (política), conforme demonstrado na configuração.

    <policies>
        <inbound>
            <!-- statements to be applied to the request go here -->
        </inbound>
        <outboud>
            <!-- statements to be applied to the response go here -->
        <outbound>
    </policies>

Uma vez que as políticas podem ser especificadas em diferentes níveis (global, de produto, API e operação), a configuração oferece uma forma de especifica a ordem na qual as instruções dessa definição são executadas com relação à política pai.

Por exemplo, se você tiver uma política no nível global e uma política configurada para uma API, sempre que a API em questão for utilizada as duas políticas serão aplicadas. O Gerenciamento de API permite uma ordenação determinista de instruções de política combinadas por meio do elemento base.

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

No exemplo de definição de política acima, a instrução entre domínios seria executada antes de quaisquer políticas mais elevadas que, por sua vez, seriam seguidas da política de “localizar e substituir”.

Observação: Uma política global não tem políticas superiores a ela, de modo que o elemento *base* é sempre um *no-op*, ou não tem efeito.

  [Referência de Política]: ../api-management-policy-reference
  [Produto]: ../api-management-howto-add-products
  [API]: ../api-management-howto-add-products/#add-apis
  [Operação]: ../api-management-howto-add-operations
  [Menu de políticas]: ./media/api-management-howto-policies/api-management-policies-menu.png
  [Editor de políticas]: ./media/api-management-howto-policies/api-management-policies-editor.png
  [Escopo]: ./media/api-management-howto-policies/api-management-policies-scope.png
  [Configurar]: ./media/api-management-howto-policies/api-management-policies-configure.png
  [Editar]: ./media/api-management-howto-policies/api-management-policies-edit.png
  [Políticas de restrição]: ./media/api-management-howto-policies/api-management-policies-restrict.png
  [Salvar]: ./media/api-management-howto-policies/api-management-policies-save.png
