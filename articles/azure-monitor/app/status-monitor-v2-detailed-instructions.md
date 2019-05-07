---
title: Instruções detalhadas de v2 de Monitor de Status do Azure | Microsoft Docs
description: Instruções detalhadas para a guia de Introdução do Monitor de Status v2. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3aca64c7b0f1ad04967782cb3349da302db557a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145080"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Instruções detalhadas de Status Monitor v2

Este documento detalhes como integrar para a Galeria do PowerShell e o download do módulo ApplicationMonitor. Documentamos parâmetros mais comuns necessários para começar a usar.
Também incluímos instruções manuais que acesso à internet não está disponível.

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Chave de instrumentação

Para começar, você deve ter uma chave de instrumentação. Para obter mais informações, leia [criar um recurso do Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Execute o PowerShell como administrador com uma política de execução com privilégios elevados

**Executar como administrador**: 
- Descrição: PowerShell precisará de permissões de nível de administrador para fazer alterações em seu computador.

**A política de execução**:
- Descrição: Por padrão, executar scripts do PowerShell será desabilitada. É recomendável permitindo scripts RemoteSigned somente no escopo atual.
- Referência: [Sobre as políticas de execução](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) e [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Parâmetros opcionais:
    - `-Force` Isso vai ignorar o prompt de confirmação.

**Erros de exemplo:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

Sua versão atual do PowerShell, executando o comando de auditoria: `$PSVersionTable`.
O comando produz a saída a seguir:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Estas instruções foram gravadas e testadas em um computador Windows 10 com as versões listadas acima.

## <a name="prerequisites-for-powershell-gallery"></a>Pré-requisitos para a Galeria do PowerShell

Essas etapas irá preparar seu servidor para baixar os módulos da Galeria do PowerShell.

> [!NOTE] 
> Suporte para a Galeria do PowerShell está incluído no Windows 10, Windows Server 2016 e 6 do PowerShell. Para versões mais antigas, examine este documento: [Instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Execute o PowerShell como administrador com uma política de execução com privilégios elevados.
2. Provedor de pacote do NuGet 
    - Descrição: Esse provedor é necessário para interagir com repositórios baseados no NuGet, como PowerShellGallery
    - Referência: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para a solicitação.
        - `-Force` Isso vai ignorar o prompt de confirmação. 
    
    Você receberá esse aviso se o NuGet não está configurado para:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Repositórios confiáveis
    - Descrição: Por padrão, PowerShellGallery é um repositório não confiável.
    - Referência: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para a solicitação.

    Você receberá esse aviso se a Galeria do PowerShell não é confiável:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Pode confirme essa alteração e PSRepositories todos os de auditoria executando o cmd: `Get-PSRepository`

4. Versão do PowerShellGet 
    - Descrição: Esse módulo contém as ferramentas usadas para obter outros módulos da Galeria do PowerShell. v1.0.0.1 é fornecido com o Windows 10 e Windows Server. Versão mínima necessária é v1.6.0. Para qual versão está instalada, execute o comando de auditoria: `Get-Command -Module PowerShellGet`
    - Referência: [Instalar o PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para a solicitação.
        - `-Force` Isso irá ignorar o aviso "já está instalado" e instalar a versão mais recente.

    Se você não estiver usando a versão mais recente do PowerShellGet, você receberá esse erro:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie o PowerShell. Não é possível carregar a nova versão na sessão atual. Quaisquer novas sessões do Powershell terá o PowerShellGet mais recente carregado.

## <a name="download--install-via-powershell-gallery"></a>Baixar e instalar por meio da Galeria do PowerShell

Essas etapas baixará o módulo Az.ApplicationMonitor da Galeria do PowerShell.

1. Pré-requisitos para a Galeria do PowerShell são necessários.
2. Execute o PowerShell como administrador com uma política de execução com privilégios elevados.
3. Instalar o módulo Az.ApplicationMonitor
    - Referência: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Parâmetros opcionais:
        - `-Proxy` Especifica um servidor proxy para a solicitação.
        - `-AllowPrerelease` Isso permitirá que a instalação de versões alfa e beta.
        - `-AcceptLicense` Isso vai ignorar o prompt "Aceitar licença"
        - `-Force` Isso irá ignorar o aviso de "Repositório não confiável"

## <a name="download--install-manually-offline-option"></a>Baixar e instalar manualmente (opção offline)

Se por algum motivo, você não pode se conectar o módulo do PowerShell, você pode descarregar manualmente e instalar o módulo Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Baixar manualmente o nupkg mais recente

1. Navegue até: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Selecione a versão mais recente do histórico de versão.
3. Localize "Opções de instalação" e selecione "Download Manual".

### <a name="option-1-install-into-powershell-modules-directory"></a>Opção 1: instalar no diretório de módulos do PowerShell
Instale o módulo do PowerShell baixado manualmente para um diretório do PowerShell para que possa ser detectável pelo sessões do PowerShell.
Para obter mais informações, consulte: [Instalando um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Descompacte o nupkg como zip usando Expand-Archive (v1.0.1.0)

- Descrição: A versão base do Microsoft.PowerShell.Archive (v1.0.1.0) não é possível descompactar arquivos nupkg. Renomeie o arquivo com a extensão ". zip".
- Referência: [Expanda-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Descompacte o nupkg usando Expand-Archive (v1.1.0.0).

- Descrição: Use uma versão atual do Expand-Archive para descompactar nupkgs sem renomear a extensão. 
- Referência: [Expanda-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) e [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Opção 2: descompacte e importar manualmente
Instale o módulo do PowerShell baixado manualmente para um diretório do PowerShell para que possa ser detectável pelo sessões do PowerShell.
Para obter mais informações, consulte: [Instalando um módulo do PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Se instalar em qualquer outro diretório, você deve importar manualmente o módulo usando [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Instalação instalará DLLs por meio de caminhos relativos. Store o conteúdo deste pacote em seu diretório do tempo de execução desejado e confirme que as permissões de acesso permitem ler, mas não gravação.

1. Altere a extensão para. zip"e extrair conteúdo do pacote no diretório de instalação pretendida.
2. Localize o caminho de arquivo para "Az.ApplicationMonitor.psd1".
3. Execute o PowerShell como administrador com uma política de execução com privilégios elevados. 
4. Carregar o módulo por meio do comando: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

Ao monitorar um computador na sua intranet privada, será necessário rotear o tráfego de http por meio de um proxy.

Os comandos do PowerShell para baixar e instalar o Az.ApplicationMonitor da Galeria do PowerShell têm suporte para um `-Proxy` parâmetro.
Analise as instruções acima quando escrevendo seus scripts de instalação.

SDK do Application Insights será necessário enviar telemetria do seu aplicativo à Microsoft. É recomendável definir configurações de proxy para seu aplicativo na Web. config. Consulte [perguntas Frequentes do Application Insights: Passagem de proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) para obter mais informações.


## <a name="enable-monitoring"></a>Habilitar o monitoramento 

Cmd: `Enable-ApplicationInsightsMonitoring`

Examine nossa [referência da API](status-monitor-v2-api-enable-monitoring.md) para obter uma descrição detalhada de como usar este cmdlet. 



## <a name="next-steps"></a>Próximas etapas

 Exiba sua telemetria:

- [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorar o desempenho e o uso
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
- [Analise](../../azure-monitor/app/analytics.md) para obter mais consultas avançadas
- [Crie painéis](../../azure-monitor/app/app-insights-dashboards.md)

 Adicione mais telemetria:

- [Criar testes da web](monitor-web-app-availability.md) para garantir que seu site permanece ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver as exceções no código de página da web e permitir que você insira chamadas de rastreamento.
- [Adicione o SDK do Application Insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir o rastreamento e chamadas de log

Faça mais com v2 de Monitor de Status:

- Use nosso guia para [solucione o problema](status-monitor-v2-troubleshoot.md) v2 do Monitor de Status.
