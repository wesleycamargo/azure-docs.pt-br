---
title: Telemetria de pilha do Azure | Microsoft Docs
description: Descreve como definir as configurações de telemetria do Azure Stack usando o PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 190a80d5807dcc8ad9666d3ba450691bc6453b41
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265610"
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Telemetria de pilha do Azure carrega automaticamente os dados do sistema para a Microsoft por meio da experiência do usuário conectado. As equipes da Microsoft usam os dados que coleta de telemetria do Azure Stack para aprimorar as experiências do cliente. Esses dados também são usados para análise de desempenho, integridade, qualidade e segurança.

Para um operador do Azure Stack, a telemetria pode fornecer informações valiosas sobre implantações empresariais e lhe dá uma voz que ajuda a versões futuras de forma do Azure Stack.

> [!NOTE]
> Você também pode configurar o Azure Stack para encaminhar informações de uso do Azure para cobrança. Isso é necessário para clientes com vários nós do Azure Stack que optam por pagamento-como-o uso cobrança. Relatório de uso é controlado independentemente da telemetria e não é necessário para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento do Azure Stack. Para esses cenários, o relatório de uso pode ser desativado [usando o script de registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria de pilha do Azure baseia-se o componente de telemetria e experiência de usuário conectado do Windows Server 2016, que usa o [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging tecnologia para coletar e armazenar dados e eventos. Componentes de pilha do Azure usam a mesma tecnologia para publicar eventos e dados coletados usando o log de eventos do sistema de operacional público e o rastreamento de APIs. Exemplos desses componentes do Azure Stack incluem esses provedores: Recurso, recurso de armazenamento, recursos de monitoramento de rede e recursos de atualização. O componente de experiência do usuário conectado e telemetria criptografa dados usando SSL e usa a anexação de certificado para transmitir dados via HTTPS para o serviço de gerenciamento de dados da Microsoft.

> [!IMPORTANT]
> Para habilitar o fluxo de dados de telemetria, a porta 443 (HTTPS) deve estar aberta na sua rede. O componente de experiência do usuário conectado e telemetria conecta-se para o serviço de gerenciamento de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente de experiência do usuário conectado e telemetria também se conecta ao https://settings-win.data.microsoft.com para baixar informações de configuração.

## <a name="privacy-considerations"></a>Considerações sobre privacidade

O serviço ETW encaminha os dados de telemetria para o armazenamento de nuvem protegida. O princípio de privilégios mínimos orienta o acesso aos dados de telemetria. Somente funcionários da Microsoft com uma necessidade comercial válido recebem acesso a dados de telemetria. Microsoft não compartilha dados pessoais de clientes com terceiros, exceto a critério do cliente ou para fins limitados descritos de [declaração de privacidade do Microsoft](https://privacy.microsoft.com/PrivacyStatement). Relatórios de negócios que são compartilhados com parceiros e os OEMs incluem dados agregados, mantido em anonimato. Decisões de compartilhamento de dados são feitos por uma equipe interna de Microsoft incluindo stakeholders de gerenciamento de privacidade, legal e dados.

A Microsoft acredita em e minimização de informações de práticas. Nos esforçamos para coletar somente as informações que são necessários e armazená-lo para apenas pelo tempo necessário para fornecer um serviço ou para análise. Muitas das informações sobre como o sistema do Azure Stack e os serviços do Azure estão funcionando é excluída em até seis meses. Resumidos ou dados agregados serão mantidos por um período maior.

Entendemos que a privacidade e segurança de informações do cliente é importante.  Microsoft adota uma abordagem abrangente e cuidadosa para privacidade do cliente e a proteção de dados do cliente no Azure Stack. Os administradores de TI têm controles para personalizar as configurações de privacidade e os recursos a qualquer momento. Nosso compromisso com a transparência e a relação de confiança é claro:

- Estamos abertos com os clientes sobre os tipos de dados que podemos obter.
- Colocamos os clientes corporativos no controle — eles podem personalizar suas próprias configurações de privacidade.
- Nós colocamos a privacidade e a segurança dos clientes em primeiro lugar.
- Estamos transparentes sobre como os dados de telemetria são usados.
- Usamos os dados de telemetria para aprimorar as experiências do cliente.

Microsoft não pretende coletar dados confidenciais, como números de cartão de crédito, nomes de usuário e senhas, endereços de email ou informações confidenciais semelhantes. Se determinarmos que informações confidenciais foram recebidas inadvertidamente, podemos excluí-lo.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria

Telemetria desempenha um papel importante em ajudar a identificar e corrigir problemas de confiabilidade críticos em implantações do cliente e as configurações rapidamente. Informações de dados de telemetria podem ajudar a identificar problemas com os serviços ou configurações de hardware. A capacidade da Microsoft para obter esses dados de clientes e gerar melhorias para o ecossistema, eleva o nível para a qualidade de soluções integradas do Azure Stack.

Telemetria também ajuda a Microsoft a entender melhor como os clientes implantam componentes, usar os recursos e usar os serviços para alcançar seus objetivos comerciais. Essas informações ajudam a priorizar investimentos em engenharia nas áreas que podem afetar diretamente experiências de clientes e cargas de trabalho.

Alguns exemplos incluem o uso do cliente de contêineres, armazenamento e as configurações da rede que estão associadas a funções do Azure Stack. Nós também usamos as ideias para aprimoramentos de unidade e a inteligência para soluções de monitoramento e gerenciamento do Azure Stack. Esses aprimoramentos tornam mais fácil para os clientes diagnosticar problemas e economizar dinheiro, tornando menos suporte chama para a Microsoft.

## <a name="manage-telemetry-collection"></a>Gerenciar coleta de telemetria

Não temos mais recomendada desativando a telemetria em sua organização. No entanto, em alguns cenários isso pode ser necessário.

Nesses cenários, você pode configurar o nível de telemetria enviado à Microsoft por meio de configurações do registro antes de implantar o Azure Stack ou usando os pontos de extremidade de telemetria depois de implantar o Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Coleta de dados e níveis de telemetria

Antes de alterar as configurações de telemetria, você deve compreender os níveis de telemetria e quais dados são coletados em cada nível.

As configurações de telemetria são agrupadas em quatro níveis (0-3) que são cumulativas e categorizados como da seguinte maneira:

**0 (segurança)**</br>
Somente dados de segurança. Informações necessárias para proteger o sistema operacional. Isso inclui dados sobre a experiência do usuário conectado e telemetria de configurações do componente e o Windows Defender. Sem telemetria específica para o Azure Stack é emitida nesse nível.

**1 (básico)**</br>
Dados de segurança e integridade básica e a qualidade de dados. Informações básicas do dispositivo, incluindo: dados relacionados à qualidade, compatibilidade de aplicativos, dados de uso do aplicativo e os dados do **segurança** nível. Definindo seu nível de telemetria para a telemetria do Azure Stack permite básico. Os dados coletados com esse nível incluem:

- *Informações básicas do dispositivo* que fornece uma compreensão sobre os tipos e as configurações das instâncias de Windows Server 2016 native e virtuais no ecossistema. Isso inclui:

  - Atributos de computador, como o OEM e o modelo.
  - Atributos, como o número de adaptadores de rede e sua velocidade de rede.
  - Atributos de processador e memória, como o número de núcleos e a quantidade de memória instalada.
  - Atributos de armazenamento, como o número de unidades, o tipo de unidade e o tamanho da unidade.

- *Funcionalidade de telemetria*, incluindo o percentual de eventos carregados, eventos ignorados e os dados de última hora do carregamento.
- *Informações relacionadas à qualidade* que ajudam a Microsoft a desenvolver um entendimento básico de como está o desempenho do Azure Stack. Por exemplo, a contagem de alertas críticos em uma configuração de hardware específica.
- *Dados de compatibilidade* que ajuda a fornecer um entendimento sobre quais provedores de recursos são instalados em um sistema e uma máquina virtual. Isso identifica possíveis problemas de compatibilidade.

**2 (Avançado)**</br>
Informações adicionais, incluindo: como o sistema operacional e os serviços do Azure Stack são usados, como executam esses serviços, confiabilidade avançada dados e dados do **segurança** e **básica** níveis.

> [!NOTE]
> Essa é a configuração de telemetria padrão.

**3 (completo)**</br>
Todos os dados necessários para identificar e ajudar a corrigir problemas, além de dados a partir o **segurança**, **básica**, e **avançado** níveis.

> [!IMPORTANT]
> Esses níveis de telemetria se aplicam somente aos componentes do Microsoft Azure Stack. Componentes de software não Microsoft e serviços que estão em execução no Host de ciclo de vida do Hardware de parceiros de hardware do Azure Stack podem se comunicar com seus serviços de nuvem fora desses níveis de telemetria. Você deve trabalhar com seu provedor de soluções de hardware do Azure Stack para entender sua política de telemetria e como você pode aceitar ou recusar.

Desativando a telemetria do Windows e o Azure Stack também desabilita a telemetria do SQL. Para obter mais informações sobre as implicações das configurações de telemetria do Windows Server, consulte o [white paper de telemetria do Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: definir o nível de telemetria no registro do Windows

Você pode usar o Editor de registro do Windows para definir o nível de telemetria manualmente no computador host físico antes de implantar o Azure Stack. Se já existir uma política de gerenciamento, como a diretiva de grupo, ela substitui a configuração do registro.

Antes de implantar o Azure Stack no host do kit de desenvolvimento, inicialize no CloudBuilder.vhdx e execute o seguinte script em uma janela elevada do PowerShell:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK e com vários nós: habilitar ou desabilitar a telemetria depois da implantação

Para habilitar ou desabilitar a telemetria após a implantação, você precisa ter acesso para o ponto de extremidade com privilégios (PEP) que é exposta nas VMs ERCS.

1. Para habilitar: `Set-Telemetry -Enable`
2. Para desabilitar o: `Set-Telemetry -Disable`

Detalhes do parâmetro:
> . PARÂMETRO Enable - ativar o carregamento de dados de telemetria</br>
> . PARÂMETRO Disable - desativar o carregamento de dados de telemetria  

**Script para habilitar a telemetria:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Script para desabilitar a telemetria:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Próximas etapas

[Registre-se a pilha do Azure com o Azure](azure-stack-registration.md)