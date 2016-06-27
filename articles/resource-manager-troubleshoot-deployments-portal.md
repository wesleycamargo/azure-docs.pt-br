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
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# Exibir operações de implantação com o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [CLI do Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se você recebeu um erro durante a implantação de recursos do Azure, você talvez queira ver mais detalhes sobre as operações de implantação que foram executadas. O Portal do Azure fornece uma interface que permite a fácil localização dos erros e determinar as possíveis correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Usar os logs de auditoria para solucionar problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver os erros de uma implantação, use as etapas a seguir:

1. Exiba os logs de auditoria no portal escolhendo **Procurar** e **Logs de Auditoria**.

    ![selecionar logs de auditoria](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. Na folha **Logs de Auditoria**, você verá um resumo das operações recentes para todos os grupos de recursos em sua assinatura. Ele incluirá uma representação gráfica do tempo e do status das operações, bem como uma lista das operações.

    ![mostrar ações](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. Você pode escolher qualquer uma das operações na lista. Escolha a operação que contém o erro que você deseja pesquisar.

    ![selecionar operação](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. Você verá todos os eventos para essa operação. Observe as **IDS de Correlação** no resumo. Essa ID é usada para monitorar eventos relacionados. Ela pode ser útil ao trabalhar com o suporte técnico para solucionar um problema. Você pode selecionar qualquer evento para ver os detalhes sobre o evento.

    ![selecionar evento](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. Você verá detalhes sobre o evento. Preste atenção, especificamente, às **Propriedades** para saber mais sobre o erro.

    ![mostrar detalhes do log de auditoria](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

Você pode filtrar a exibição dos logs de auditoria para se concentrar em determinadas condições. Para personalizar a exibição do log de auditoria:

1. Escolha **Filtrar**, na parte superior da folha **Logs de auditoria**.

    ![filtrar logs](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. Na folha **Filtro**, selecione condições para restringir a exibição dos logs de auditoria somente às operações que você deseja ver. Por exemplo, você pode filtrar as operações para exibir somente erros de um determinado grupo de recursos.

    ![definir opções de filtragem](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. Você pode filtrar ainda mais as operações, definindo um intervalo de tempo. A imagem a seguir filtra a exibição para um período específico de 20 minutos.

    ![definir tempo](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

Depois de atualizar a exibição dos logs de auditoria, você verá somente as operações que atendem à condição especificada. Essas configurações serão mantidas na próxima vez que você exibir os logs de auditoria. Portanto, talvez seja necessário alterar esses valores para ampliar a exibição das operações.

Esperamos que você tenha conseguido descobrir porque a implantação falhou. Você também pode examinar as operações de implantação para saber mais sobre o status, conforme mostra a próxima seção.

## Usar operações de implantação para solucionar problemas

Para ver as operações de implantação, use as etapas a seguir:

1. Para o grupo de recursos envolvido na implantação, observe o status da última implantação. Selecione esse status para obter mais detalhes.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. Você verá o histórico recente da implantação. Selecione a implantação que falhou.

    ![status da implantação](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. Exiba as informações sobre a implantação e selecione a operação que falhou para ver detalhes sobre o erro.

    ![exibir implantação com falha](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. Na folha **Detalhes das operações**, você verá informações sobre a operação que falhou. Preste atenção especial à mensagem de status.

    ![exibir mensagem de status](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## Próximas etapas

- Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para saber mais sobre como usar os logs de auditoria para monitorar outros tipos de ações, veja [Auditar operações com o Gerenciador de Recursos](resource-group-audit.md).
- Para validar sua implantação antes de executá-la, veja [Implantar um grupo de recursos com um modelo do Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0615_2016-->