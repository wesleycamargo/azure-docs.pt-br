<properties 
	pageTitle="Referência de políticas do Gerenciamento de API do Azure" 
	description="Saiba mais sobre as políticas disponíveis para configurar o Gerenciamento de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Referência de políticas do Gerenciamento de API do Azure

Este tópico fornece uma referência das seguintes políticas do Gerenciamento de API (visualização). Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API][Políticas de Gerenciamento de API].

-   [Políticas de restrição de acesso][Políticas de restrição de acesso]
    -   [Cota de uso][Cota de uso] - Permite que você aplique uma cota renovável ou permanente de volume da chamada e/ou largura de banda.
    -   [Limite de taxa][Limite de taxa] - Evita picos de uso da API limitando chamadas e/ou a taxa de consumo de largura de banda.
    -   [Restrição de IP do autor da chamada][Restrição de IP do autor da chamada] - Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.
-   [Políticas de transformação de conteúdo][Políticas de transformação de conteúdo]
    -   [Definir cabeçalho HTTP][Definir cabeçalho HTTP] - Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.
    -   [Converter XML para JSON][Converter XML para JSON] - Converte o corpo da solicitação ou da resposta de XML para a forma "JSON" ou "XML faithful" de JSON.
    -   [Substituir cadeia no corpo][Substituir cadeia no corpo] - Encontra uma subcadeia de uma solicitação ou resposta e a substitui por outra subcadeia.
    -   [Definir parâmetro de cadeia de consulta][Definir parâmetro de cadeia de consulta] - Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.
-   [Políticas de cache][Políticas de cache]
    -   [Armazenar em cache][Armazenar em cache] - Armazena a resposta em cache de acordo com a configuração de cache especificada.
    -   [Obter do cache][Obter do cache] - Executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando uma estiver disponível.
-   [Outras políticas][Outras políticas]
    -   [Reescrever URI][Reescrever URI] - Converte o URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.
    -   [Mascarar URLS no conteúdo][Mascarar URLS no conteúdo] - Reescreve (mascara) os links no corpo da resposta e no cabeçalho de local, de modo que eles apontem para o link equivalente por meio do proxy.
    -   [Permitir chamadas entre domínios][Permitir chamadas entre domínios] - Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.
    -   [JSONP][JSONP] - Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores.
    -   [CORS][CORS] - Adicionar suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.

## <a name="access-restriction-policies"> </a> Políticas de restrição de acesso

-   [Cota de uso][Cota de uso] - Permite que você aplique uma cota renovável ou permanente de volume da chamada e/ou largura de banda.
-   [Limite de taxa][Limite de taxa] - Evita picos de uso da API limitando chamadas e/ou a taxa de consumo de largura de banda.
-   [Restrição de IP do autor da chamada][Restrição de IP do autor da chamada] - Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.

### <a name="usage-quota"> </a> Cota de uso

**Descrição:**
Aplica uma cota renovável ou permanente de volume da chamada e/ou largura de banda.

**Declaração de política:**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**Exemplo:**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Usada na seção de entrada no Escopo do produto.

**Onde pode ser aplicada:**
Quando o uso das operações e/ou APIs deve ser limitado para um produto.

**Por que deve ser aplicada, por que não:**
Para definir limites de uso para um produto com base no tempo e largura de banda. Quando uma API atinge o limite de cota definido, as chamadas subsequentes são rejeitadas com uma mensagem de erro. Normalmente, as cotas são definidas como o número de mensagens de solicitação que são permitidas no decorrer de um intervalo de tempo e limite de largura de banda pré-determinados.

| Atributo                 | Descrição                                                                                                                                                     |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| quota calls="number"     | O número máximo de chamadas de assinatura que são permitidas no decorrer do período de renovação (por exemplo, 10000)                                         |
| bandwidth="kilobytes"    | O número máximo de quilobytes que poderão ser consumidos pelo produto, API ou operação no decorrer do período de renovação especificado (por exemplo, 40000). |
| renewal-period="seconds" | O período de tempo, em segundos, durante o qual uma cota se aplica (por exemplo, 3.600).                                                                      |
| api name="name"          | O nome da chamada à API, à qual a cota se aplica.                                                                                                             |
| calls="number"           | O número máximo de chamadas para uma API ou operação, que poderão ser feitas no período de renovação especificado (por exemplo, 5000).                        |
| operation name="name"    | O nome da operação à qual a cota se aplica.                                                                                                                   |

### <a name="rate-limit"> </a> Limite de taxa

**Descrição:**
Impede picos de uso da API limitando a taxa de solicitações que chegam a uma API e, se for o caso, a uma operação de API específica. Quando a política é acionada, o consumidor recebe um código de status de resposta `429 Too Many Requests`.

**Declaração de política:**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**Exemplo:**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Use na seção de entrada no Escopo do produto.

| Elemento/Atributo        | Descrição                                                                 |
|--------------------------|---------------------------------------------------------------------------|
| calls="number"           | número de chamadas permitidas por período de renovação                    |
| renewal-period="seconds" | período de tempo após o qual a cota de chamadas da taxa limite é renovada |
| api name="name"          | Nome da API à qual a taxa limite se aplica                                |
| operation name="name"    | Nome da operação à qual a taxa limite se aplica                           |

### <a name="caller-ip-restriction"> </a> Restrição de IP do autor da chamada

**Descrição:**
Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.

**Declaração de política:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Exemplo:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Onde pode ser aplicada:**
Use na seção de entrada de qualquer escopo.

**Quando deve ser aplicada:**
Use esta política quando é necessário controlar de forma específica quem pode acessar seu serviço.

**Por que deve ser aplicada, por que não:**
Esta política precisa ser aplicada somente quando é necessário um controle restrito do acesso (por exemplo, para serviços com requisitos elevados de segurança), com base no host ou em um intervalo de hosts.

| Atributo                                  | Descrição                                                                           |
|-------------------------------------------|-------------------------------------------------------------------------------------|
| ip-filter action="allow \| forbid"        | Especifica se chamadas para os endereços especificados devem ou não ser permitidas. |
| address="address"                         | Um ou mais endereços IP aos quais o acesso será permitido ou negado.                |
| address-range from="address" to="address" | Um intervalo de endereços IP aos quais o acesso será permitido ou negado.           |

## <a name="content-transformation-policies"> </a> Políticas de transformação de conteúdo

-   [Definir cabeçalho HTTP][Definir cabeçalho HTTP] - Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.
-   [Converter XML para JSON][Converter XML para JSON] - Converte o corpo da solicitação ou da resposta de XML para a forma "JSON" ou "XML faithful" de JSON.
-   [Substituir cadeia no corpo][Substituir cadeia no corpo] - Encontra uma subcadeia de uma solicitação ou resposta e a substitui por outra subcadeia.
-   [Definir parâmetro de cadeia de consulta][Definir parâmetro de cadeia de consulta] - Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.

### <a name="set-http-header"> </a> Definir cabeçalho HTTP

**Descrição:**
Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.

Insere uma lista de cabeçalhos HTTP em uma mensagem HTTP. Quando colocada em um pipeline de entrada, esta política define os cabeçalhos HTTP para a solicitação que está sendo passada para o serviço alvo. Quando colocada em um pipeline de saída, esta política define os cabeçalhos HTTP para a resposta que está sendo passada para o cliente do proxy.

**Declaração de política:**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**Exemplo:**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**Onde pode ser aplicada:**
Use na seção de entrada ou de saída com qualquer escopo, exceto por *Editor*.

**Quando deve ser aplicada:**
Use para especificar os parâmetros de operação e/ou valores de retorno de uma transação HTTP.

**Por que deve ser aplicada, por que não:**
A maioria das solicitações HTTP e muitas respostas requerem que os cabeçalhos especifiquem parâmetros de entrada/resposta. Sendo assim, é provável que esta política se aplique a quase todos os seus serviços.

| Atributo                                   | Descrição                                                                                                                                                                                                                                                                                                                       |
|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| reconcile-action="override\|skip\|append"  | Especifica a ação a ser adotada quando um cabeçalho já foi especificado. Observação: quando definido como “override”, listar diversas entradas com o mesmo nome faz com que o cabeçalho seja definido de acordo com todas as entradas (que serão listadas várias vezes); somente valores listados serão definidos no resultado. |
| header name="header name"                  | Especifica o nome do cabeçalho a ser definido. Obrigatório.                                                                                                                                                                                                                                                                     |
| exists-action="override \| skip \| append" | Especifica a ação a ser adotada quando o cabeçalho já foi especificado.                                                                                                                                                                                                                                                         |
| value="value"                              | Especifica o valor do cabeçalho a ser definido.                                                                                                                                                                                                                                                                                 |

### <a name="convert-xml-to-json"> </a> Converter XML para JSON

**Descrição:**
Converte o corpo da solicitação ou da resposta de XML para JSON.

**Declaração de política:**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**Exemplo:**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Use na seção de entrada ou de saída nos escopos da API ou da operação.

**Por que deve ser aplicada, por que não:**
Para modernizar APIs baseadas em serviços Web de back-end somente XML.

| Atributo                               | Descrição                                                                                                                                    |
|----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| kind="javascript-friendly \| direct    | JSON convertido tem um formato amigável para desenvolvedores de JavaScript \| JSON convertido reflete a estrutura original de documentos XML |
| apply= always \| content-type-xml      | Converter sempre \| Converter somente se o cabeçalho `Content-Type` indicar a presença de XML                                                |
| consider-accept-header="true \| false" | Aplicar conversão com base no valor do cabeçalho `Accept` \| Sempre converter                                                                |

### <a name="replace-string-in-body"> </a> Substituir cadeia do corpo

**Descrição:**
Encontra uma subcadeia na solicitação ou na resposta e a substitui por outra subcadeia.

**Declaração de política:**

    <find-and-replace from="what to replace" to="replacement" />

**Exemplo:**

    <find-and-replace from="notebook" to="laptop" />

**Onde pode ser aplicada:**
Use na seção de entrada ou de saída com qualquer escopo.

| Atributo               | Descrição                                             |
|------------------------|-------------------------------------------------------|
| from="what to replace" | A cadeia a ser pesquisada.                            |
| to="replacement"       | A cadeia a ser colocada no local da encontrada acima. |

### <a name="set-query-string-parameter"> </a> Definir parâmetro de cadeia de consulta

**Descrição:**
Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.

**Declaração de política:**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**Exemplo:**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Pode ser usada na seção de entrada em qualquer escopo.

**Porque deve ser aplicada, por que não:**
Use para transmitir parâmetros de consulta esperados pelo serviço de back-end que são opcionais ou nunca estão presentes na solicitação.

| Elemento/Atributo        | Descrição                                                          |
|--------------------------|--------------------------------------------------------------------|
| name="name"              | uma cadeia que nomeia o parâmetro                                  |
| exists-action="override" | substitui o valor do parâmetro, se estiver presente na solicitação |
| exists-action="skip"     | Não faz nada se o parâmetro estiver presente na solicitação        |
| exists-action="append"   | anexa o valor ao parâmetro existente                               |
| exists-action="delete"   | remove o parâmetro da solicitação\*                                |
| value="value"            | define o valor do parâmetro no elemento de circunscrição           |

## <a name="caching-policies"> </a> Políticas de cache

-   [Armazenar em cache][Armazenar em cache] - Armazena a resposta em cache de acordo com a configuração de cache especificada.
-   [Obter do cache][Obter do cache] - Executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando uma estiver disponível.

### <a name="store-to-cache"> </a> Armazenar em cache

**Descrição:**
Armazena as respostas em cache de acordo com a configuração de cache especificada. Deve ter uma política de [Obter do cache][Obter do cache] correspondente.

**Declaração de política:**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**Exemplo:**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Pode estar presente na seção de saída em um ou nos dois escopos de API e operação.

**Quando deve ser aplicada:**
Deve ser aplicada em casos nos quais o conteúdo da resposta permanece estático por um período de tempo.

**Por que deve ser aplicada, por que não:**
O cache das respostas reduz os requisitos de largura de banda e processamento exigidos do servidor Web de back-end e reduz a latência percebida pelos consumidores da API.

| Elemento/Atributo        | Descrição                                                                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| segundos                 | Vida útil das entradas armazenadas em cache (em segundos, padrão = 3.600)                                                                                                               |
| cache-on \| do-not-cache | As respostas são armazenadas em cache \| As respostas não são armazenadas em cache e os cabeçalhos relacionados ao cache são definidos para proibir o armazenamento em cache downstream |

### <a name="get-from-cache"> </a> Obter do cache

**Descrição:**
Executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando uma estiver disponível. Responde adequadamente a solicitações de validação de cache de consumidores da API. É necessário ter uma política correspondente de [Armazenar em cache][Armazenar em cache].

**Declaração de política:**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**Exemplo:**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Pode estar presente na seção de entrada em um ou nos dois escopos de API e operação.

**Quando deve ser aplicada:**
Deve ser aplicada em casos nos quais o conteúdo da resposta permanece estático por um período de tempo.

**Por que deve ser aplicada, por que não:**
O cache das respostas reduz os requisitos de largura de banda e processamento exigidos do servidor Web de back-end e reduz a latência percebida pelos consumidores da API.

| Elemento/Atributo                                 | Descrição                                                                                                                                                                                                                                               |
|---------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true \| false"                  | Defina como \*true\* para iniciar o cache de respostas por chave de desenvolvedor; \*false\* é a definição padrão                                                                                                                                       |
| vary-by-developer-groups="true \| false"           | Defina como \*true\* para iniciar o cache de respostas por função do usuário; \*false\* é a definição padrão                                                                                                                                            |
| downstream-caching-type="none \| private \| public" | \*none\* - o cache downstream não é permitido; definição padrão \| \*private\* - o cache downstream privado é permitido \| \*public\* - o cache downstream privado e compartilhado é permitido.                                                           |
| vary-by-header: "Accept"                          | Inicie o cache de respostas de acordo com o valor do cabeçalho `Accept`                                                                                                                                                                                 |
| vary-by-header: Accept-Charset"                   | Inicie o cache de respostas de acordo com o valor do cabeçalho `Accept-Charset`                                                                                                                                                                         |
| vary-by-header: “nome do cabeçalho"               | Inicie o cache de respostas de acordo com o valor do cabeçalho especificado, como `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match`                                                       |
| vary-by-query-parameter: “nome do parâmetro"      | Inicie o cache de respostas de acordo com o valor dos parâmetros de consulta especificados. Insira somente um ou múltiplos parâmetros. Use ponto e vírgula como um separador. Se nenhum for especificado, todos os parâmetros de consulta serão usados. |

## <a name="other-policies"> </a> Outras políticas

-   [Reescrever URI][Reescrever URI] - Converte o URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.
-   [Mascarar URLS no conteúdo][Mascarar URLS no conteúdo] - Reescreve (mascara) os links no corpo da resposta e no cabeçalho de local, de modo que eles apontem para o link equivalente por meio do proxy.
-   [Permitir chamadas entre domínios][Permitir chamadas entre domínios] - Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.
-   [JSONP][JSONP] - Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores.
-   [CORS][CORS] - Adicionar suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.

### <a name="rewrite-uri"> </a> Reescrever URI

**Descrição:**
Converta o URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.

**URL pública:** <http://api.example.com/storenumber/ordernumber>

**URL de solicitação:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

Não adicione o parâmetro de caminho do modelo extra ao URL reescrito. Você pode adicionar somente parâmetros de cadeia de consulta.

**Declaração de política:**

    <rewrite-uri template="uri template" />

**Exemplo:**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Onde pode ser aplicada:**
Na seção de entrada do escopo de Operação somente.

**Quando deve ser aplicada:**
Use quando um URL simplificado para humanos e/ou navegadores precisar ser transformado no URL esperado pelo serviço Web.

**Por que deve ser aplicada, por que não:**
Precisa ser usada somente para expor um URL alternativo. URLs limpas, URLs RESTful, URLs simplificadas para usuários ou para SEO são URLs puramente estruturais que não contêm uma cadeia de consulta, contendo somente o caminho do recurso (após o esquema e autoridade). Frequentemente, isso é feito para fins estéticos, de usabilidade ou de otimização do mecanismo de pesquisa (SEO).

| Atributo                | Descrição                                                              |
|-------------------------|------------------------------------------------------------------------|
| template="uri template" | URL real do serviço Web com quaisquer parâmetros de cadeia de consulta |

### <a name="mask-urls-in-content"> </a> Mascarar URLS no conteúdo

**Descrição:**
Reescreve (mascara) os links no corpo da resposta e no cabeçalho de local, de modo que eles apontem para o link equivalente por meio do proxy.

**Declaração de política:**

    <redirect-body-urls />

**Exemplo:**

    <redirect-body-urls />

**Onde pode ser aplicada:**
Aplique nos escopos de *API* e *Operação*. Pode ser aplicada na seção de entrada ou de saída.

### <a name="allow-cross-domain-calls"> </a> Permitir chamadas entre domínios

**Descrição:**
Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.

**Declaração de política:**

    <cross-domain />

**Exemplo:**

    <cross-domain />

**Onde pode ser aplicada:**
Use na seção de entrada no escopo dos *Editores*.

### <a name="jsonp"> </a> JSONP

**Descrição:**
Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores. O JSONP é um método usado em programas JavaScript para solicitar dados de um servidor em um domínio diferente. O JSONP ignora a limitação aplicada pela maioria dos navegadores da Web quando o acesso às páginas da Web precisa ser do mesmo domínio.

**Declaração de política:**

    <jsonp callback-parameter-name="callback function name" />

**Exemplo:**

    <jsonp callback-parameter-name="cb" />

Se você chamar o método sem o parâmetro de retorno de chamada `?cb=XXX`, será retornado o JSON simples (sem um wrapper de chamada de função).

Se você adicionar o parâmetro de retorno de chamada `?cb=XXX`, será retornado um resultado JSONP, dispondo os resultados JSON originais em torno da função de retorno de chamada como `XXX('<json result goes here>')`;

**Onde pode ser aplicada:**
Use na seção de saída somente.

**Quando deve ser aplicada:**
Para solicitar dados de um servidor em um domínio diferente.

| Atributo                | Descrição                                                                                                                   |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| callback-parameter-name | A chamada da função JavaScript entre domínios, prefixada com o nome do domínio onde a função reside totalmente qualificado. |

### <a name="cors"> </a> CORS

**Descrição:**
Adiciona suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.

O CORS permite que um navegador e um servidor interajam e determina e solicitações entre origens específicas devem ou não ser aceitas (por exemplo, chamadas XMLHttpRequests feitas por meio de JavaScript em uma página da Web para outros domínios). Isso permite maior flexibilidade do que permitir somente solicitações com a mesma origem, mas é mais seguro do que permitir todas as solicitações entre origens.

**Declaração de política:**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**Exemplo:**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**Onde pode ser aplicada:**
Use na seção de entrada e somente nos escopos de *API* e *Operação*.

| Atributo             | Descrição                                                                                                                                          |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| <origin>\*</origin>  | Permita qualquer um, OU uma lista com um ou mais URIs específicos                                                                                  |
| <origin>URI</origin> | O URI deve incluir um esquema, um host e uma porta. Se a porta for omitida, a porta 80 será presumida para http e a 443 será presumida para https. |

  [Políticas de Gerenciamento de API]: ../api-management-howto-policies
  [Políticas de restrição de acesso]: #access-restriction-policies
  [Cota de uso]: #usage-quota
  [Limite de taxa]: #rate-limit
  [Restrição de IP do autor da chamada]: #caller-ip-restriction
  [Políticas de transformação de conteúdo]: #content-transformation-policies
  [Definir cabeçalho HTTP]: #set-http-header
  [Converter XML para JSON]: #convert-xml-to-json
  [Substituir cadeia no corpo]: #replace-string-in-body
  [Definir parâmetro de cadeia de consulta]: #set-query-string-parameter
  [Políticas de cache]: #caching-policies
  [Armazenar em cache]: #store-to-cache
  [Obter do cache]: #get-from-cache
  [Outras políticas]: #other-policies
  [Reescrever URI]: #rewrite-uri
  [Mascarar URLS no conteúdo]: #mask-urls-in-content
  [Permitir chamadas entre domínios]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors

<!--HONumber=46--> 
