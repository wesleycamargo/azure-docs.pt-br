---
title: Telemetria de pilha do Azure | Microsoft Docs
description: "Descreve como definir as configurações de telemetria de pilha do Azure usando o PowerShell."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 5cd8d4045764b753c5fdd81ade98d69c72709881
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Dados de sistema de pilha do Azure ou telemetria, é carregada automaticamente à Microsoft por meio da experiência do usuário conectado. Dados coletados de telemetria de pilha do Azure são usados pelas equipes da Microsoft principalmente para melhorar nosso experiências de clientes e para análise de integridade, qualidade, desempenho e segurança.

Como um operador de pilha do Azure, telemetria pode fornecer informações valiosas sobre implantações corporativas e oferece uma voz que ajuda a versões futuras de forma da pilha do Azure.

> [!NOTE]
> A pilha do Azure também pode ser configurada para informações sobre o uso de encaminhamento para o Azure para cobrança. Isso é necessário para clientes de vários nós do Azure pilha que escolha pago como você-uso cobrança. Relatório de uso é controlado independentemente de telemetria e não é necessário para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento de pilha do Azure. Nessas situações, o relatório de uso pode ser desativado [usando o script de registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting). 

Telemetria de pilha do Azure baseia-se o componente de telemetria e experiência de usuário conectado do Windows Server 2016, que usa o [rastreamento de eventos para Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia de registro em log para coletar e armazenar dados e eventos de telemetria de rastreamento. Componentes da pilha do Azure usam a mesma tecnologia de registro em log para publicar eventos e os dados são coletados usando o log de eventos do sistema operacional público e APIs de rastreamento. Provedor de recursos de rede, o provedor de recursos de armazenamento, o provedor de recursos de monitoramento e o provedor de recursos de atualização são exemplos de componentes da pilha do Azure. O componente de experiência do usuário conectado e telemetria criptografa dados usando SSL e usa a fixação de certificado para transmitir dados de telemetria via HTTPS para o serviço de gerenciamento de dados da Microsoft.

> [!NOTE]
> Para oferecer suporte ao fluxo de dados de telemetria, a porta 443 (HTTPS) deve estar aberta em sua rede. O componente de experiência do usuário conectado e telemetria conecta-se para o serviço de gerenciamento de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente de experiência do usuário conectado e telemetria também se conecta ao https://settings-win.data.microsoft.com para baixar informações de configuração.

## <a name="privacy-considerations"></a>Considerações sobre privacidade
O serviço ETW encaminha os dados de telemetria para armazenamento em nuvem protegida. O princípio de menos privilégios guias acessem dados de telemetria. Somente funcionários da Microsoft com uma necessidade comercial válido têm acesso aos dados de telemetria. A Microsoft não compartilha dados pessoais de nossos clientes com terceiros, exceto a critério do cliente ou para fins limitado descrito no [declaração de privacidade do Azure pilha](http://windows.microsoft.com/windows/preview-privacy-statement). Compartilhamos relatórios comerciais com os OEMs e os parceiros que incluem informações de telemetria anônimos, agregados. Decisões de compartilhamento de dados são feitos por uma equipe interna do Microsoft incluindo participantes do gerenciamento de dados, legal e privacidade.

A Microsoft acredita em e minimização de informações de práticas. Buscamos coletar somente as informações de que precisamos e armazenamos apenas enquanto ele é necessário para fornecer um serviço ou para análise. Muitas das informações sobre como o sistema de pilha do Azure e os serviços do Azure estão funcionando é excluída em seis meses. Resumidos ou dados agregados serão mantidos por um período maior.

Compreendemos que a privacidade e segurança de informações de nossos clientes é muito importante.  Levamos uma abordagem ponderada e abrangente para privacidade do cliente e a proteção de dados do cliente com a pilha do Azure. Os administradores de TI têm controles para personalizar os recursos e as configurações de privacidade a qualquer momento. Nosso compromisso com a relação de confiança e de transparência é claro:
- Estamos abrir com clientes sobre os tipos de dados coletadas.
- Colocamos clientes corporativos no controle, eles podem personalizar suas próprias configurações de privacidade.
- Colocamos segurança e privacidade do cliente pela primeira vez.
- Estamos transparentes sobre como a telemetria é usada.
- Usamos a telemetria para aprimorar as experiências de clientes.

Microsoft não pretende coletar informações confidenciais, como números de cartão de crédito, nomes de usuário e senhas, endereços de email ou outras informações confidenciais da mesma forma. Se, determinamos que informações confidenciais inadvertidamente recebidas, podemos excluí-la.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria
Telemetria desempenha um papel importante para nos ajudar a rapidamente identificar e corrigir problemas de confiabilidade crítica nas configurações e implantações de nossos clientes. Ideias sobre os dados de telemetria coletadas nos ajudar a identificar rapidamente problemas de serviços ou configurações de hardware. A capacidade da Microsoft para obter esses dados de clientes e melhorias de unidade para o ecossistema de ajuda a elevar o nível de qualidade de nossas soluções integradas de pilha do Azure. 

Telemetria também ajuda a Microsoft a entender melhor como os clientes implantar componentes, usar os recursos e usar serviços para atingir suas metas de negócios. Obtendo informações de que os dados nos ajuda a priorizar nossos investimentos de engenharia em áreas que podem afetar diretamente as experiências de nossos clientes e cargas de trabalho.

Alguns exemplos incluem o uso do cliente de contêineres, armazenamento e as configurações de rede que estão associadas a funções de pilha do Azure. Também podemos usar as ideias para aprimoramentos de unidade e sobre alguns dos nossos gerenciamento e soluções de monitoramento.  Isso ajuda os clientes a diagnosticar problemas de qualidade e economizar dinheiro fazendo suporte menos chamadas para Microsoft.

## <a name="manage-telemetry-collection"></a>Gerenciar a coleção de telemetria
Não é recomendável que você desative telemetria em sua organização como telemetria oferece dados que orienta a estabilidade e funcionalidade aprimorada de produto. Reconhecemos no entanto, que em alguns cenários isso pode ser necessário. 

Nesses casos, você pode configurar o nível de telemetria enviado à Microsoft usando a pré-implantação de configurações de registro ou usando o pós-implantação de pontos de extremidade de telemetria.

### <a name="asdk-set-telemetry-level-in-the-windows-registry"></a>ASDK: definir o nível de telemetria no registro do Windows
O Editor de registro do Windows é usado para definir o nível de telemetria manualmente no computador host físico antes de implantar a pilha do Azure. Se já existir uma política de gerenciamento, como a política de grupo, ele substituirá a configuração do registro. 

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

Os níveis de telemetria são cumulativas e categorizados em quatro níveis (0-3):

**0 (segurança)**. Somente dados de segurança. Proteger informações obrigatórias para ajudar a manter o sistema operacional, incluindo dados sobre o Windows Defender e as configurações de componentes de telemetria e experiência do usuário conectado. Não há telemetria específica de pilha do Azure é emitida nesse nível.

**1 (básico)**. Dados de segurança e integridade básica e qualidade de dados. Informações básicas do dispositivo, incluindo: dados de nível de segurança, compatibilidade de aplicativo, dados de uso do aplicativo e dados relacionados à qualidade. Definindo o nível de telemetria a telemetria de pilha do Azure permite básico. Os dados coletados com esse nível incluem:

- **Informações básicas do dispositivo** que ajuda a fornecer uma compreensão sobre os tipos e as configurações de instâncias virtualizadas e nativas do Windows Server 2016 no ecossistema do, incluindo:
 - Atributos de máquina, como OEM, modelagem, 
 - Atributos de rede, como o número e a velocidade dos adaptadores de rede
 - Processador e memória atributos, como o número de núcleos, tamanho da memória, 
 - Atributos de armazenamento, como o número de unidades, tipo e tamanho.
- **Funcionalidade de telemetria**, incluindo % dos eventos carregados, eventos descartados e o último tempo de carregar.
- **Informações relacionadas à qualidade** que ajuda a Microsoft a desenvolver uma compreensão básica do desempenho de pilha do Azure. Um exemplo é a contagem de alertas críticos em uma configuração de hardware específica.
- **Dados de compatibilidade** que ajuda a fornecer uma compreensão sobre quais provedores de recursos são instalados em um sistema e a máquina virtual e identifica possíveis problemas de compatibilidade.

**2 (Avançado)**. Informações adicionais, incluindo: como o sistema operacional e outros serviços de pilha do Azure são usados, seu desempenho, confiabilidade avançada de dados e dados de níveis de segurança e do Basic. 

**3 (completo)**. Todos os dados necessários para identificar e ajudar a corrigir problemas, mais dados do **segurança**, **básica**, e **avançado** níveis.

> [!NOTE]
> O valor de nível de telemetria padrão é 2 (Avançado).

Observe que desativando a telemetria do Windows e a pilha do Azure também desabilitará a telemetria do SQL. Para obter informações adicionais sobre as implicações de telemetria do Windows Server, configurações, consulte o [white paper de telemetria do Windows](https://aka.ms/winservtelemetry). 

> [!IMPORTANT]
> Esses níveis de telemetria se aplicam somente aos componentes da pilha do Microsoft Azure. Componentes de software não Microsoft e serviços que estão em execução no Host de ciclo de vida do Hardware de parceiros de hardware do Azure pilha podem se comunicar com seus serviços de nuvem fora desses níveis de telemetria. Você deve trabalhar com seu provedor de solução de hardware de pilha do Azure para entender a política de telemetria e como você pode aceitar ou recusar. 

### <a name="asdk-and-multinode-enable-or-disable-telemetry-after-deployment"></a>ASDK e vários nós: habilitar ou desabilitar a telemetria após a implantação

Para habilitar ou desabilitar a telemetria após a implantação, você precisa ter acesso para o ponto de extremidade privilegiado (PEP) que é exposta nas VMs ERCS.
1.  Para habilitar:`Set-Telemetry -Enable`
2.  Para desabilitar o:`Set-Telemetry -Disable`

Detalhes de parâmetro: 
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
[Baixe o pacote de implantação do kit de desenvolvimento do Azure pilha](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implantar o kit de desenvolvimento de pilha do Azure](azure-stack-run-powershell-script.md)

