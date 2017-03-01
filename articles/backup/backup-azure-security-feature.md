---
title: "Recursos de segurança para proteger backups híbrida usando o Backup do Azure | Microsoft Docs"
description: "Saiba como usar os recursos de segurança no Backup do Azure para tornar os backups mais seguros"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 5c49de40401235d38142efd60d22b0591752fe75
ms.openlocfilehash: f17802bf455b82f0b5239356c35024ecde7f1f35
ms.lasthandoff: 02/17/2017


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Recursos de segurança para proteger backups híbridos usando o Backup do Azure
Mais e mais clientes são atingidos por problemas de segurança, como malware, ransomware, intrusão etc. Esses problemas de segurança resultam em perda de dados e o custo por violação de segurança tem aumentado como nunca. Para se proteger contra esses ataques, o Backup do Azure agora fornece recursos de segurança para proteger os backups híbridos. Este artigo fala sobre como habilitar e aproveitar esses recursos usando o Microsoft Azure Recovery Services Agent e o Servidor de Backup do Microsoft Azure. Esses recursos foram criados sobre três pilares de segurança:

1. **Prevenção** - uma camada adicional de autenticação será adicionada sempre que uma operação crítica como a alteração de senha for executada. Essa validação é garantir que essas operações possam ser realizadas apenas por usuários com credenciais do Azure válidas.
2. **Alerta** - uma notificação por email é enviada para o administrador da assinatura sempre que uma operação crítica como Excluir dados do Backup é executada. Este email garante que o usuário é notificado em tempo hábil sobre tais ações.
3. **Recuperação** - os dados de backup excluídos são retidos por 14 dias adicionais desde a data de exclusão. Isso garante a capacidade de recuperação de dados em um determinado período de tempo de forma que não haja perda de dados mesmo se houver um ataque. Além disso, mais pontos de recuperação mínimos são mantidos para proteção contra dados corrompidos.

> [!NOTE]
> Os recursos de segurança só deverão ser habilitados se você estiver usando: <br/>
> * **Agente MAB** -versão mínima do agente 2.0.9052. Depois de habilitar esses recursos, você deverá atualizar para esta versão do agente para realizar operações críticas como Alterar Senha, Parar backup e Excluir dados. <br/>
> * **Servidor de Backup do Azure** – versão mínima do agente MAB 2.0.9052 com o servidor de Backup do Azure atualização 1. <br/>
> * **DPM** – versão mínima do agente MAB 2.0.9052 com o DPM 2012 R2 UR12 ou DPM 2016 UR2. <br/>
> * **Backup da VM IaaS** - não habilite esses recursos para o Backup da VM IaaS. Esses recursos ainda não estão disponíveis para o backup da VM IaaS e, portanto, habilitá-los não terá qualquer impacto no backup da VM IaaS.
> * Esses recursos estão disponíveis somente para o cofre dos Serviços de Recuperação.
> * Todos os cofres dos Serviços de Recuperação recém-criados têm esses recursos habilitados por padrão. Para cofres dos Serviços de Recuperação existentes, os usuários precisam habilitar esses recursos usando as etapas mencionadas na seção a seguir.
> * Uma vez habilitada, você obtém os Recursos de Segurança para todos os computadores MARS (Agente de Serviços de Recuperação do Azure), Servidores de Backup do Azure e servidores DPM registrados com o cofre. <br/>
> * Habilitar essa configuração é uma ação única e você não poderá desabilitar esses recursos depois de habilitá-los. <br/>
>
>

## <a name="enabling-security-features"></a>Habilitando recursos de segurança
Os usuários que criarem um cofre dos serviços de recuperação poderiam avaliar todos os Recursos de Segurança. Para o cofre dos serviços de recuperação existentes, as etapas a seguir deverão ser usadas para habilitar estes recursos:

1. Fazer logon no portal do Azure usando credenciais do Azure
2. Digite Serviços de Recuperação no menu Hub para navegar até a lista de serviços de recuperação.

    ![Criar Cofre de Serviços de Recuperação - etapa 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A lista de cofres de Serviços de Recuperação aparecerá. Nesta lista, selecione um cofre.

    O painel de cofres selecionados será aberto.
3. Na lista de itens que aparece sob o cofre, clique em **Propriedades** em **Configurações**.

    ![Abrir propriedades do cofre](./media/backup-azure-security-feature/vault-list-properties.png)
4. Clique em **Atualizar** em **Configurações de Segurança**.

    ![Abrir configurações de segurança](./media/backup-azure-security-feature/security-settings-update.png)

    O link Atualizar abre a folha Configurações de Segurança, o que permite a você Habilitar estes recursos e oferece um resumo do recurso.
5. Selecione um valor na lista suspensa **Você configurou a Autenticação Multifator do Azure?** para confirmar se você habilitou a [Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication.md). Se estiver habilitada, você deverá autenticar de outro dispositivo (por exemplo, telefone celular) ao fazer logon no portal do Azure.

   Como parte dos Recursos de Segurança, quando operações críticas forem executadas no Backup do Azure, você precisará inserir o PIN de Segurança disponível no portal do Azure. Habilitar a Autenticação Multifator Azure adiciona uma camada de segurança, garantindo que somente os usuários autorizados com credenciais válidas do Azure e autenticados de um segundo dispositivo possam acessar o portal do Azure e executem tais operações críticas.
6. Use o botão de alternância para **Habilitar** e clique no botão **Salvar** na parte superior para salvar as Configurações de Segurança, conforme mostrado na figura. Você só poderá selecionar **Habilitar** depois de selecionar um valor da lista suspensa "Você configurou a Autenticação Multifator do Azure?" .

    ![Habilitar configurações de segurança](./media/backup-azure-security-feature/enable-security-settings.png)

## <a name="recovering-deleted-backup-data"></a>Recuperação de dados de backup excluídos
Como uma medida de segurança, o Backup do Azure retém os dados de backup excluídos por mais de 14 dias e não os exclui imediatamente se Parar backup coma operação excluir dados de backup for executado. Para restaurar esses dados no período de 14 dias, use as seguintes etapas:

Para usuários do **MARS (Agente de Serviços de Recuperação da Microsoft)**:

1. Se o computador onde os backups estavam acontecendo ainda estiver disponível, use [Recuperar dados para o mesmo computador](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) em MARS para recuperar todos os pontos de recuperação antigos.
2. Se computador mencionado acima não estiver disponível, use [Recuperar em um computador alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) para usar outro computador MARS para obter esses dados.

Para usuários do **Servidor de Backup do Azure**:

1. Se o servidor onde os backups estavam acontecendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso de recuperar dados para recuperar todos os pontos de recuperação antigos.
2. Se o computador mencionado acima não estiver disponível, use [Recuperar dados de outro Servidor de Backup do Azure](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) para usar outro Servidor de Backup do Azure para obter esses dados.

Para usuários do **Data Protection Manager (DPM)**:

1. Se o servidor onde os backups estavam acontecendo ainda estiver disponível, proteja novamente as fontes de dados excluídas e use o recurso de recuperar dados para recuperar todos os pontos de recuperação antigos.
2. Se computador mencionado acima não estiver disponível, use [Adicionar DPM Externo](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) para usar outro Servidor DPM para obter esses dados.

## <a name="preventing-attacks"></a>Prevenção de ataques
Como parte desse recurso, foram adicionadas verificações para garantir que somente os usuários válidos possam executar várias operações.

### <a name="authentication-to-perform-critical-operations"></a>Autenticação para realizar operações críticas
Como parte da adição de uma camada extra de autenticação para operações críticas, você deveria inserir o PIN de Segurança ao executar Parar Proteção com as operações Excluir dados e Alterar Senha.

Para receber o PIN de Segurança, use estas etapas:

1. Faça logon no portal do Azure.
2. Navegue até o cofre do serviço de recuperação > Configurações > Propriedades.
3. Clique em **Gerar** em PIN de Segurança. Gerar o link abre uma folha, que contém o PIN de Segurança a ser inserido na interface do usuário do Agente de Serviços de Recuperação do Azure.
    Esse PIN é válido somente por 5 minutos e é gerado automaticamente após esse período.

### <a name="maintaining-minimum-retention-range"></a>Manter o intervalo de retenção mínimo
Para garantir que sempre haja um número válido de pontos de recuperação disponíveis, as verificações a seguir foram adicionadas:

1. Para a retenção diária, devem ser no mínimo **sete** dias de retenção
2. Para a retenção semanal, devem ser no mínimo **quatro** semanas de retenção
3. Para a retenção mensal, devem ser no mínimo **três** meses de retenção
4. Para a retenção anual, deve ser no mínimo **um** ano de retenção

## <a name="notifications-for-critical-operations"></a>Notificações para operações críticas
Sempre que algumas operações críticas forem executadas, o administrador de assinatura receberá uma notificação por email com detalhes sobre a operação. Se você quiser configurar ids adicionais de email para receber notificações por email, poderá usar o portal do Azure para configurá-las.

Os recursos de Segurança mencionados neste artigo fornecem mecanismos de defesa contra ataques direcionados, impedindo que os invasores toquem nos backups. Mais importante, esses recursos oferecem a capacidade de recuperar dados caso ocorra um ataque.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao cofre dos Serviços de Recuperação do Azure](backup-azure-vms-first-look-arm.md) para habilitar esses recursos
* [Baixe o agente dos Serviços de Recuperação do Azure mais recente](http://aka.ms/azurebackup_agent) para proteger computadores Windows e proteger seus dados de backup contra ataques
* [Baixe o Servidor de Backup do Azure mais recente](https://aka.ms/latest_azurebackupserver) para proteger as cargas de trabalho e proteger seus dados de backup contra ataques
* [Baixar UR12 para o System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) ou [Baixar UR2 para System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) para proteger as cargas de trabalho e seus dados de backup contra ataques

