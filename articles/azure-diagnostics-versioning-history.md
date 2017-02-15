---
title: "Histórico de versão do Diagnóstico do Azure"
description: "Explicação das alterações nas diferentes versões do diagnóstico do Azure, conforme fornecido com diferentes versões de SDKs do Microsoft Azure."
services: multiple
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 0ae6738e-d3bf-4774-86bf-6e30a9d16c79
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/12/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3b693bcb17fb306d25833ad93416cdec383642bb


---
# <a name="microsoft-azure-diagnostics-version-history"></a>Histórico de versão do Diagnóstico do Microsoft Azure
Ainda não conhece o Diagnóstico do Azure? Confira [Visão geral do Diagnóstico do Azure](azure-diagnostics.md).

Normalmente, cada versão do SDK do Azure é fornecida com uma nova versão do Diagnóstico do Azure. A tabela abaixo descreve as versões do SDK do Azure e Diagnóstico do Azure associadas à versão do SDK.

| Versão do SDK do Azure | Versão do Diagnóstico do Azure | Modelo |
| --- | --- | --- |
| 1.x |1.0 |plug-in |
| 2.0 para 2.4 |1.0 |" |
| 2.5 |1.2 |extensão |
| 2.6 |1,3 |" |
| 2.7 |1.4 |" |
| 2.8 |1.5 |" |

A versão mais recente é 1.5, que é fornecida com o SDK 2.8 do Azure. A versão da extensão do Diagnóstico do Azure que é fornecida com o SDK é usada somente para cenários de emulador local. Qualquer aplicativo implantado usa automaticamente a versão mais recente quando executado no Azure, independentemente de qual versão do SDK é utilizada para compilar o aplicativo. No entanto, a menos que o SDK mais recente do Azure seja instalado, talvez você não tenha todas as ferramentas que o ajudam a utilizar os novos recursos.

Vários recursos e alterações descritas abaixo.

## <a name="azure-sdk-28"></a>SDK 2.8 do Azure
O SDK 2.8 do Azure adicionou a capacidade de enviar dados de diagnósticos para o [Application Insights](application-insights/app-insights-cloudservices.md) , facilitando o diagnóstico de problemas em seu aplicativo, bem como no nível do sistema e da infraestrutura.

## <a name="azure-26-diagnostics-changes"></a>Alterações de diagnóstico do Azure 2.6
Para projetos do Serviço de Nuvem do SDK 2.6 do Azure no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam a versões mais recentes do SDK do Azure).

* O emulador local agora dá suporte a diagnósticos. Isso significa que você pode coletar dados de diagnóstico e garantir que seu aplicativo está criando os rastreamentos corretos enquanto está desenvolvendo e testando no Visual Studio. A cadeia de conexão `UseDevelopmentStorage=true` habilita a coleta de dados de diagnóstico durante a execução do seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletados na conta de armazenamento (armazenamento de desenvolvimento).
* A cadeia de conexão da conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada novamente no arquivo de configuração de serviço (.cscfg). No SDK do Azure 2.5, a conta de armazenamento de diagnóstico foi especificada no arquivo diagnostics.wadcfgx.

Há algumas diferenças perceptíveis entre como a cadeia de conexão funcionava no SDK do Azure 2.4 e anteriores e como funciona no SDK do Azure 2.6 e posteriores.

* No SDK do Azure 2.4 e anteriores, a cadeia de conexão era usada como um tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os logs de diagnóstico.
* No SDK do Azure 2.6 e posteriores, a cadeia de conexão de diagnóstico é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações da conta de armazenamento apropriadas durante a publicação. A cadeia de conexão permite definir contas de armazenamento diferentes para diferentes configurações de serviço que o Visual Studio usará ao publicar. No entanto, como o plug-in de diagnóstico não está mais disponível (após o SDK do Azure 2.5), o arquivo .cscfg sozinho por si só não é capaz de habilitar a extensão de diagnóstico. Você precisa habilitar a extensão separadamente por meio de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída do pacote do Visual Studio também contém o XML de configuração pública para a extensão de diagnóstico para cada função. O Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento presentes na configuração pública. Os arquivos de configuração pública são criados na pasta Extensões e seguem o padrão PaaSDiagnostics.<RoleName>. PubConfig.xml. Todas as implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.
* A cadeia de conexão no arquivo .cscfg também é usada pelo portal do Azure para acessar os dados de diagnóstico para que possam aparecer na guia **monitoramento** . A cadeia de conexão é necessária para configurar o serviço para mostrar dados do monitoramento detalhado no portal.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos para o SDK do Azure 2.6 e posteriores
Ao migrar do SDK do Azure 2.5 para SDK do Azure 2.6 ou posteriores, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo de .wadcfgx, lá ela permanecerá. Para aproveitar a flexibilidade de usar diferentes contas de armazenamento para diferentes configurações de armazenamento, você terá de adicionar manualmente a cadeia de conexão ao seu projeto. Se estiver migrando um projeto do SDK 2.4 ou anterior do Azure para o SDK 2.6 do Azure, as cadeias de conexão de diagnóstico serão preservadas. No entanto, observe as alterações na forma como as cadeias de conexão são tratadas no SDK do Azure 2.6 conforme especificado na seção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* Se nenhuma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio voltará a usar a conta de armazenamento especificada no arquivo .wadcfgx para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* A cadeia de conexão de diagnóstico no arquivo .cscfg tem precedência sobre a conta de armazenamento no arquivo .wadcfgx. Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa e ignora a conta de armazenamento em .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz a caixa de seleção "Atualizar cadeias de conexão do armazenamento de desenvolvimento..." faz a caixa de seleção?
A caixa de seleção **Atualizar cadeias de conexão do armazenamento de desenvolvimento para Diagnóstico e cache com credenciais de conta de armazenamento do Microsoft Azure durante a publicação no Microsoft Azure** oferece uma maneira conveniente para atualizar qualquer cadeia de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que você selecione esta caixa de seleção e a cadeia de conexão de diagnóstico especifique `UseDevelopmentStorage=true`. Quando você publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de conexão de diagnóstico com a conta de armazenamento que você especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de conexão de diagnóstico, então essa conta será usada.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre SDK do Azure 2.4 e anteriores e SDK do Azure 2.5 e posteriores
Se você estiver atualizando seu projeto do SDK do Azure 2.4 para o SDK do Azure 2.5 ou posterior, considere as seguintes diferenças de funcionalidade de diagnóstico.

* **APIs de configuração estão preteridos** – A configuração programática de diagnóstico está disponível no SDK do Azure 2.4 ou anteriores, mas foi preterida no SDK do Azure 2.5 e posteriores. Se sua configuração de diagnóstico está definida atualmente no código, você precisará reconfigurar as configurações do zero no projeto migrado para que o diagnóstico continue a funcionar. O arquivo de configuração de diagnóstico do SDK do Azure 2.4 é diagnostics.wadcfg e para o SDK do Azure 2.5 e posteriores, diagnostics.wadcfgx.
* **Diagnóstico para aplicativos de serviço de nuvem só podem ser configurados no nível de função, não no nível de instância.**
* **Sempre que você implanta seu aplicativo, a configuração de diagnóstico é atualizada** – isso pode causar problemas de paridade, se você altera a configuração de diagnóstico do Gerenciador de Servidores e, em seguida, reimplanta o aplicativo.
* **No SDK do Azure 2.5 e posteriores, despejos de memória são configurados no arquivo de configuração de diagnóstico, não no código** – se você tiver despejos de memória configurados no código, precisará transferir manualmente a configuração de código para o arquivo de configuração, pois os despejos de memória não são transferidos durante a migração para o SDK do Azure 2.6.




<!--HONumber=Nov16_HO3-->


