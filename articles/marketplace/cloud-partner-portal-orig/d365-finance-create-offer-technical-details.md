---
title: Como preencher o formulário de Informações Técnicas | Microsoft Docs
description: Explica como inserir valores para no formulário de Informações Técnicas para um novo aplicativo do Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cb8e5074d0cd99f5c7cb2130e6f9181eb5382015
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805050"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Como preencher o formulário de informações técnicas
===========================================

1.  Na seção **Escolher Tipo de Aplicativo**, carregue o arquivo de pacote de extensão (.app) e qualquer arquivo de pacote de extensão do qual sua extensão tenha dependências.

    ![Informações do pacote de aplicativo](./media/d365-financials/image015.png)

-   **Arquivo de Pacote de Extensões** – obrigatório – o arquivo de pacote de extensão (.app).

-   **Arquivo de pacote de dependência** – obrigatório se o aplicativo tiver uma dependência em outro aplicativo publicado no AppSource. Esse arquivo .app de uma extensão já publicado no AppSource, do qual aplicativo atual depende. 

-   **Arquivo de Pacote de Biblioteca** – obrigatório se o aplicativo tiver uma dependência de outro aplicativo *não* publicado no AppSource. Esse arquivo .app de um aplicativo existente, mas um que não tenha sido e não vá ser publicado no AppSource.

-   **Automação de Teste do Aplicativo** – obrigatório – o pacote de teste em VS Code que você deve criar para automação de testes de extensões.

2.  Na seção **Informações adicionais para a extensão**, carregue informações adicionais para a sua extensão. Essas informações são usadas durante a validação.

    ![Informações adicionais para o formulário de extensão de aplicativo](./media/d365-financials/image016.png)


-   **URL para a Documentação do Produto** – obrigatório – URL para a documentação para a extensão.

-   **Cenários de Uso de Chave** – obrigatório – um documento que lista os detalhes uso e configuração passo a passo para a extensão. Um exemplo pode ser encontrado no artigo [Documentação do Cenário do Usuário](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Versão de destino** – obrigatório – selecione a versão na qual deseja implantar o aplicativo. Selecione **atual** para implantar na atual versão de mercado. Selecione **próxima secundária** para implantar com a próxima versão secundária a ser liberada. Selecione **próxima principal** para implantar com a próxima versão principal a ser liberada.

-   **Requer SKU Premium** – opcional – selecione o botão Premium se o aplicativo exigir a SKU Premium. Gerenciamento de Serviços e Fabricação estão disponíveis apenas no premium. Informações detalhadas sobre Essential vs. Premium pode ser encontrado no artigo [Alterar Quais Recursos são Exibidos](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Explicação para Erros de Análise de Código** – opcional – documento que lista e justifica qualquer código que não atenda aos requisitos.

-   **Explicação da Funcionalidade Básica Afetada** – opcional – documento que lista e explica qualquer funcionalidade básica limitada pela extensão.

-   **Contas de Teste** – opcional – contas de usuário para serviços remotos, sites etc. que serão necessárias para concluir o teste de uso de ponta a ponta.

-   **Exceções de requisitos de UX** – opcional – documento que lista e justifica quaisquer requisitos de experiência do usuário não atendidos pela extensão.

A próxima etapa é adicionar os detalhes da vitrine de sua oferta.
