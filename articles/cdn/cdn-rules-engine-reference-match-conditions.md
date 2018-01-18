---
title: "Condições de correspondência para o mecanismo de regras da CDN do Azure | Microsoft Docs"
description: "Documentação de referência para condições de correspondência do mecanismo de regras da Rede de Distribuição de Conteúdo do Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Condições de correspondência para o mecanismo de regras da CDN do Azure
Este artigo lista descrições detalhadas das condições de correspondência disponíveis para o [mecanismo de regras](cdn-rules-engine.md) da CDN (Rede de Distribuição de Conteúdo) do Azure.

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para os quais um conjunto de recursos será executado.

Por exemplo, você pode usar uma condição de correspondência para:
- Filtrar solicitações para conteúdo em um local específico.
- Filtrar solicitações geradas de um endereço IP ou país específico.
- Filtrar solicitações por informações de cabeçalho.

## <a name="always-match-condition"></a>Condição de correspondência Sempre

A condição de correspondência Sempre aplica um conjunto de recursos padrão a todas as solicitações.

NOME | Finalidade
-----|--------
[Sempre](#always) | Aplica um conjunto de recursos padrão para todas as solicitações.

## <a name="device-match-condition"></a>Condição de correspondência ispositivo

A condição de correspondência Dispositivo identifica solicitações feitas de um dispositivo móvel baseadas em suas propriedades.  

NOME | Finalidade
-----|--------
[Dispositivo](#device) | Identifica solicitações feitas de um dispositivo móvel baseadas em suas propriedades.

## <a name="location-match-conditions"></a>Condições de correspondência Localização

As condições de correspondência Localização identificam solicitações baseadas na localização do solicitante.

NOME | Finalidade
-----|--------
[Número AS](#as-number) | Identifica solicitações originadas de uma rede específica.
[País](#country) | Identifica solicitações originadas de países especificados.

## <a name="origin-match-conditions"></a>Condições de correspondência Origem

As condições de correspondência Origem identificam solicitações que apontem para o armazenamento da Rede de Distribuição de Conteúdo ou para um servidor de origem do cliente.

NOME | Finalidade
-----|--------
[Origem CDN](#cdn-origin) | Identifica solicitações para conteúdo armazenado no armazenamento da Rede de Distribuição de Conteúdo.
[Origem do Cliente](#customer-origin) | Identifica solicitações de conteúdo armazenado em um servidor de origem do cliente específico.

## <a name="request-match-conditions"></a>Condições de correspondência Solicitação

As condições de correspondência Solicitação identificam solicitações baseadas em suas propriedades.

NOME | Finalidade
-----|--------
[Endereço IP do cliente](#client-ip-address) | Identifica solicitações originadas de um endereço IP específico.
[Parâmetro de Cookie](#cookie-parameter) | Verifica o valor especificado nos cookies associados a cada solicitação.
[Regex de Parâmetro de Cookie](#cookie-parameter-regex) | Verifica a expressão regular especificada nos cookies associados a cada solicitação.
[Cname de Borda](#edge-cname) | Identifica solicitações que apontam para um CNAME de borda específico.
[Domínio de Referência](#referring-domain) | Identifica solicitações referenciadas dos nomes do host especificados.
[Literal de Cabeçalho de Solicitação](#request-header-literal) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor especificado.
[Regex do Cabeçalho da Solicitação](#request-header-regex) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponde à expressão regular especificada.
[Curinga de Cabeçalho de Solicitação](#request-header-wildcard) | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponda ao padrão especificado.
[Método de Solicitação](#request-method) | Identifica solicitações pelo método HTTP.
[Esquema de Solicitação](#request-scheme) | Identifica solicitações pelo protocolo HTTP.

## <a name="url-match-conditions"></a>Condições de correspondência URL

As condições de correspondência URL identificam solicitações baseadas em suas URLs.

NOME | Finalidade
-----|--------
Diretório do Caminho da URL | Identifica solicitações pelo caminho relativo.
Extensão do Caminho da URL | Identifica solicitações pela extensão de nome do arquivo.
Nome do Arquivo do Caminho da URL | Identifica solicitações pelo nome do arquivo.
Literal do Caminho da URL | Compara a caminho relativo da solicitação com o valor especificado.
Regex do Caminho da URL | Compara o caminho relativo da solicitação com a expressão regular especificada.
Curinga do Caminho da URL | Compara o caminho relativo da solicitação com o padrão especificado.
Literal da Consulta da URL | Compara a sequência da cadeia de caracteres de consulta da solicitação com o valor especificado.
Parâmetro da Consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a um padrão especificado.
Regex da consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a uma expressão regular especificada.
Curinga da consulta da URL | Compara o valor especificado com a cadeia de caracteres de consulta da solicitação.


## <a name="reference-for-rules-engine-match-conditions"></a>Referência para condições de correspondência do mecanismo de regras

---
### <a name="always"></a>Sempre

A condição de correspondência Sempre aplica um conjunto de recursos padrão a todas as solicitações.

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Número AS 
A rede de número AS é definida por seu número de sistema autônomo (ASN). Uma opção é fornecida para indicar se essa condição será atendida quando a rede de um cliente "Corresponder" ou "Não corresponder" ao ASN especificado.

Informações de chave:
- Especifique vários ASNs delimitando cada um deles com um único espaço. Por exemplo, 64514 64515 corresponde a solicitações que chegam de 64514 ou 64515.
- Determinadas solicitações podem não retornar um ASN válido. Um ponto de interrogação (?) corresponderá a solicitações para as quais um ASN válido não pôde ser determinado.
- Você deve especificar o ASN inteiro para a rede desejada. Não haverá correspondência de valores parciais.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Origem CDN
A condição de correspondência de origem CDN é atendida quando as condições a seguir são atendidas:
- O conteúdo do armazenamento da Rede de Distribuição de Conteúdo foi solicitado.
- A URI de solicitação usa o ponto de acesso ao conteúdo (por exemplo, /000001) que está definido nessa condição de correspondência.
  - URL da Rede de Distribuição de Conteúdo: O URI da solicitação deve conter o ponto de acesso ao conteúdo selecionado.
  - URL CNAME de borda: A configuração CNAME de borda correspondente deve apontar para o ponto de acesso ao conteúdo selecionado.
  
Informações de chave:
 - O ponto de acesso ao conteúdo identifica o serviço que deve ser o conteúdo solicitado.
 - Não use uma instrução AND IF para combinar determinadas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Origem CDN com uma condição de correspondência de Origem do Cliente cria um padrão de correspondência que não poderia ser satisfeito. Por esse motivo, as duas condições de correspondência de Origem de CDN não podem ser combinadas por meio de uma instrução AND IF.

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Endereço IP do Cliente
Uma opção é fornecida para indicar se a condição Endereço IP do Cliente será atendida quando um endereço IP de cliente "Corresponder" ou "Não corresponder" aos endereços IP especificados.

Informações de chave:
- Certifique-se de usar a notação CIDR.
- Especifique vários endereços IP e/ou blocos de endereços IP delimitando cada um com um único espaço.
  - **Exemplo de IPv4**: 1.2.3.4 10.20.30.40 corresponde a qualquer solicitação que chegue de 1.2.3.4 ou 10.20.30.40.
  - **Exemplo de IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 corresponde a qualquer solicitação que chegue de 1:2:3:4:5:6:7:8 ou 10:20:30:40:50:60:70:80.
- A sintaxe para um bloco de endereços IP é o endereço IP básico seguido por uma barra invertida e o tamanho do prefixo.
  - **Exemplo de IPv4**: 5.5.5.64/26 corresponde a qualquer solicitação que chegue de 5.5.5.64 ou 5.5.5.127.
  - **Exemplo de IPv6**: 1:2:3:/48 corresponde a qualquer solicitação que chegue de 1:2:3:0:0:0:0:0 por meio de 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Parâmetro de Cookie
A opção **Corresponde**/**Não corresponde** determina as condições sob as quais a condição de correspondência Parâmetro de Cookie será satisfeita.
- **Corresponde**: requer uma solicitação para conter o cookie especificado com um valor que corresponde a pelo menos um dos valores definidos nessa condição de correspondência.
- **Não corresponde**: requer que a solicitação satisfaça qualquer um dos critérios a seguir:
  - Ela não contém o cookie especificado.
  - Ela contém o cookie especificado, mas seu valor não corresponde a nenhum dos valores que são definidos nessa condição de correspondência.
  
Informações de chave:
- Nome do cookie: 
  - Não há suporte para caracteres especiais, incluindo um asterisco, quando você estiver especificando um nome de cookie. Isso significa que apenas as correspondências de nome exato de cookie são qualificadas para comparação.
  - Somente um nome de cookie único pode ser especificado por instância dessa condição de correspondência.
  - Comparações de nome de cookie diferenciam maiusculas de minúsculas.
- Valor do cookie: 
  - Especifique vários valores de cookie delimitando cada um deles com um único espaço.
  - Um valor de cookie pode aproveitar caracteres especiais. 
  - Se um caractere curinga não tiver sido especificado, apenas uma correspondência exata vai satisfazer essa condição de correspondência. Por exemplo, especificar "Valor" corresponderá a "Valor", mas não "Valor1" ou "Valor2".
  - A opção **Ignorar maiúsculas** determina se uma comparação que diferencia maiúsculas de minúsculas será feita no valor do cookie da solicitação.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Regex de Parâmetro de Cookie
A condição de correspondência de Regex de Parâmetro de Cookie define o valor e o nome de um cookie. Você pode usar expressões regulares para definir o valor do cookie desejado. 

A opção **Corresponde**/**Não corresponde** determina as condições sob as quais essa condição de correspondência será satisfeita.
- **Corresponde**: Requer que uma solicitação contenha o cookie especificado com um valor que corresponde à expressão regular especificada.
- **Não corresponde**: requer que a solicitação satisfaça qualquer um dos critérios a seguir:
  - Ela não contém o cookie especificado.
  - Ela contém o cookie especificado, mas seu valor não corresponde à expressão regular especificada.
  
Informações de chave:
- Nome do cookie: 
  - Não há suporte para expressões regulares nem caracteres especiais, incluindo um asterisco, quando você estiver especificando um nome de cookie. Isso significa que apenas as correspondências de nome exato de cookie são qualificadas para comparação.
  - Somente um nome de cookie único pode ser especificado por instância dessa condição de correspondência.
  - Comparações de nome de cookie diferenciam maiusculas de minúsculas.
- Valor do cookie: 
  - Um valor de cookie pode aproveitar expressões regulares.
  - A opção **Ignorar maiúsculas** determina se uma comparação que diferencia maiúsculas de minúsculas será feita no valor do cookie da solicitação.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>País
Você pode especificar um país por meio de seu código de país. Uma opção é fornecida para indicar se essa condição será atendida quando o país do qual uma solicitação é originada "Corresponder" ou "Não corresponder" aos valores especificados.

Informações de chave:
- Especifique vários códigos de país delimitando cada um deles com um único espaço.
- Não há suporte para curingas quando você está especificando um código de país.
- Os códigos de país "EU" e "PA" não abrangem todos os endereços IP nessas regiões.
- Determinadas solicitações podem não retornar um código de país válido. Um ponto de interrogação (?) corresponderá a solicitações para as quais um código de pais válido não pôde ser determinado.
- Os códigos de país diferenciam maiúsculas de minúsculas.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origem do Cliente

Informações de chave: 
- A condição de correspondência Origem do Cliente será satisfeita independentemente se o conteúdo for solicitado por meio de uma URL de Rede de Distribuição de Conteúdo ou uma URL CNAME de borda que aponta para a origem de cliente selecionada.
- Uma configuração de origem de cliente que é referenciada por uma regra não pode ser excluída da página Origem do Cliente. Antes de tentar excluir uma configuração de origem do cliente, certifique-se de que as configurações a seguir fazem referência a ela:
  - Uma condição de correspondência Origem do Cliente
  - Uma configuração CNAME de borda
- Não use uma instrução AND IF para combinar determinadas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Origem do Cliente com uma condição de correspondência de Origem de CDN cria um padrão de correspondência que não poderia ser satisfeito. Por esse motivo, as duas condições de correspondência de Origem do Cliente não podem ser combinadas por meio de uma instrução AND IF.

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Dispositivo

A condição de correspondência Dispositivo identifica solicitações feitas de um dispositivo móvel baseadas em suas propriedades. A detecção de dispositivos móveis é alcançada por meio de [WURFL](http://wurfl.sourceforge.net/). A tabela a seguir lista recursos WURFL e suas variáveis para o mecanismo de regras da Rede de Distribuição de Conteúdo.
<br>
> [!NOTE] 
> As variáveis a seguir têm suporte nos recursos **Modificar Cabeçalho de Solicitação do Cliente** e **Modificar Cabeçalho de Resposta do Cliente**.

Recurso | Variável | DESCRIÇÃO | Valores de exemplo
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma cadeia de caracteres que indica o nome da marca do dispositivo. | Samsung
SO do dispositivo | %{wurfl_cap_device_os} | Uma cadeia de caracteres que indica o sistema operacional instalado no dispositivo. | IOS
Versão do SO do dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia de caracteres que indica o número de versão do sistema operacional instalado no dispositivo. | 1.0.1
Orientação dupla | %{wurfl_cap_dual_orientation} | Um booliano que indica se o dispositivo dá suporte a orientação dupla. | verdadeiro
DTD preferencial HTML | %{wurfl_cap_html_preferred_dtd} | Uma cadeia de caracteres que indica a DTD (definição de tipo de documento) do dispositivo móvel para conteúdo HTML. | Nenhum<br/>xhtml_basic<br/>html5
Inlining de Imagem | %{wurfl_cap_image_inlining} | Um booliano que indica se o dispositivo dá suporte a imagens codificadas de Base64. | falso
É Android | %{wurfl_vcap_is_android} | Um booliano que indica se o dispositivo usa o sistema operacional Android. | verdadeiro
É IOS | %{wurfl_vcap_is_ios} | Um booliano que indica se o dispositivo usa iOS. | falso
É Smart TV | %{wurfl_cap_is_smarttv} | Um booliano que indica se o dispositivo é uma Smart TV. | falso
É Smartphone | %{wurfl_vcap_is_smartphone} | Um booliano que indica se o dispositivo é um smartphone. | verdadeiro
É Tablet | %{wurfl_cap_is_tablet} | Um booliano que indica se o dispositivo é um tablet. Esta é uma descrição independente de sistema operacional. | verdadeiro
É dispositivo sem fio | %{wurfl_cap_is_wireless_device} | Um booliano que indica se o dispositivo é considerado um dispositivo sem fio. | verdadeiro
Nome de marketing | %{wurfl_cap_marketing_name} | Uma cadeia de caracteres que indica o nome de marketing do dispositivo. | BlackBerry 8100 Pearl
Navegador de dispositivo móvel | %{wurfl_cap_mobile_browser} | Uma cadeia de caracteres que indica o navegador que é usado para solicitar o conteúdo do dispositivo. | Chrome
Versão do navegador móvel | %{wurfl_cap_mobile_browser_version} | Uma cadeia de caracteres que indica a versão do navegador que é usado para solicitar conteúdo do dispositivo. | 31
Nome do modelo | %{wurfl_cap_model_name} | Uma cadeia de caracteres que indica o nome do modelo do dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Um booliano que indica se o dispositivo dá suporte a reprodução de áudio e vídeo enquanto ele ainda está sendo baixado. | verdadeiro
Data do lançamento | %{wurfl_cap_release_date} | Uma cadeia de caracteres que indica o ano e o mês em que o dispositivo foi adicionado ao banco de dados WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura de resolução | %{wurfl_cap_resolution_height} | Um inteiro que indica a altura do dispositivo em pixels. | 768
Largura de resolução | %{wurfl_cap_resolution_width} | Um inteiro que indica a largura do dispositivo em pixels. | 1024

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Cname de Borda
Informações de chave: 
- A lista CNAMEs de borda disponíveis é limitada aos que foram configuradas na página dos CNAMEs de borda que correspondente à plataforma em que o mecanismo de regras de HTTP está sendo configurado.
- Antes de tentar excluir uma configuração de CNAME de borda, certifique-se de que uma condição de correspondência de Cname de borda não faz referência a ela. Configurações de CNAME de borda que foram definidas em uma regra não podem ser excluídas da página de CNAMEs de borda. 
- Não use uma instrução AND IF para combinar determinadas condições de correspondência. Por exemplo, combinar uma condição de correspondência de Cname de Borda com uma condição de correspondência de Origem do Cliente cria um padrão de correspondência que não poderia ser satisfeito. Por esse motivo, as duas condições de correspondência de Cname de Borda não podem ser combinadas por meio de uma instrução AND IF.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Domínio de Referência
O nome do host associado com o referenciador por meio do qual o conteúdo foi solicitado determina se a condição do domínio de referência é atendida. Uma opção é fornecida para indicar se essa condição será atendida quando o nome do host referido "Corresponder" ou "Não corresponder" aos valores especificados.

Informações de chave:
- Especifique vários nomes de host delimitando cada um deles com um único espaço.
- Essa condição de correspondência dá suporte a caracteres especiais.
- Se o valor especificado não contiver um asterisco, ele deverá ser uma correspondência exata para o nome de host do referenciador. Por exemplo, especificar "mydomain.com" não corresponderia a "www.mydomain.com."
- A opção **Ignorar maiúsculas** determina se uma comparação que diferencia maiúsculas de minúsculas será executada.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Literal de Cabeçalho de Solicitação
A opção **Corresponde**/**Não corresponde** determina as condições sob as quais essa condição de correspondência será satisfeita.
- **Correspondências**: requer que a solicitação contenha o cabeçalho especificado. O valor deve corresponder ao que está definido nessa condição de correspondência.
- **Não corresponde**: requer que a solicitação satisfaça qualquer um dos critérios a seguir:
  - Ela não contém o cabeçalho especificado.
  - Ela contém o cabeçalho especificado, mas seu valor não corresponde ao que está definido nessa condição de correspondência.
  
Informações de chave:
- Comparações de nome de cabeçalho sempre diferenciam maiúsculas de minúsculas. A opção **Ignorar maiúsculas** determina a diferenciação de maiúsculas e minúsculas de comparações de valores de cabeçalho.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Regex do Cabeçalho da Solicitação
A opção **Corresponde**/**Não corresponde** determina as condições sob as quais a condição de correspondência Regex do Cabeçalho da Solicitação será satisfeita.
- **Correspondências**: requer que a solicitação contenha o cabeçalho especificado. O valor deve corresponder ao padrão que é definido na expressão regular especificada.
- **Não corresponde**: requer que a solicitação satisfaça qualquer um dos critérios a seguir:
  - Ela não contém o cabeçalho especificado.
  - Ela contém o cabeçalho especificado, mas seu valor não corresponde à expressão regular especificada.

Informações de chave:
- Nome do cabeçalho: 
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Espaços no nome do cabeçalho devem ser substituídos por "%20". 
- Valor do cabeçalho: 
  - Um valor de cabeçalho pode aproveitar expressões regulares.
  - A opção **Ignorar maiúsculas** determina a diferenciação de maiúsculas e minúsculas de comparações de valores de cabeçalho.
  - Apenas as correspondências de valor exato do cabeçalho para pelo menos um dos padrões especificados satisfarão essa condição.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno 

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Curinga de Cabeçalho de Solicitação
A opção **Corresponde**/**Não corresponde** determina as condições sob as quais a condição de correspondência Curinga do Cabeçalho da Solicitação será satisfeita.
- **Correspondências**: requer que a solicitação contenha o cabeçalho especificado. Seu valor deve corresponder a pelo menos um dos valores que são definidos nessa condição de correspondência.
- **Não corresponde**: requer que a solicitação satisfaça qualquer um dos critérios a seguir:
  - Ela não contém o cabeçalho especificado.
  - Ela contém o cabeçalho especificado, mas seu valor não corresponde a nenhum dos valores especificados.
  
Informações de chave:
- Nome do cabeçalho: 
  - Comparações de nome de cabeçalho diferenciam maiúsculas de minúsculas.
  - Espaços no nome do cabeçalho devem ser substituídos por "%20". Você também pode usar esse valor para especificar os espaços em um valor de cabeçalho.
- Valor do cabeçalho: 
  - Um valor de cabeçalho pode aproveitar caracteres especiais.
  - A opção **Ignorar maiúsculas** determina a diferenciação de maiúsculas e minúsculas de comparações de valores de cabeçalho.
  - Apenas as correspondências de valor exato do cabeçalho para pelo menos um dos padrões especificados satisfarão essa condição.
  - Especifique vários valores delimitando cada um deles com um único espaço.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Método de Solicitação
Somente os recursos que são solicitados por meio do método de solicitação selecionado atenderão à condição Método de Solicitação. Os métodos de solicitação disponíveis são:
- GET
- HEAD 
- POST 
- OPÇÕES 
- PUT 
- EXCLUIR 
- RASTREAMENTO 
- CONECTAR 

Informações de chave:
- Por padrão, o método de solicitação GET pode gerar o conteúdo armazenado em cache na rede. Todos os outros métodos de solicitação são solicitados por proxy através da rede.
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Esquema de Solicitação
Somente os recursos que são solicitados por meio do protocolo selecionado atenderão à condição Esquema de Solicitação. Os protocolos disponíveis são HTTP e HTTPS.

Informações de chave:
- Devido à maneira como as configurações de cache são acompanhadas, essa condição de correspondência é incompatível com os recursos a seguir:
  - Concluir o Preenchimento do Cache
  - Idade Máxima Interna Padrão
  - Forçar Idade Máxima Interna
  - Ignorar Ausência de Cache de Origem
  - Máximo de Estado Obsoleto Interno

[Voltar ao início](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md)
* [Referência do mecanismo de regras](cdn-rules-engine-reference.md)
* [Expressões condicionais do mecanismo de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Recursos do mecanismo de regras](cdn-rules-engine-reference-features.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)

