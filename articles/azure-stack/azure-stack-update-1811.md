---
title: Atualização da pilha 1811 do Azure | Microsoft Docs
description: Saiba mais sobre a atualização 1811 para sistemas integrados do Azure Stack, incluindo novidades, problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: ddcf3428f32698c9825f13975929bc4677139acf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081042"
---
# <a name="azure-stack-1811-update"></a>Atualização da pilha 1811 do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1811. O pacote de atualização inclui aprimoramentos, correções e novos recursos para esta versão do Azure Stack. Este artigo também descreve problemas conhecidos nesta versão e inclui um link para que você pode baixar a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados ao processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1811 **1.1811.0.101**.

## <a name="hotfixes"></a>Hotfixes

O Azure Stack libera hotfixes regularmente. Certifique-se de instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1809 antes de atualizar o Azure Stack para 1811.

> [!TIP]  
> Inscrever-se ao seguinte *RSS* ou *Atom* feeds manter-se atualizado com hotfixes do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Hotfixes de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nenhum hotfix atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Durante a instalação da atualização 1811, você deve garantir que todas as instâncias do portal do administrador serão fechadas. O portal do usuário pode permanecer aberto, mas o portal de administração deve ser fechado.

- Prepare sua implantação do Azure Stack para o host de extensão do Azure Stack. Prepare seu sistema usando as diretrizes a seguir: [Preparar para o host de extensão para o Azure Stack](azure-stack-extension-host-prepare.md). 
 
- Instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1809 antes de atualizar para o 1811.

- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Se você não tiver os requisitos de host de extensão atendidos, o `Test-AzureStack` saída exibe a seguinte mensagem: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare`

- A atualização do Azure Stack 1811 requer que você importou corretamente os certificados de host de extensão obrigatória para seu ambiente do Azure Stack. Para continuar com a instalação da atualização 1811, você deve importar os certificados SSL necessários para o host de extensão. Para importar os certificados, consulte [esta seção](azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Se você ignorar cada aviso e ainda optar por instalar a atualização 1811, a atualização falhará em aproximadamente 1 hora com a seguinte mensagem:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure/azure-stack/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Depois que você importou os certificados de host de extensão obrigatória corretamente, você pode retomar a atualização 1811 do portal do administrador. Embora a Microsoft recomenda que os operadores do Azure Stack para agendar uma janela de manutenção durante o processo de atualização, uma falha devido aos certificados de host de extensão ausente não deve afetar as cargas de trabalho ou os serviços existentes.  

    Durante a instalação dessa atualização, o portal do usuário do Azure Stack está disponível enquanto o host de extensão está sendo configurado. A configuração do host de extensão pode levar até 5 horas. Durante esse tempo, você pode verificar o status de uma atualização ou retomar uma instalação de atualização com falha usando o [PowerShell do Administrador do Azure Stack ou o ponto de extremidade com privilégios](azure-stack-monitor-update.md).

- Quando o Azure Stack é gerenciado pelo SCOM System Center Operations Manager (), certifique-se de atualizar o pacote de gerenciamento para Microsoft Azure Stack para versão 1.0.3.11 antes de aplicar 1811.

## <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes novos recursos e melhorias para o Azure Stack:

- Com esta versão, o [host de extensão](azure-stack-extension-host-prepare.md) está habilitado. O host de extensão simplifica a integração de rede e melhora a postura de segurança do Azure Stack.

- Adicionado suporte para autenticação de dispositivo com o Active Directory Federated Services (AD FS), ao usar a CLI do Azure em particular. Para obter mais informações, consulte [perfis de versão da API de uso com a CLI do Azure no Azure Stack](./user/azure-stack-version-profiles-azurecli2.md)

- Adicionado suporte para entidades de serviço usando um segredo do cliente com o Active Directory Federated Services (AD FS). Para obter mais informações, consulte [criar entidade de serviço do AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Essa versão adiciona suporte para as seguintes versões de API do serviço de armazenamento do Azure: **2017-07-29**, **2017-11-09**. Também é adicionado suporte para as seguintes versões de API de provedor de recursos de armazenamento do Azure: **2016-05-01**, **2016-12-01**, **2017-06-01**, e **2017-10-01**. Para obter mais informações, consulte [armazenamento do Azure Stack: Diferenças e considerações](./user/azure-stack-acs-differences.md).

- Adicionado novos comandos de ponto de extremidade com privilégios para atualizar e remover entidades de serviço para ADFS. Para obter mais informações, consulte [criar entidade de serviço do AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

- Adicionadas novas operações de nó de unidade de escala que permitem que um operador do Azure Stack Iniciar, parar e desligar um nó de unidade de escala. Para obter mais informações, consulte [ações de nó de unidade de dimensionamento no Azure Stack](azure-stack-node-actions.md).

- Adicionada uma nova folha de propriedades de região que exibe os detalhes do registro do ambiente. Você pode exibir essas informações clicando o **gerenciamento de região** bloco no painel do padrão no portal do administrador e, em seguida, selecionando **propriedades**.

- Adicionado um novo comando de ponto de extremidade com privilégios para atualizar a credencial BMC com o nome de usuário e senha, usada para se comunicar com máquinas físicas. Para obter mais informações, consulte [atualizar baseboard management controller \(BMC) credencial](azure-stack-rotate-secrets.md).

- Adicionada a capacidade de acessar o roteiro do Azure no entanto o ícone de Ajuda e suporte (ponto de interrogação) no canto superior direito dos portais administrador e usuário, semelhantes à forma como ele está disponível no portal do Azure.

- Adicionado um melhor gerenciamento de Marketplace experiência para usuários desconectados. O processo de carregamento para publicar um item do Marketplace em um ambiente desconectado é simplificado para uma única etapa, em vez de carregar a imagem e o pacote de Marketplace separadamente. O produto carregado também serão visível na folha de gerenciamento do Marketplace. Para saber mais, consulte [esta seção](azure-stack-download-azure-marketplace-item.md#import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher). 

- Esta versão reduz a janela de manutenção necessária para a rotação secreta adicionando a capacidade para girar apenas certificados externos durante [rotação do segredo do Azure Stack](azure-stack-rotate-secrets.md).

- [O Azure Stack PowerShell](azure-stack-powershell-install.md) foi atualizado para a versão 1.6.0. A atualização inclui suporte para os novos recursos de armazenamento no Azure Stack. Para obter mais informações, consulte as notas de versão para o [módulo de administração do Azure Stack 1.6.0 na Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0) para obter informações sobre como atualizar ou instalar o Azure Stack PowerShell, consulte [instalar o PowerShell para O Azure Stack](azure-stack-powershell-install.md).

- Discos gerenciados agora está habilitado por padrão durante a criação de máquinas virtuais usando o portal do Azure Stack. Consulte a [problemas conhecidos](#known-issues-post-installation) seção para obter as etapas adicionais necessárias para o Managed Disks evitar falhas de criação da VM.

- Esta versão apresenta alerta **reparo** ações para o operador do Azure Stack. Alguns alertas em 1811 fornecem uma **reparo** botão no alerta que você pode selecionar para resolver o problema. Para obter mais informações, consulte [monitorar a integridade e alertas no Azure Stack](azure-stack-monitor-health.md).

- Atualizações para a experiência de atualização no Azure Stack. Os aprimoramentos de atualização incluem: 
  - Guias de dividir as atualizações do histórico de atualização para atualizações de acompanhamento de melhor em andamento e concluídas as atualizações.
  - Visualizações de estado aprimorado na seção essentials com novos ícones e o layout para as versões atuais e de OEM, bem como a última data de atualização.
  - **Modo de exibição** vincular para a coluna de notas de versão leva o usuário diretamente para a documentação específica para que a atualização, em vez da página de atualização genérica.
  - O **histórico de atualizações** guia usada para determinar os tempos de execução para cada uma das atualizações bem como recursos de filtragem avançados.  
  - Unidades de escala de pilha do Azure que são conectadas automaticamente ainda receberá **atualização disponível** assim que estiverem disponíveis.
  - Unidades de escala de pilha do Azure que não estão conectadas podem importar as atualizações assim como antes. 
  - Não há nenhuma alteração no processo de baixar os logs de JSON a partir do portal. Operadores do Azure Stack verá expandindo etapas expressar o progresso.

    Para obter mais informações, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD - IS ASDK --> 
- Corrigido um problema em que os dados de medição do uso de endereço IP públicos mostraram o mesmo **EventDateTime** valor para cada registro, em vez da **TimeDate** carimbo que mostra que o registro foi criado. Agora você pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- 3099544 – IS, ASDK --> 
- Corrigido um problema que ocorreu durante a criação de uma nova máquina virtual (VM) usando o portal do Azure Stack. Selecionando o tamanho VM que causou a **USD por mês** coluna para exibir um **indisponível** mensagem. Esta coluna não aparece mais; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Corrigido um problema em que o portal do administrador, ao acessar os detalhes de qualquer assinatura de usuário, depois de fechar a folha e clicando em **recentes**, o nome da assinatura de usuário não foram exibidos. O nome de usuário da assinatura agora é exibida.

<!-- 3060156 - IS ASDK --> 
- Corrigido um problema em portais de administrador e o usuário: clicar em configurações do portal e selecionando **excluir todas as configurações e painéis privados** não funcionou conforme o esperado e uma notificação de erro foi exibida. Essa opção agora funciona corretamente.

<!-- 2930799 - IS ASDK --> 
- Corrigido um problema em portais de administrador e o usuário: em **todos os serviços**, o ativo **planos de proteção contra DDoS** estava listado incorretamente. Ele não está disponível no Azure Stack. A listagem foi removida.
 
<!--2760466 – IS  ASDK --> 
- Corrigido um problema que ocorreu quando você instalou um novo ambiente do Azure Stack, no qual o alerta indicar **ativação necessária** não é exibido. Agora exiba corretamente.

<!--1236441 – IS  ASDK --> 
- Corrigido um problema que impedia a aplicação de políticas RBAC a um grupo de usuários ao usar o ADFS.

<!--3463840 - IS, ASDK --> 
- Corrigido um problema com backups de infra-estrutura falhando devido a um servidor de arquivos acessível da rede VIP pública. Essa correção volta o serviço de backup de infraestrutura à rede de infraestrutura pública. Se você aplicou a versão mais recente [hotfix do Azure Stack para 1809](#azure-stack-hotfixes) que resolve esse problema, a atualização 1811 não fará as modificações adicionais. 

<!-- 2967387 – IS, ASDK --> 
- Corrigido um problema em que a conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Esta mensagem foi exibida quando a conta não tem qualquer um uma **primeira** ou **última** nome especificado.   

<!--  2873083 - IS ASDK --> 
- Corrigido um problema no qual usando o portal para criar um conjunto de dimensionamento de máquinas virtuais (VMSS) causado a **tamanho da instância** na lista suspensa para não carregar corretamente ao usar o Internet Explorer. Este navegador agora funciona corretamente.  

<!-- 3190553 - IS ASDK -->
- Corrigido um problema que geraram alertas barulhentas que indica que uma instância de função de infraestrutura não estava disponível ou nó de unidade de escala estava offline.

<!-- 2724961 - IS ASDK -->
- Fixo um problema em que a página de visão geral VM não é possível mostrar corretamente o gráfico de métricas VM. 

## <a name="changes"></a>Alterações

- Uma nova maneira de exibir e editar as cotas em um plano é introduzida no 1811. Para obter mais informações, consulte [exibir uma cota existente](azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Aprimoramentos de segurança nesta atualização resultaram em um aumento no tamanho do backup da função de serviço de diretório. Para atualizada orientação para o local de armazenamento externo de dimensionamento, consulte o [documentação da infraestrutura de backup](azure-stack-backup-reference.md#storage-location-sizing). Essa alteração resulta em mais tempo para concluir o backup devido à transferência de dados de tamanho maior. Essa alteração afeta os sistemas integrados. 

- O cmdlet PEP existente para recuperar as chaves de recuperação do BitLocker será renomeado no 1811, de Get-AzsCsvsRecoveryKeys para Get-AzsRecoveryKeys. Para obter mais informações sobre como recuperar as chaves de recuperação do BitLocker, consulte [obter instruções sobre como recuperar as chaves](azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as atualizações de segurança a seguir:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Para obter mais informações sobre essas vulnerabilidades, clique nos links acima ou consulte os artigos da Base de dados de Conhecimento da Microsoft [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando você executa o **Get-AzureStackLog** cmdlet do PowerShell após a execução **teste AzureStack** na mesma sessão do ponto de extremidade com privilégios (PEP), **Get-AzureStackLog** falhar. Para contornar esse problema, feche a sessão PEP no qual você executou **teste AzureStack**e, em seguida, abra uma nova sessão para ser executado **Get-AzureStackLog**.

- Durante a instalação do 1811, assegure que todas as instâncias do portal do administrador são fechadas durante esse tempo. O portal do usuário pode permanecer aberto, mas o portal de administração deve ser fechado.

- Ao executar [teste AzureStack](azure-stack-diagnostic-test.md), se o **AzsInfraRoleSummary** ou o **AzsPortalApiSummary** teste falhar, você será solicitado a executar  **Teste AzureStack** com o `-Repair` sinalizador.  Se você executar esse comando, ele falhará com a seguinte mensagem de erro:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Esse problema será corrigido em uma versão futura.

- Durante a instalação da atualização 1811, não o usar o portal do Azure Stack está disponível enquanto o host de extensão está sendo configurado. A configuração do host de extensão pode levar até 5 horas. Durante esse tempo, você pode verificar o status de uma atualização ou retomar uma instalação de atualização com falha usando o [PowerShell do Administrador do Azure Stack ou o ponto de extremidade com privilégios](azure-stack-monitor-update.md). 

- Durante a instalação da atualização 1811, painel do portal do usuário pode não estar disponível e personalizações podem ser perdidas. Você pode restaurar o painel para a configuração padrão após a conclusão da atualização abrindo as configurações do portal e selecionando **restaurar as configurações padrão**.

- Quando você executa [AzureStack teste](azure-stack-diagnostic-test.md), será exibida uma mensagem de aviso do Baseboard Management Controller (BMC). Você pode ignorar com segurança esse aviso.

- <!-- 2468613 - IS --> Durante a instalação dessa atualização, você pode ver os alertas com o título `Error – Template for FaultType UserAccounts.New is missing.` você pode ignorar com segurança esses alertas. Os alertas fecham automaticamente após a instalação dessa atualização.

- <!-- 3139614 | IS --> Se você tiver aplicado uma atualização para o Azure Stack de seu OEM, a **atualização disponível** notificação não pode aparecer no portal do administrador do Azure Stack. Para instalar a Microsoft update, baixe e importe-o manualmente usando as instruções aqui [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Etapas de pós-atualização

- Após a instalação dessa atualização, instale os hotfixes aplicáveis. Para obter mais informações, consulte [Hotfixes](#hotfixes), bem como nosso [política de manutenção](azure-stack-servicing-policy.md).  

- Recuperar os dados em chaves de criptografia do rest e armazene-os com segurança fora da sua implantação do Azure Stack. Siga as [obter instruções sobre como recuperar as chaves](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Ele não está disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

<!-- TBD - IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas de usuário.

<!-- TBD - IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use [PowerShell para verificar permissões](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Monitoramento e integridade

<!-- 1264761 - IS ASDK -->  
- Você pode ver os alertas para o **controlador de integridade** componente que tem os seguintes detalhes:  

  - #1 do alerta:
     - NOME:  Função de infraestrutura não íntegro
     - GRAVIDADE: Aviso
     - COMPONENTE: Controlador de integridade
     - DESCRIÇÃO: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  - #2 do alerta:
     - NOME:  Função de infraestrutura não íntegro
     - GRAVIDADE: Aviso
     - COMPONENTE: Controlador de integridade
     - DESCRIÇÃO: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

    Ambos os alertas podem ser ignorados com segurança. Será fechada automaticamente ao longo do tempo.  

### <a name="compute"></a>Computação

- Ao criar um novo Windows VM (Máquina Virtual), o **configurações** folha requer que você selecione uma porta de entrada pública para continuar. 1811, essa configuração é necessária, mas não tem nenhum efeito. Isso ocorre porque o recurso depende de Firewall do Azure, que não está implementado no Azure Stack. Você pode selecionar **portas de entrada públicas não**, ou qualquer uma das outras opções para prosseguir com a criação da VM. A configuração não terá efeito.

- Ao criar um novo Windows VM (Máquina Virtual), o seguinte erro pode ser exibido:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se você habilitar o diagnóstico de inicialização em uma VM, mas excluir sua conta de armazenamento de diagnóstico de inicialização. Para contornar esse problema, recrie a conta de armazenamento com o mesmo nome que você usou anteriormente.

- Ao criar uma [VM da série Dv2](./user/azure-stack-vm-considerations.md#virtual-machine-sizes), D11 14v2 VMs permitem criar 4, 8, 16 e 32 discos de dados, respectivamente. No entanto, o painel VM de criar mostra 8, 16, 32 e 64 discos de dados.

- Registros de uso no Azure Stack podem conter inesperado de maiusculas e minúsculas; Por exemplo:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   Neste exemplo, o nome do grupo de recursos deve ser **AndrewRG**. Você pode ignorar com segurança essa inconsistência.

<!-- 3235634 – IS, ASDK -->
- Implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v minúsculo). Não use **Standard_A2_V2** (V maiusculo). Isso funciona no Azure global e uma inconsistência no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao usar o [ **Add AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhar com o seguinte erro: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Quando você usa o portal para criar máquinas virtuais (VMs) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. Você pode ignorar com segurança o aviso que diz: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- A experiência de criação de conjunto (VMSS) de escala de máquina virtual oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação, ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que tiver sido baixada antes da implantação no marketplace, o operador.  

<!-- 2724873 - IS --> 
- Ao usar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução com êxito deve concluir a operação. 

<!-- TBD - IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, permita que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

<!-- 3507629 - IS, ASDK --> 
- Discos gerenciados cria dois novos [tipos de cota de computação](azure-stack-quota-types.md#compute-quota-types) para limitar a capacidade máxima de discos gerenciados podem ser provisionados. Por padrão, 2048 GiB é alocado para cada tipo de cota de discos gerenciados. No entanto, você pode encontrar os seguintes problemas:

   - Cotas de criados antes da atualização 1808, a cota de Managed Disks mostra valores 0 no portal do administrador, apesar de 2048 GiB é alocada. Você pode aumentar ou diminuir o valor com base em suas necessidades reais e recentemente defina o valor de cota substitui o padrão de GiB de 2048.
   - Se você atualizar o valor da cota para 0, é equivalente ao valor padrão de 2048 GiB. Como alternativa, defina o valor da cota como 1.

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1811, atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   - Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Selecione **provedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se o **AzureStack-DiskRP-Client** função está listada.
   - Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização SSH habilitada não permitirá que você use as chaves SSH para fazer logon no. Como alternativa, use o acesso de VM para a extensão do Linux para implementar as chaves SSH após o provisionamento ou usar a autenticação baseada em senha.

### <a name="networking"></a>Rede  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são rejeitadas.

<!-- 16309153 - IS ASDK --> 
- Em uma rede virtual que foi criada com uma configuração de servidor DNS de **automática**, alterando para um servidor DNS personalizado falha. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o locatário está acessando máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao gateway de rede local depois que o gateway já foi criado. 

- No portal do Azure Stack, quando você altera um endereço IP estático para uma configuração de IP que está associado a um adaptador de rede anexado a uma instância VM, você verá uma mensagem de aviso dizendo que 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Você pode ignorar com segurança essa mensagem; o endereço IP será alterado, mesmo se a instância de VM não reinicia.

- No portal, sobre o **propriedades de rede** folha há um link para **regras de segurança efetivas** para cada adaptador de rede. Se você selecionar esse link, uma nova folha é aberta mostrando a mensagem de erro `Not Found.` esse erro ocorre porque o Azure Stack não dê suporte ainda **regras de segurança efetivas**.

- No portal, se você adicionar uma regra de segurança de entrada e selecione **marca de serviço** como a origem, várias opções são exibidas na **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são da seguinte maneira:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    Não há suporte para as outras opções como marcas de origem no Azure Stack. Da mesma forma, se você adicionar uma regra de segurança de saída e selecione **marca de serviço** como o destino, a mesma lista de opções para **marca de origem** é exibida. As únicas opções válidas são os mesmos para **marca de origem**, conforme descrito na lista anterior.

- O **New-AzureRmIpSecPolicy** cmdlet do PowerShell não oferece suporte à configuração **DHGroup24** para o `DHGroup` parâmetro.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack no Azure mesma forma como global. No Azure, você pode definir várias portas em uma regra NSG (usando o portal, PowerShell e modelos do Resource Manager). No Azure Stack, é possível definir várias portas em uma regra NSG por meio do portal. Para contornar esse problema, use um modelo do Resource Manager para definir essas regras adicionais.

### <a name="infrastructure-backup"></a>Backup de infraestrutura

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Depois de habilitar os backups automáticos, o serviço de Agendador entra em estado desativado inesperadamente. O serviço de backup do controlador detectará que os backups automáticos são desabilitados e geram um aviso no portal do administrador. Esse aviso é esperado quando backups automáticos são desabilitados. 
    - Causa: Esse problema é devido a um bug no serviço que resulta na perda da configuração do Agendador. Esse bug não altera o local de armazenamento, nome de usuário, senha ou chave de criptografia.   
    - Correção: Para atenuar esse problema, abra a folha de configurações de backup do controlador no provedor de recursos de infraestrutura de Backup e selecione **habilitar Backups automáticos**. Certifique-se de definir o período de retenção e a frequência desejado.
    - Ocorrência: Baixo 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Você deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1811 [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários conectados apenas, as implantações do Azure Stack verificar periodicamente um ponto de extremidade seguro em notificá-lo automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [Gerenciando atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Próximas etapas

- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).  
