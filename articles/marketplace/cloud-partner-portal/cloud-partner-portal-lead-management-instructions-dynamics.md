---
title: "Instruções de gerenciamento de leads para Dynamics CRM online no Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece orientações passo a passo aos fornecedores sobre como configurar o gerenciamento de leads com o Dynamics CRM online."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 05b076bf173d0fbff4baaf44c57ce215dbf23979
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-dynamics-crm-online"></a>Gerar instruções de gerenciamento do Dynamics CRM online

Este documento fornece instruções sobre como configurar o sistema Dynamics CRM Online para que a Microsoft possa fornecer vendas potenciais.

> [!NOTE]
> **Permissões de usuário necessárias para executar as etapas a seguir:** você precisa ser um administrador na instância do Dynamics CRM Online para instalar uma solução e um administrador de locatários para criar uma nova conta de serviço para o serviço de lead.

**Instalando a solução:** <br/>
1. Baixe a [solução Microsoft Marketplace Lead Writer](https://testdriveaccount.blob.core.windows.net/testdrivecon/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salve-a localmente.

2. Vá para as configurações do Dynamics CRM online.  <br/>
(Caso você não veja as opções como na imagem abaixo é porque não tem as permissões necessárias) <br/>
![Imagem de configuração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.    Clique em Importar e selecione a solução que você baixou na etapa 1.  <br/>
![Imagem de importação do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.    Conclua a instalação da solução.

**Permissões de usuário:** <br/>
Você precisa compartilhar conosco informações da conta de serviço para que seja possível autorizar a gravação de leads na instância do Dynamics CRM. As etapas a seguir explicam como criar a conta de serviço e as permissões a atribuir. <br/>
1.    Vá para o [Portal de administração do Microsoft Office 365](https://go.microsoft.com/fwlink/?LinkId=225975).

2.    Clique no bloco "Admin": <br/>
![Imagem de configuração de administração do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3.    Clique em “Adicionar usuário”.<br/>
![Imagem de adição de usuário do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4.    Crie um novo usuário para o serviço de gravação do lead. Certifique-se de seguir 
*  Forneça uma senha e desmarque a opção “Trocar senha do usuário na primeira conexão”
*     Selecione a função “Usuário (sem acesso de administrador)”
*     Selecione a licença do produto conforme mostrado abaixo. (Você será cobrado pela licença que selecionar; a solução funcionará com a licença do Dynamics CRM Online Basic também) <br/>
![Imagem de adição de usuário do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

5.  Vá para o Dynamics CRM online e acesse Configurações-> Segurança.<br/>
![Imagem das configurações de segurança do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

6.  Selecione o usuário que você criou na Etapa 4, clique em Gerenciar Funções na faixa de opções superior e atribua a função como Gravador de Lead do Microsoft Marketplace mostrada abaixo: <br/>
![Imagem do gerenciamento de funções do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)<br/>

Essa função é criada pela solução que você importou e tem permissões para gravar apenas os leads e saber a versão da solução para garantir compatibilidade.

7.  Para o Dynamics 365 8.2, permissões adicionais são necessárias para a função de Gravador de Lead do Microsoft Marketplace criada pela solução que você importou. Enquanto estiver na Segurança, selecione Funções de Segurança e localize a função Gravador de Lead do Microsoft Marketplace, escolha essa função e altere a configuração de criar/ler/gravar para as Configurações de Interface do Usuário da Entidade do Usuário na guia Registros Principais mostrada abaixo: <br/>
![Imagem das funções de segurança do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)<br/>
![Imagem das funções de gerenciamento de segurança do Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)<br/>


Por fim, conecte suas informações de acesso aos campos **Url**, **Nome de Usuário** e **Senha** no Portal do Cloud Partner.
