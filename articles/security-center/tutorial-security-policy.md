---
title: "Tutorial da Central de Segurança do Azure - Definir e avaliar as políticas de segurança | Microsoft Docs"
description: "Tutorial da Central de Segurança do Azure - Definir e avaliar as políticas de segurança"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: a740cec8a3177e0de5f33b362d5c4956671cd427
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-define-and-assess-security-policies"></a>Tutorial: Definir e avaliar as políticas de segurança
A Central de Segurança ajuda a garantir a conformidade com os requisitos de segurança regulatórios ou corporativos usando-se políticas de segurança para definir a configuração desejada das cargas de trabalho. Depois que você definir as políticas para suas assinaturas do Azure e adaptá-las ao tipo de carga de trabalho ou à confidencialidade dos seus dados, a Central de Segurança pode fornecer recomendações de segurança aos seus recursos de computação, de rede, de SQL e armazenamento e de aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Configurar a política de segurança
> * Avaliar a segurança de seus recursos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos
Para percorrer os recursos abordados neste tutorial, você deve estar em um tipo de preço da Central de Segurança Padrão. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. O início rápido [Integração da sua assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você sobre como fazer upgrade para Standard.

## <a name="configure-security-policy"></a>Configurar a política de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. As políticas de segurança são compostas de recomendações que podem ser ativadas ou desativadas de acordo com os requisitos de segurança da assinatura. Para fazer alterações na política de segurança padrão, você deve ser proprietário, colaborador ou administrador da segurança da assinatura.

1. No menu principal da Central de Segurança, selecione **Política de segurança**. Selecione a assinatura que você quer usar. Em **COMPONENTES DE POLÍTICA**, selecione **Política de segurança**:

  ![Política de segurança](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

2. Para cada configuração de segurança que você deseja monitorar, selecione **Ativado**. A Central de Segurança avaliará continuamente a configuração de seu ambiente e, quando houver vulnerabilidade, gerará uma recomendação de segurança. Selecione **Desativado** se a configuração de segurança não for recomendada nem relevante. Por exemplo, em um ambiente de desenvolvimento/teste, talvez não possa exigir o mesmo nível de segurança que um ambiente de produção. Depois de selecionar as políticas que se aplicam ao seu ambiente, clique em **Salvar**.

Aguarde até que a Central de Segurança processe essas políticas e gere as recomendações. Algumas configurações, como atualizações do sistema e configurações do sistema operacional, podem demorar até 12 horas, enquanto os grupos de segurança de rede e as configurações de criptografia podem ser avaliados quase instantaneamente. Após ver as recomendações no painel da Central de Segurança, poderá continuar para a próxima etapa.

## <a name="assess-security-of-resources"></a>Avaliar a segurança dos recursos
1. De acordo com as políticas de segurança habilitadas, a Central de Segurança fornecerá um conjunto de recomendações de segurança conforme necessário. Você deve começar revisando as recomendações de computadores e de máquina virtual. No painel Central de Segurança, clique em **Visão geral** e em **Computação**.

  ![Computação](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Analise cada recomendação priorizando as recomendações em vermelho (prioridade alta). A correção de algumas dessas recomendações pode ser implementada diretamente na Central de Segurança, como os [problemas de proteção do ponto de extremidade](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Outras recomendações só têm diretrizes para aplicar a correção, como a recomendação de criptografia de disco ausente.

2. Depois de resolver todas as recomendações de computação relevantes, passe para a carga de trabalho seguinte: rede. No painel Central de Segurança, clique em **Visão geral** e em **Rede**.

  ![Rede](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  A página de recomendações de rede tem uma lista de problemas de segurança para a configuração de rede, pontos de extremidade para a Internet e topologia de rede. Assim como a **Computação**, algumas recomendações de rede fornecerão uma solução integrada, mas outras não.

3. Depois de resolver todas as recomendações de rede relevantes, passe para a carga de trabalho seguinte: armazenamento e dados. No painel Central de Segurança, clique em **Visão geral** e em **Armazenamento e dados**.

  ![Recursos de dados](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  A página **Recursos de Dados** contém as recomendações sobre como habilitar a auditoria para os bancos de dados e servidores SQL do Azure, como habilitar a criptografia para bancos de dados SQL e como habilitar a criptografia da conta de armazenamento do Azure. Se você não tiver essas cargas de trabalho, não verá nenhuma recomendação. Assim como a **Computação**, algumas recomendações de SQL e armazenamento fornecerão uma solução integrada, mas outras não.

4. Depois de resolver todas as recomendações de SQL e armazenamento relevantes, passe para a carga de trabalho seguinte: aplicativos. No painel Central de Segurança, clique em **Visão geral** e em **Aplicativos**.

  ![APLICATIVOS](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  A página **Aplicativos** contém recomendações para a implantação de firewall do aplicativo Web e diretrizes gerais para a proteção de aplicativos. Se você não tiver uma máquina virtual ou computadores com aplicativos Web em execução no Serviço de Informações da Internet (IIS), não verá essas recomendações.

## <a name="clean-up-resources"></a>Limpar recursos
Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e os guias de início rápido subsequentes, continue executando a camada Standard e mantenha o provisionamento automático habilitado. Se você não planejar continuar ou quiser retornar para a camada Gratuita:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Selecione a assinatura ou a política que você deseja retornar para Gratuita. A **Política de segurança** abre.
3. Em **COMPONENTES DE POLÍTICA**, selecione **Tipo de preços**.
4. Selecione **Gratuita** para alterar a assinatura da camada Standard para a camada Gratuita.
5. Selecione **Salvar**.

Se quiser desabilitar o provisionamento automático:

1. Retorne ao menu principal da Central de Segurança e selecione **Política de segurança**.
2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Em **Política de segurança – Coleta de dados**, selecione **Desativar** em **Integração** para desabilitar o provisionamento automático.
4. Selecione **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu sobre a definição de política básica e a avaliação de segurança de sua carga de trabalho com a Central de Segurança, por exemplo:

> [!div class="checklist"]
> * Configuração da política de segurança para garantir a conformidade com os requisitos de segurança regulatórios ou corporativos
> * Avaliação de segurança para seus recursos de computação, rede, de SQL e armazenamento e de aplicativo

Vá ao próximo tutorial para aprender a usar a Central de Segurança para proteger seus recursos.

> [!div class="nextstepaction"]
> [Proteger seus recursos](tutorial-protect-resources.md)
