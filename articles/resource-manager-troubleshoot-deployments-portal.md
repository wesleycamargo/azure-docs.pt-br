<properties
   pageTitle="Exibir operações de implantação com o portal | Microsoft Azure"
   description="Descreve como usar o portal do Azure para detectar e corrigir problemas com a implantação do Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/15/2016"
   ms.author="tomfitz"/>

# Exibir operações de implantação com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Você pode exibir as operações para uma implantação por meio do portal do Azure. Você pode estar mais interessado em ver as operações quando recebeu um erro durante a implantação para que este artigo foque em exibir as operações que falharam. O portal fornece uma interface que permite encontrar facilmente os erros e determinar as possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Usar operações de implantação para solucionar problemas

Para ver as operações de implantação, use as etapas a seguir:

1. Para o grupo de recursos envolvido na implantação, observe o status da última implantação. Selecione esse status para obter mais detalhes.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Você verá o histórico recente da implantação. Selecione a implantação que falhou.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Selecione **Falhou. Clique aqui para obter detalhes** para ver uma descrição do motivo da implantação ter falhado. Na imagem abaixo, o registro DNS não é exclusivo.

    ![exibir implantação com falha](./media/resource-manager-troubleshoot-deployments-portal/view-error.png)

    Essa mensagem de erro deve ser suficiente para você começar a solucionar o problema. No entanto, se você precisar de mais detalhes sobre quais tarefas foram concluídas, poderá exibir as operações, como mostrado nas etapas a seguir.

4. Você pode exibir todas as operações de implantação na folha **Implantação**. Selecione qualquer operação para ver mais detalhes.

    ![exibir operações](./media/resource-manager-troubleshoot-deployments-portal/view-operations.png)

    Neste caso, verá que a conta de armazenamento, a rede virtual e o conjunto de disponibilidades foram criados com êxito. O endereço IP público falhou e outros recursos não foram tentados.

5. Você pode exibir os eventos para a implantação selecionando **Eventos**.

    ![exibir eventos](./media/resource-manager-troubleshoot-deployments-portal/view-events.png)

6. Você verá todos os eventos para a implantação e selecione qualquer um para obter mais detalhes.

    ![consultar eventos](./media/resource-manager-troubleshoot-deployments-portal/see-all-events.png)

## Usar os logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver os erros de uma implantação, use as seguintes etapas:

1. Exiba os logs de auditoria para um grupo de recursos selecionando **Logs de Auditoria**.

    ![selecionar logs de auditoria](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Na folha **Logs de Auditoria**, você verá um resumo das operações recentes de todos os grupos de recursos em sua assinatura. Ele incluirá uma representação gráfica do tempo e do status das operações, bem como uma lista das operações.

    ![mostrar ações](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Você pode filtrar a exibição dos logs de auditoria para se concentrar em determinadas condições. Selecione **Filtrar**, na parte superior da folha **Logs de auditoria**.

    ![filtrar logs](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

4. Na folha **Filtro**, selecione as condições para restringir a exibição dos logs de auditoria somente às operações que você deseja ver. Por exemplo, você pode filtrar as operações para exibir somente os erros do grupo de recursos.

    ![definir opções de filtragem](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

5. Você pode filtrar ainda mais as operações, definindo um intervalo de tempo. A imagem a seguir filtra a exibição para um período específico de 20 minutos.

    ![definir tempo](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

6. Você pode escolher qualquer uma das operações na lista. Escolha a operação que contém o erro que você deseja pesquisar.

    ![selecionar operação](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
7. Você verá todos os eventos para essa operação. Observe as **IDs de Correlação** no resumo. Essa ID é usada para monitorar eventos relacionados. Ela pode ser útil ao trabalhar com o suporte técnico para solucionar um problema. Você pode selecionar qualquer evento para ver os detalhes sobre o evento.

    ![selecionar evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

8. Você verá detalhes sobre o evento. Preste atenção, especificamente, nas **Propriedades** para saber mais sobre o erro.

    ![mostrar detalhes do log de auditoria](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

O filtro aplicado no log de auditoria será mantido na próxima vez que o exibir, portanto, você pode precisar alterar esses valores para ampliar a exibição das operações.

## Próximas etapas

- Para obter ajuda com a resolução de determinados erros de implantação, consulte [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, consulte [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, consulte [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->