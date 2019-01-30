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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8eb02eadba68f4e226e09e8e243e6c88eb0dc9fe
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239189"
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

Dados de sistema do Azure Stack ou telemetria é carregada automaticamente à Microsoft por meio da experiência do usuário conectado. Dados coletados de telemetria do Azure Stack são usados pelas equipes da Microsoft principalmente para melhorar nossas experiências de cliente e para análise de desempenho, integridade, qualidade e segurança.

Como um operador do Azure Stack, a telemetria pode fornecer informações valiosas sobre implantações empresariais e lhe dá uma voz que ajuda a versões futuras de forma do Azure Stack.

> [!NOTE]
> O Azure Stack também pode ser configurado para informações de uso de encaminhar para o Azure para cobrança. Isso é necessário para clientes com vários nós do Azure Stack que optam por pagamento-como-o uso cobrança. Relatório de uso é controlado independentemente da telemetria e não é necessário para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento do Azure Stack. Para esses cenários, o relatório de uso pode ser desativado [usando o script de registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria de pilha do Azure baseia-se o componente de telemetria e experiência de usuário conectado do Windows Server 2016, que usa o [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia de registro em log para coletar e armazenar eventos de telemetria e dados de rastreamento. Componentes de pilha do Azure usam a mesma tecnologia de registro em log para publicar eventos e os dados são coletados usando o log de eventos do sistema de operacional público e o rastreamento de APIs. Provedor de recursos de rede, provedor de recursos de armazenamento, provedor de recursos de monitoramento e atualização do provedor de recursos são exemplos de componentes do Azure Stack. O componente de experiência do usuário conectado e telemetria criptografa dados usando SSL e usa a anexação de certificado para transmitir dados de telemetria via HTTPS para o serviço de gerenciamento de dados da Microsoft.

> [!NOTE]
> Para dar suporte a fluxo de dados de telemetria, a porta 443 (HTTPS) deve estar aberta na sua rede. O componente de experiência do usuário conectado e telemetria conecta-se para o serviço de gerenciamento de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente de experiência do usuário conectado e telemetria também se conecta ao https://settings-win.data.microsoft.com para baixar informações de configuração.

## <a name="privacy-considerations"></a>Considerações sobre privacidade
O serviço ETW encaminha os dados de telemetria para o armazenamento de nuvem protegida. O princípio de acesso menos privilegiado de guias para dados de telemetria. Somente funcionários da Microsoft com uma necessidade comercial válido podem acessar os dados de telemetria. A Microsoft não compartilha dados pessoais de nossos clientes com terceiros, exceto a critério do cliente ou para fins limitados descritos de [declaração de privacidade do Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Podemos compartilhar relatórios comerciais com parceiros que incluem informações de telemetria agregados, anônimos e os OEMs. Decisões de compartilhamento de dados são feitos por uma equipe interna de Microsoft incluindo stakeholders de gerenciamento de privacidade, legal e dados.

A Microsoft acredita em e minimização de informações de práticas. Nos esforçamos para coletar somente as informações que precisamos, e armazenamos só, desde que ele é necessário para fornecer um serviço ou para análise. Muitas das informações sobre como o sistema do Azure Stack e os serviços do Azure estão funcionando é excluída em até seis meses. Resumidos ou dados agregados são mantidos por um período mais longo.

Entendemos que a privacidade e segurança de informações de nossos clientes é importante. Uma abordagem abrangente e cuidadosa é que nós tiramos a privacidade do cliente e a proteção de dados do cliente com o Azure Stack. Os administradores de TI têm controles para personalizar as configurações de privacidade e os recursos a qualquer momento. Nosso compromisso com a transparência e a relação de confiança é claro:
- Somos abertos com os clientes sobre os tipos de dados que coletamos.
- Colocamos o controle nas mãos dos clientes corporativos; eles podem personalizar suas próprias configurações de privacidade.
- Nós colocamos a privacidade e a segurança dos clientes em primeiro lugar.
- Somos transparentes sobre como a telemetria é usada.
- Usamos a telemetria para melhorar as experiências de nossos clientes.

Microsoft não pretende coletar informações confidenciais, como números de cartão de crédito, nomes de usuário e senhas, endereços de email ou outras informações confidenciais da mesma forma. Se determinarmos que informações confidenciais foram recebidas inadvertidamente, podemos excluí-lo.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria
Telemetria desempenha um papel importante nos ajudar a identificar e corrigir problemas de confiabilidade críticos nas configurações e implantações de nossos clientes rapidamente. Informações sobre os dados de telemetria coletadas nos ajudar a identificar rapidamente problemas com os serviços ou configurações de hardware. A capacidade da Microsoft para obter esses dados de clientes e gerar melhorias no ecossistema do ajuda a elevar o nível para a qualidade de nossas soluções integradas do Azure Stack.

Telemetria também ajuda a Microsoft a entender melhor como os clientes implantam componentes, usar os recursos e usar os serviços para alcançar seus objetivos comerciais. Obtendo insights de dados ajuda a priorizar investimentos em engenharia nas áreas que podem afetar diretamente as cargas de trabalho e as experiências de nossos clientes.

Alguns exemplos incluem o uso do cliente de contêineres, armazenamento e as configurações da rede que estão associadas a funções do Azure Stack. Também podemos usar as informações para gerar melhorias e inteligência em algumas das soluções de monitoramento e nosso gerenciamento. Isso ajuda os clientes a diagnosticar problemas de qualidade e economizar dinheiro, tornando menos suporte chama para a Microsoft.

## <a name="manage-telemetry-collection"></a>Gerenciar coleta de telemetria
Não recomendamos que você desativar a telemetria em sua organização como telemetria fornece dados que impulsionam a funcionalidade aprimorada de produto e estabilidade. Reconhecemos no entanto, que em alguns cenários isso pode ser necessário.

Nesses casos, você pode configurar o nível de telemetria enviado à Microsoft usando a pré-implantação de configurações de registro ou usando a pontos de extremidade de telemetria após a implantação.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Definir o nível de telemetria no registro do Windows
O Editor de registro do Windows é usado para definir manualmente o nível de telemetria no computador host físico antes de implantar o Azure Stack. Se já existir uma política de gerenciamento, como a diretiva de grupo, ela substitui a configuração do registro.

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

Os níveis de telemetria são cumulativas e categorizadas em quatro níveis (0-3):

**0 (segurança)**. Somente dados de segurança. Proteger informações necessárias para ajudar a manter o sistema operacional, incluindo dados sobre as configurações de componente de experiência do usuário conectado e telemetria e o Windows Defender. Sem telemetria específica do Azure Stack é emitida nesse nível.

**1 (básico)**. Dados de segurança e integridade básica e a qualidade de dados. Informações básicas do dispositivo, incluindo: dados de nível de segurança, compatibilidade de aplicativos, dados de uso do aplicativo e dados relacionados à qualidade. Definindo seu nível de telemetria para a telemetria do Azure Stack permite básico. Os dados coletados com esse nível incluem:

- **Informações básicas do dispositivo** que ajuda a fornecer uma compreensão sobre os tipos e as configurações virtualizadas e nativas instâncias do Windows Server 2016 no ecossistema do, incluindo:
  - Atributos de computador, como o OEM, modelam,
  - Atributos de sistema de rede, como o número e a velocidade dos adaptadores de rede
  - Processador e os atributos de memória, como o número de núcleos, tamanho da memória,
  - Atributos de armazenamento, como o número de unidades, tipo e tamanho.
- **Funcionalidade de telemetria**, incluindo % dos eventos carregados, eventos ignorados e a última hora do carregamento.
- **Informações relacionadas à qualidade** que ajudam a Microsoft a desenvolver um entendimento básico de como está o desempenho do Azure Stack. Um exemplo é a contagem de alertas críticos em uma configuração de hardware específica.
- **Dados de compatibilidade**, que ajuda a fornecer um entendimento sobre quais provedores de recursos são instalados em um sistema e a máquina virtual e identifica possíveis problemas de compatibilidade.

**2 (Avançado)**. Informações adicionais, incluindo: como o sistema operacional e outros serviços do Azure Stack são usados, como eles se comportam, dados de confiabilidade avançadas e dados de níveis de segurança e do Basic.

**3 (completo)**. Todos os dados necessários para identificar e ajudar a corrigir problemas, além de dados a partir o **segurança**, **básica**, e **avançado** níveis.

> [!NOTE]
> O valor de nível de telemetria padrão é 2 (Avançado).

Desativando a telemetria do Windows e o Azure Stack desabilita a telemetria do SQL. Para obter informações adicionais sobre as implicações das configurações de telemetria do Windows Server, fazer referência a [white paper de telemetria do Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Esses níveis de telemetria se aplicam somente aos componentes do Microsoft Azure Stack. Componentes de software não Microsoft e serviços que estão em execução no Host de ciclo de vida do Hardware de parceiros de hardware do Azure Stack podem se comunicar com seus serviços de nuvem fora desses níveis de telemetria. Você deve trabalhar com seu provedor de soluções de hardware do Azure Stack para entender sua política de telemetria e como você pode aceitar ou recusar.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Habilitar ou desabilitar a telemetria depois da implantação

Para habilitar ou desabilitar a telemetria após a implantação, você precisa ter acesso para o ponto de extremidade com privilégios (PEP) que é exposta nas VMs ERCS.
1.  Para habilitar: `Set-Telemetry -Enable`
2.  Para desabilitar o: `Set-Telemetry -Disable`

Detalhes do parâmetro:
> . PARÂMETRO Enable - ativar o carregamento de dados de telemetria

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
[Iniciar e parar o ASDK](asdk-start-stop.md)
