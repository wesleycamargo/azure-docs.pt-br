---
title: "Início rápido da Central de Segurança do Azure - Conectar soluções de segurança | Microsoft Docs"
description: "Início rápido da Central de Segurança do Azure - Conectar soluções de segurança"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3263bb3d-befc-428c-9f80-53de65761697
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: yurid
ms.custom: mvc
ms.openlocfilehash: 2ea4dc75c6285379d7a7eb3e85d28c89ae520dc8
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-connect-security-solutions-to-security-center"></a>Início rápido: Conectar soluções de segurança à Central de Segurança

Além de coletar dados de segurança de seus computadores, você pode integrar dados de segurança de uma variedade de outras soluções de segurança, incluindo as que dão suporte ao CEF (Formato Comum de Evento). O CEF é um formato padrão da indústria, além das mensagens Syslog, usado por muitos fornecedores de segurança para permitir a integração de eventos entre diferentes plataformas.

Este guia de início rápido mostra como:
- Conectar uma solução de segurança à Central de Segurança usando os logs do CEF
- Validar a conexão com a solução de segurança

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).

Para percorrer este guia de início rápido, você deverá estar no tipo de preço Standard da Central de Segurança. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. O início rápido [Integração da sua assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você sobre como fazer upgrade para Standard.

Você também precisa de um [computador Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux) com o serviço Syslog que já esteja conectado à Central de Segurança.

## <a name="connect-solution-using-cef"></a>Conectar a solução usando CEF

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

    ![Selecionar central de segurança](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. No menu principal da Central de Segurança, selecione **Soluções de Segurança**.
4. Na página Soluções de Segurança, em **Adicionar fontes de dados (3)**, clique em **Adicionar** em **Formato Comum de Evento**.

    ![Adicionar fonte de dados](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Na página Logs do Formato Comum de Evento, expanda a segunda etapa, **Configurar o encaminhamento do Syslog para enviar os logs necessários para o agente na porta 25226 UDP** e siga as instruções abaixo no seu computador Linux:

    ![Configurar syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Expanda a terceira etapa, **Posicione o arquivo de configuração do agente no computador do agente**e siga as instruções abaixo no seu computador Linux:

    ![Configuração do agente](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Expanda a quarta etapa, **Reiniciar o daemon e o agente do syslog**e siga as instruções abaixo no seu computador Linux:

    ![Reiniciar o syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


## <a name="validate-the-connection"></a>Validar a conexão

Antes de prosseguir com as etapas abaixo, você precisa esperar até que o syslog inicie a geração de relatórios para a Central de Segurança. Isso pode levar algum tempo, e vai variar de acordo com o tamanho do ambiente.

1.  No painel esquerdo do painel da Central de Segurança, clique em **Pesquisar**.
2.  Selecione o espaço de trabalho ao qual o Syslog (computador Linux) está conectado.
3.  Digite *CommonSecurityLog* e clique no botão **Pesquisar**.

O exemplo a seguir mostra o resultado destas etapas: ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

## <a name="clean-up-resources"></a>Limpar recursos
Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e os guias de início rápido subsequentes, continue executando a camada Standard e mantenha o provisionamento automático habilitado. Se você não planejar continuar ou quiser retornar para a camada Gratuita:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Selecione a assinatura ou a política que você deseja retornar para Gratuita. A **Política de segurança** abre.
3. Em **COMPONENTES DE POLÍTICA**, selecione **Tipo de preços**.
4. Selecione **Gratuita** para alterar a assinatura da camada Padrão para a camada Gratuita.
5. Selecione **Salvar**.

Se quiser desabilitar o provisionamento automático:

1. Retorne ao menu principal da Central de Segurança e selecione a **Política de segurança**.
2. Selecione a assinatura em que você deseja desabilitar o provisionamento automático.
3. Em **Política de segurança – Coleta de dados**, selecione **Desativar** em **Integração** para desabilitar o provisionamento automático.
4. Selecione **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure em que o agente tenha sido provisionado. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Este guia de início rápido, você aprendeu a conectar uma solução Syslog do Linux à Central de Segurança usando CEF. Ao conectar seus logs do CEF à Central de Segurança, você poderá tirar proveito de regras de alerta personalizadas e de pesquisa, e do enriquecimento da inteligência de ameaça para cada log. Para saber mais sobre como usar a Central de Segurança, prossiga para o tutorial para configurar uma política de segurança e avaliar a segurança de seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar as políticas de segurança](./tutorial-security-policy.md)
