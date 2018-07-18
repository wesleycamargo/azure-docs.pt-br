---
title: Telemetria de pilha do Azure | Microsoft Docs
description: Descreve como definir as configurações de telemetria de pilha do Azure usando o PowerShell.
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248190"
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Telemetria de pilha do Azure carrega automaticamente os dados do sistema para a Microsoft por meio da experiência do usuário conectado. Equipes da Microsoft usam os dados de telemetria de pilha do Azure reúne para aprimorar as experiências de clientes. Esses dados também são usados para análise de desempenho, integridade, qualidade e segurança.

Para um operador de pilha do Azure, telemetria pode fornecer informações valiosas sobre implantações corporativas e oferece uma voz que ajuda a versões futuras de forma da pilha do Azure.

> [!NOTE]
> Você também pode configurar a pilha do Azure para encaminhar as informações de uso para o Azure para cobrança. Isso é necessário para clientes de vários nós do Azure pilha que escolha pago como você-uso cobrança. Relatório de uso é controlado independentemente de telemetria e não é necessário para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento de pilha do Azure. Nessas situações, o relatório de uso pode ser desativado [usando o script de registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria de pilha do Azure baseia-se o componente de telemetria e experiência de usuário conectado do Windows Server 2016, que usa o [rastreamento de eventos para Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging tecnologia para coletar e armazenar dados e eventos. Componentes da pilha do Azure usam a mesma tecnologia para publicar eventos e dados coletados usando o log de eventos do sistema operacional público e APIs de rastreamento. Exemplos desses componentes da pilha do Azure incluem estes provedores: recursos de rede, os recursos de armazenamento, recursos de monitoramento e recursos de atualização. O componente de experiência do usuário conectado e telemetria criptografa dados usando SSL e usa a fixação de certificado para transmitir dados via HTTPS para o serviço de gerenciamento de dados da Microsoft.

> [!IMPORTANT]
> Para habilitar o fluxo de dados de telemetria, a porta 443 (HTTPS) deve estar aberta em sua rede. O componente de experiência do usuário conectado e telemetria conecta-se para o serviço de gerenciamento de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente de experiência do usuário conectado e telemetria também se conecta ao https://settings-win.data.microsoft.com para baixar informações de configuração.

## <a name="privacy-considerations"></a>Considerações sobre privacidade

O serviço ETW encaminha os dados de telemetria para armazenamento em nuvem protegida. O princípio de menos privilégios guias de acesso de dados de telemetria. Somente funcionários da Microsoft com uma necessidade comercial válido recebem acesso a dados de telemetria. Microsoft não compartilha dados pessoais de cliente com terceiros, exceto a critério do cliente ou para fins limitado descrito no [declaração de privacidade do Microsoft](https://privacy.microsoft.com/PrivacyStatement). Relatórios de negócios que são compartilhados com parceiros e OEMs incluem dados agregados, anônimos. Decisões de compartilhamento de dados são feitos por uma equipe interna do Microsoft incluindo participantes do gerenciamento de dados, legal e privacidade.

A Microsoft acredita em e minimização de informações de práticas. Tentamos reunir informações que é necessário e armazenam-o para apenas pelo tempo necessário para fornecer um serviço ou para análise. Muitas das informações sobre como o sistema de pilha do Azure e os serviços do Azure estão funcionando é excluída em seis meses. Resumidos ou dados agregados serão mantidos por um período maior.

Compreendemos que a privacidade e segurança de informações do cliente é importante.  Microsoft usa uma abordagem ponderada e abrangente para privacidade do cliente e a proteção de dados do cliente na pilha do Azure. Os administradores de TI têm controles para personalizar os recursos e as configurações de privacidade a qualquer momento. Nosso compromisso com a relação de confiança e de transparência é claro:

- Estamos abrir com clientes sobre os tipos de dados coletadas.
- Colocamos clientes corporativos no controle, eles podem personalizar suas próprias configurações de privacidade.
- Colocamos segurança e privacidade do cliente pela primeira vez.
- Estamos transparentes sobre como os dados de telemetria são usados.
- Usamos os dados de telemetria para aprimorar as experiências de clientes.

Microsoft não pretende coletar dados confidenciais, como números de cartão de crédito, nomes de usuário e senhas, endereços de email ou informações confidenciais semelhantes. Se, determinamos que informações confidenciais inadvertidamente recebidas, podemos excluí-la.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria

Telemetria desempenha um papel importante em ajudando a rapidamente identificar e corrigir problemas de confiabilidade crítica em configurações e implantações de cliente. Informações dos dados de telemetria podem ajudar a identificar problemas com os serviços ou configurações de hardware. A capacidade da Microsoft para obter esses dados de clientes e melhorias de unidade para o ecossistema eleva o padrão para a qualidade de soluções integradas de pilha do Azure.

Telemetria também ajuda a Microsoft a entender melhor como os clientes implantar componentes, usar os recursos e usar serviços para atingir suas metas de negócios. Essas informações ajudam a priorizar os investimentos em engenharia em áreas que podem afetar diretamente experiências de clientes e cargas de trabalho.

Alguns exemplos incluem o uso do cliente de contêineres, armazenamento e as configurações de rede que estão associadas a funções de pilha do Azure. Também podemos usar as informações para gerar melhorias e inteligência em soluções de monitoramento e gerenciamento da pilha do Azure. Esses aprimoramentos facilitam para os clientes diagnosticar problemas e economizar dinheiro fazendo suporte menos chamadas para Microsoft.

## <a name="manage-telemetry-collection"></a>Gerenciar a coleção de telemetria

Nós não recomendada desativando a telemetria em sua organização. No entanto, em alguns cenários isso pode ser necessário.

Nesses cenários, você pode configurar o nível de telemetria enviado à Microsoft, usando as configurações do registro antes de implantar a pilha do Azure, ou usando os pontos de extremidade de telemetria depois de implantar a pilha do Azure.

### <a name="telemetry-levels-and-data-collection"></a>Coleção de níveis e os dados de telemetria

Antes de alterar as configurações de telemetria, você deve entender os níveis de telemetria e quais dados são coletados em cada nível.

As configurações de telemetria são agrupadas em quatro níveis (0-3) que são cumulativas e categorizados como a seguir:

**0 (segurança)**</br>
Somente dados de segurança. Informações necessárias para proteger o sistema operacional. Isso inclui dados sobre as configurações de componente de telemetria e experiência do usuário conectado e o Windows Defender. Não há telemetria específica para a pilha do Azure é emitida nesse nível.

**1 (básico)**</br>
Dados de segurança e integridade básica e qualidade de dados. Informações básicas do dispositivo, incluindo: dados relacionados a qualidade, a compatibilidade de aplicativo, dados de uso do aplicativo e os dados do **segurança** nível. Definindo o nível de telemetria a telemetria de pilha do Azure permite básico. Os dados coletados com esse nível incluem:

- *Informações básicas do dispositivo* que oferece uma compreensão sobre os tipos e as configurações das instâncias de Windows Server 2016 native e virtuais no ecossistema do. Isso inclui:

  - Atributos de máquina, como OEM e modelo.
  - Atributos, como o número de adaptadores de rede e sua velocidade de rede.
  - Atributos de processador e memória, como o número de núcleos e a quantidade de memória instalada.
  - Atributos de armazenamento, como o número de unidades, o tipo de unidade e o tamanho da unidade.

- *Funcionalidade de telemetria*, incluindo o percentual de eventos carregados, eventos descartados e os últimos dados de tempo de carregamento.
- *Informações relacionadas à qualidade* que ajuda a Microsoft a desenvolver uma compreensão básica do desempenho de pilha do Azure. Por exemplo, a contagem de alertas críticos em uma configuração de hardware específica.
- *Dados de compatibilidade* que ajuda a fornecer uma compreensão sobre quais provedores de recursos são instalados em um sistema e uma máquina virtual. Isso identifica possíveis problemas de compatibilidade.

**2 (Avançado)**</br>
Informações adicionais, incluindo: como o sistema operacional e serviços de pilha do Azure são usados, como executam esses serviços, confiabilidade avançada dados e dados do **segurança** e **básica** níveis.

> [!NOTE]
> Essa é a configuração de telemetria do padrão.

**3 (completo)**</br>
Todos os dados necessários para identificar e ajudar a corrigir problemas, mais dados do **segurança**, **básica**, e **avançado** níveis.

> [!IMPORTANT]
> Esses níveis de telemetria se aplicam somente aos componentes da pilha do Microsoft Azure. Componentes de software não Microsoft e serviços que estão em execução no Host de ciclo de vida do Hardware de parceiros de hardware do Azure pilha podem se comunicar com seus serviços de nuvem fora desses níveis de telemetria. Você deve trabalhar com seu provedor de solução de hardware de pilha do Azure para entender a política de telemetria e como você pode aceitar ou recusar.

Desativando a telemetria do Windows e a pilha do Azure também desabilita a telemetria do SQL. Para obter mais informações sobre as implicações das configurações de telemetria do Windows Server, consulte o [white paper de telemetria do Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: definir o nível de telemetria no registro do Windows

Você pode usar o Editor de registro do Windows para definir manualmente o nível de telemetria no computador host físico antes de implantar a pilha do Azure. Se já existir uma política de gerenciamento, como a política de grupo, ela substitui a configuração do registro.

Antes de implantar o Azure pilha no host do kit de desenvolvimento, inicialize o CloudBuilder.vhdx e execute o script a seguir em uma janela elevada do PowerShell:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK e vários nós: habilitar ou desabilitar a telemetria após a implantação

Para habilitar ou desabilitar a telemetria após a implantação, você precisa ter acesso para o ponto de extremidade privilegiado (PEP) que é exposta nas VMs ERCS.

1. Para habilitar: `Set-Telemetry -Enable`
2. Para desabilitar o: `Set-Telemetry -Disable`

Detalhes de parâmetros:
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

**Script para desativar a telemetria:**

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

- [Baixe o pacote de implantação do kit de desenvolvimento do Azure pilha](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Implantar o kit de desenvolvimento de pilha do Azure](azure-stack-run-powershell-script.md)
