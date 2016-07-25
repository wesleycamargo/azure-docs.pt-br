<properties
   pageTitle="Aplicar atualizações do sistema na Central de Segurança do Azure | Microsoft Azure"
   description="Este documento mostra como implementar as recomendações da Central de Segurança do Azure **Aplicar atualizações do sistema** e **Reinicializar após as atualizações do sistema**."
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
   ms.date="07/12/2016"
   ms.author="terrylan"/>

# Aplicar atualizações do sistema na Central de Segurança do Azure

A Central de Segurança do Azure monitora diariamente VMs (máquinas virtuais) Windows e Linux para saber se faltam atualizações do sistema operacional. A Central de Segurança recupera uma lista de atualizações críticas e de segurança disponíveis no Windows Update ou no WSUS (Windows Server Update Services), dependendo de qual serviço está configurado em uma VM do Windows. A Central de Segurança também verifica as atualizações mais recentes em sistemas Linux. Se faltar uma atualização do sistema em sua VM, a Central de Segurança recomendará que você aplique as atualizações do sistema

> [AZURE.NOTE] As informações neste documento se aplicam à versão de visualização da Central de Segurança do Azure. Este documento apresenta o serviço usando uma implantação de exemplo. Ela não é um guia passo a passo.

## Implementar a recomendação

1. Na folha **Recomendações**, selecione **Aplicar atualizações do sistema**. ![Aplicar atualizações do sistema][1]

2. A folha **Aplicar atualizações do sistema** abre exibindo uma lista de VMs sem atualizações de sistema. Selecione uma VM. ![Selecionar uma máquina virtual][2]

3. Uma folha abrirá exibindo uma lista de atualizações ausentes para a VM. Selecione uma atualização do sistema. Neste exemplo, vamos selecionar KB3156016. ![Atualizações de segurança ausentes][3]

4. Siga as etapas na folha **Atualização de segurança** para aplicar a atualização ausente. ![Atualização de segurança][4]

## Reinicializar após as atualizações do sistema

5. Volte para a folha **Recomendações**. Uma nova entrada foi gerada depois que você aplicou atualizações do sistema, chamada **Reinicializar após as atualizações do sistema**. Essa entrada permite que você saiba que é necessário reinicializar a máquina virtual para concluir o processo de aplicação de atualizações de sistema. ![Reinicializar após as atualizações do sistema][5]

6. Selecione **Reinicializar após as atualizações do sistema**. Isso abre a folha **Uma reinicialização está pendente para concluir as atualizações do sistema** exibindo uma lista de máquinas virtuais que você precisa reiniciar para concluir o processo de aplicar atualizações d sistema. ![Reinicialização pendente][6]

Reinicie a VM do Azure para concluir o processo.

## Próximas etapas

Para saber mais sobre a Central de Segurança, confira o seguinte:

- [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger os recursos do Azure.
- [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
- [Gerenciamento e resposta a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
- [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.
- [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]: ./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

<!---HONumber=AcomDC_0713_2016-->