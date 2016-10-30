<properties
   pageTitle="Protegendo suas máquinas virtuais na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento endereça as recomendações na Central de Segurança do Azure que ajudam a proteger suas máquinas virtuais e cumprir as políticas de segurança."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>


# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Protegendo suas máquinas virtuais na Central de Segurança do Azure

A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários.  As recomendações se aplicam aos tipos de recursos do Azure: máquinas virtuais (VMs), rede, aplicativos e SQL.

Este artigo endereça as recomendações que se aplicam às VMs.  As recomendações de VM giram em torno da coleta de dados, aplicando atualizações do sistema, provisionamento o antimalware, criptografando os discos da VM e muito mais.  Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis da VM e o que cada uma delas fará se você as aplicar.

## <a name="available-vm-recommendations"></a>Recomendações disponíveis da VM

|Recomendações|Descrição|
|-----|-----|
|[Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md)|Recomenda que você ative a coleta de dados na política de segurança para cada uma de suas assinaturas e todas as VMs (máquinas virtuais) em suas assinaturas.|
|[Corrigir as vulnerabilidades do sistema operacional](security-center-remediate-os-vulnerabilities.md)|Recomenda que você alinhe as configurações do sistema operacional com as regras de configuração recomendadas, por exemplo, não permitir o armazenamento de senhas.|
|[Aplicar atualizações do sistema](security-center-apply-system-updates.md)|Recomenda que você implante a atualizações críticas e de segurança do sistema ausentes para VMs.|
|[Reinicializar após as atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Recomenda que você reinicialize uma VM para concluir o processo de aplicação de atualizações de sistema.|
|[Instalar proteção do ponto de extremidade](security-center-install-endpoint-protection.md)|Recomenda que você provisione programas antimalware para máquinas virtuais (somente VMs do Windows).|
|[Resolver alertas de integridade do Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recomenda que você resolva falhas do Endpoint Protection.|
|[Habilitar o Agente de VM](security-center-enable-vm-agent.md)|Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e programas antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM.|
| [Aplicar a criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda de que você criptografe os discos de VM usando o Azure Disk Encryption (VMs do Windows e do Linux). A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM.|
| [Atualizar a versão do sistema operacional](security-center-update-os-version.md) | Recomenda que você atualize a versão do SO (sistema operacional) de seu Serviço de Nuvem para a versão mais recente disponível para a família do SO.  Para saber mais sobre os Serviços de Nuvem, confira a [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) | Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| [Corrigir vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite que você veja as vulnerabilidades do sistema e dos aplicativos detectadas pela solução de avaliação de vulnerabilidade instalada na VM. |

## <a name="see-also"></a>Consulte também

Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

- [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
- [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
- [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
- [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.



<!--HONumber=Oct16_HO2-->


