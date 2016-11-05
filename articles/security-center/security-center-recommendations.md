---
title: Gerenciando recomendações de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2016
ms.author: terrylan

---
# Gerenciando recomendações de segurança na Central de Segurança do Azure
Este documento mostra como usar as recomendações na Central de Segurança do Azure para ajudar a proteger os recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.
> 
> 

## O que são recomendações de segurança?
A Central de Segurança analisa periodicamente o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, cria recomendações. As recomendações o orientam ao longo do processo de configuração dos controles necessários.

## Implementando recomendações de segurança
### Definir recomendações
Em [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md), você aprenderá a:

* Configurar políticas de segurança.
* Habilitar a coleta de dados.
* Escolher quais recomendações ver como parte da política de segurança.

As recomendações de política atuais giram em torno de atualizações do sistema, regras de linha de base, programas antimalware, [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) em sub-redes e em interfaces de rede, auditoria do banco de dados SQL, Transparent Data Encryption do banco de dados SQL e firewalls do aplicativo Web. [Configurando políticas de segurança](security-center-policies.md) fornece uma descrição de cada opção de recomendação.

### Monitorar as recomendações
Depois de definir uma política de segurança, a Central de Segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **Recomendações** na folha **Central de Segurança** permite saber o número total de recomendações identificadas pela Central de Segurança.

![Bloco Recomendações][1]

Para ver os detalhes de cada recomendação:

1. Clique no **bloco Recomendações** na folha **Central de Segurança**. A folha **Recomendações** será aberta.

As recomendações são mostradas em um formato de tabela, em que cada linha representa uma recomendação específica. As colunas da tabela são:

* **DESCRIÇÃO**: explica a recomendação e o que precisa ser feito para resolvê-la.
* **RECURSO**: lista os recursos aos quais essa recomendação se aplica.
* **ESTADO**: descreve o estado atual da recomendação:
  * **Aberta**: a recomendação ainda não foi resolvida.
  * **Em Andamento**: a recomendação está sendo aplicada atualmente aos recursos, e não é necessário que você realize nenhuma ação.
  * **Resolvida**: a recomendação já foi concluída (nesse caso, a linha ficará esmaecida).
* **GRAVIDADE**: descreve a gravidade dessa recomendação específica:
  * **Alta**: existe uma vulnerabilidade em um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e ela requer atenção.
  * **Média**: existe uma vulnerabilidade, e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
  * **Baixa**: existe uma vulnerabilidade que deve ser resolvida, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.)

Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis e o que cada uma delas fará se você as aplicar.

> [!NOTE]
> Você deve compreender os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.
> 
> 

| Recomendações | Descrição |
| --- | --- |
| [Habilitar coleta de dados para assinaturas](security-center-enable-data-collection.md) |Recomenda que você ative a coleta de dados na política de segurança para cada uma de suas assinaturas e todas as VMs (máquinas virtuais) em suas assinaturas. |
| [Corrigir as vulnerabilidades do sistema operacional](security-center-remediate-os-vulnerabilities.md) |Recomenda que você alinhe as configurações do sistema operacional com as regras de configuração recomendadas, por exemplo, não permitir o armazenamento de senhas. |
| [Aplicar atualizações do sistema](security-center-apply-system-updates.md) |Recomenda que você implante a atualizações críticas e de segurança do sistema ausentes para VMs. |
| [Reinicializar após as atualizações do sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Recomenda que você reinicialize uma VM para concluir o processo de aplicação de atualizações de sistema. |
| [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md) |Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web. Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes. Dispositivos WAF (criados usando o modelo de implantação do Gerenciador de Recursos) precisam ser implantados em uma rede virtual separada. Dispositivos WAF (criados usando o modelo de implantação clássico) são restritos ao uso de um grupo de segurança de rede. No futuro, esse suporte será estendido para uma implantação totalmente personalizada de um dispositivo WAF (clássica). A Central de Segurança recomendará que você provisione um WAF para ajudar a proteger contra ataques direcionados a seus aplicativos Web em VMs e no ASE (Ambiente do Serviço de Aplicativo). Para saber mais sobre o ASE, consulte a [Documentação do Ambiente do Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md). |
| [Finalizar a proteção do aplicativo](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Se essa recomendação for seguida, serão concluídas as alterações de configuração necessárias. |
| [Adicionar um Firewall de Última Geração](security-center-add-next-generation-firewall.md) |Recomenda que você adicione um Firewall de Última Geração (NGFW) de um parceiro da Microsoft para aumentar suas proteções de segurança. |
| [Rotear o tráfego apenas através do NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recomenda que você configure regras para o grupo de segurança de rede (NSG) que forcem o tráfego de entrada em sua VM a passar pelo NGFW. |
| [Instalar proteção do ponto de extremidade](security-center-install-endpoint-protection.md) |Recomenda que você provisione programas antimalware para máquinas virtuais (somente VMs do Windows). |
| [Resolver alertas de integridade do Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md) |Recomenda que você resolva falhas do Endpoint Protection. |
| [Habilitar Grupos de Segurança de Rede em sub-redes ou máquinas virtuais](security-center-enable-network-security-groups.md) |Recomenda que você habilite NSGs em sub-redes ou VMs. |
| [Restringir o acesso por meio de ponto de extremidade para a Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Recomenda que você configure regras de tráfego de entrada para NSGs. |
| [Habilitar a auditoria do servidor SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda que você habilite auditoria para servidores Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais). |
| [Habilitar auditoria para Banco de Dados SQL](security-center-enable-auditing-on-sql-databases.md) |Recomenda que você habilite auditoria para bancos de dados do Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais). |
| [Habilitar Transparent Data Encryption em bancos de dados SQL](security-center-enable-transparent-data-encryption.md) |Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL). |
| [Habilitar o Agente de VM](security-center-enable-vm-agent.md) |Permite que você veja quais máquinas virtuais exigem o Agente de VM. O Agente de VM deve ser instalado em VMs para provisionar verificação de linha de base, verificação de patch e programas antimalware. O agente de VM está instalado por padrão nas VMs implantadas do Azure Marketplace. O artigo [Agente de VM e extensões – parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornece informações sobre como instalar o Agente de VM. |
| [Aplicar a criptografia de disco](security-center-apply-disk-encryption.md) |Recomenda de que você criptografe os discos de VM usando o Azure Disk Encryption (VMs do Windows e do Linux). A criptografia é recomendada para volumes de dados e do sistema operacional em sua VM. |
| [Fornecer detalhes de contato de segurança](security-center-provide-security-contact-details.md) |Recomenda que você forneça informações de contato de segurança para cada uma das suas assinaturas. Informações de contato são um número de telefone e um endereço de email. As informações serão usadas para contatá-lo se nossa equipe de segurança acreditar que os recursos estão comprometidos. |
| [Atualizar a versão do sistema operacional](security-center-update-os-version.md) |Recomenda que você atualize a versão do SO (sistema operacional) de seu Serviço de Nuvem para a versão mais recente disponível para a família do SO. Para saber mais sobre os Serviços de Nuvem, confira a [Visão geral dos serviços de nuvem](../cloud-services/cloud-services-choose-me.md). |
| [Avaliação de vulnerabilidade não instalada](security-center-vulnerability-assessment-recommendations.md) |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| [Corrigir vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) |Permite que você veja as vulnerabilidades do sistema e dos aplicativos detectadas pela solução de avaliação de vulnerabilidade instalada na VM. |

Você pode filtrar e ignorar as recomendações.

1. Clique em **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e de estado que deseja ver.
   
    ![Recomendações de filtro][2]
2. Se você determinar que uma recomendação não se aplica, poderá ignorar a recomendação e removê-la da exibição. Há duas maneiras de ignorar uma recomendação. Uma das maneiras consiste em clicar com o botão direito do mouse em um item e selecionar **Ignorar**. A outra maneira é passar o mouse sobre um item, clicar nos três pontos que aparecem à direita e selecionar **Ignorar**. Você pode exibir as recomendações ignoradas ao clicar em **Filtro** e selecionar **Ignoradas**.
   
    ![Ignorar recomendação][3]

### Aplicar recomendações
Depois de examinar todas as recomendações, decida qual delas aplicar primeiro. É recomendável usar a classificação de gravidade como o parâmetro principal para avaliar quais recomendações devem ser aplicadas primeiro.

Na tabela de recomendações acima, selecione uma recomendação e execute suas etapas como um exemplo de como aplicar uma recomendação.

## Confira também
Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png

<!---HONumber=AcomDC_0928_2016-->