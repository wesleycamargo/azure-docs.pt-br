---
title: Referência de configurações de aplicativo para Azure Functions
description: Documentação de referência para as configurações de aplicativo ou variáveis de ambiente do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 573e6ea857ef17a6160f7e03f52f9c64f903530a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546619"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicativo para Azure Functions

As configurações de aplicativo em um aplicativo de funções contém opções de configuração global que afetam todas as funções desse aplicativo de funções. Quando você executa localmente, essas configurações são acessadas como [variáveis de ambiente](functions-run-local.md#local-settings-file). Este artigo lista as configurações de aplicativo disponíveis nos aplicativos de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Há outras opções de configuração global no arquivo [host.json](functions-host-json.md) e no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Chave de instrumentação do Application Insights se você estiver usando o Application Insights. Consulte [Monitorar Azure Functions](functions-monitoring.md).

|Chave|Valor de exemplo|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **Monitor** do portal. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> Para desempenho e experiência, é recomendável usar APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitoramento, em vez de AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. O padrão é `false`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDisableHomepage|verdadeiro|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida na resposta à URL `<functionappname>.azurewebsites.net`.

![Página de aterrissagem do aplicativo de funções](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de versão durante a compilação do código .NET; `false` significa usar o modo de depuração. O padrão é `true`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|verdadeiro|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de recursos beta a habilitar. Os recursos beta habilitados por esses sinalizadores não estão prontos para produção, mas podem ser habilitados para uso experimental antes de serem lançados.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

O caminho para o diretório raiz onde o arquivo *host.json* e as pastas de funções estão localizados. Em um aplicativo de funções, o padrão é `%HOME%\site\wwwroot`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o provedor a ser usado para armazenar chaves. Atualmente, os repositórios com suporte são o armazenamento de blobs ("Blob") e o sistema de arquivos local ("Arquivos"). O padrão é blob na versão 2 e sistema de arquivos na versão 1.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsSecretStorageType|Arquivos|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução do Azure Functions usa essa cadeia de conexão da conta armazenamento para todas as funções, exceto para as funções disparadas por HTTP. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite substituir o padrão se necessário.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

Determina se a edição no portal do Azure está habilitado. Os valores válidos são "readwrite" e "readonly".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

A versão do tempo de execução do Functions para usar nesse aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~2"). Quando novas versões da mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo a uma versão específica, use o número de versão completo (por exemplo, "2.0.12345"). O padrão é "~ 2". Um valor de pinos `~1` seu aplicativo para a versão 1.x do tempo de execução.

|Chave|Valor de exemplo|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functionsworkerruntime"></a>FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO

O tempo de execução do trabalho de linguagem deve ser carregado no aplicativo de funções.  Isso irá corresponder ao idioma que está sendo usado em seu aplicativo (por exemplo, "dotnet"). Para funções em vários idiomas, você precisará publicá-las em vários aplicativos, cada um com um valor de tempo de execução de trabalho correspondente.  Os valores válidos são `dotnet` (C#/F#), `node` (JavaScript/TypeScript) `java` (Java), e `python` (Python).

|Chave|Valor de exemplo|
|---|------------|
|FUNÇÕES\_TRABALHADOR\_TEMPO DE EXECUÇÃO|dotnet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Apenas para planos de consumo. Cadeia de conexão para a conta de armazenamento na qual o código do aplicativo de funções e a configuração são armazenados. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

Apenas para planos de consumo. O caminho do arquivo para o código do aplicativo de funções e a configuração. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome do aplicativo de funções. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

O número máximo de instâncias que o aplicativo de funções pode alcançar. O padrão é sem limites.

> [!NOTE]
> Essa configuração é uma versão prévia do recurso - e só confiável se definido como um valor < = 5

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

O padrão é "8.11.1".

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>SITE\_EXECUTAR\_DO\_PACOTE

Permite que seu aplicativo de funções execute de um arquivo de pacote montado.

|Chave|Valor de exemplo|
|---|------------|
|SITE\_EXECUTAR\_DO\_PACOTE|1|

Os valores válidos são `1` ou uma URL que resolve para o local de um arquivo de pacote de implantação. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação em zip com essa configuração, o pacote é automaticamente carregado para esse local. Na versão prévia, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, veja [Executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="azurefunctionproxydisablelocalcall"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por padrão, os proxies de funções utilizarão um atalho para enviar chamadas à API de proxies diretamente para funções no mesmo aplicativo de funções, em vez de criar uma nova solicitação HTTP. Essa configuração permite que você desabilite esse comportamento.

|Chave|Valor|DESCRIÇÃO|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|verdadeiro|Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local não serão mais enviadas diretamente à função e, em vez disso, serão direcionadas de volta para o front-end HTTP para o Aplicativo de funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|falso|Esse é o valor padrão. Chamadas com uma URL de back-end apontando para uma função no Aplicativo de Funções local serão encaminhadas diretamente para essa Função|


## <a name="azurefunctionproxybackendurldecodeslashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Essa configuração controla se %2F é decodificado como barras nos parâmetros de rota quando inseridos na URL de back-end. 

|Chave|Valor|DESCRIÇÃO|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|verdadeiro|Os parâmetros de rota com barras codificadas os terão decodificados. `example.com/api%2ftest` se tornará `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|falso|Esse é o comportamento padrão. Todos os parâmetros de rota serão passados inalterados|

### <a name="example"></a>Exemplo

Aqui, está um exemplo de proxy.json em um aplicativo de funções no myfunction.com da URL

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Decodificação URL|Entrada|Saída|
|-|-|-|
|verdadeiro|myfunction.com/test%2fapi|example.com/test/api
|falso|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Próximos passos

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Consulte as configurações globais no arquivo host.json](functions-host-json.md)

[Consulte outras configurações de aplicativo para aplicativos do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
