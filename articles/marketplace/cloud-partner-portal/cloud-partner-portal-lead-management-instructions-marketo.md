---
title: "Instruções de gerenciamento de leads para Marketo no Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece orientações passo a passo aos fornecedores sobre como configurar o gerenciamento de leads com o Marketo."
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
ms.openlocfilehash: 77c9bdf6b2634e222592ac0818f608ee3cb3fe09
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-marketo"></a>Instruções de gerenciamento de leads para Marketo

Este documento fornece instruções sobre como configurar o sistema Marketo para que a Microsoft possa fornecer vendas potenciais. 

1. Vá para "Design Studio" no Marketo. <br/>
![Imagem de configuração do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

2. Clique em “Novo Formulário” <br/>
![Imagem de novo formulário no Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

3. Preencha os campos no pop-up do novo formulário <br/>
![Imagem de criação do formulário no Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4. Clique em "concluir" no formulário seguinte. Não se preocupe com a formatação ou com os campos. <br/>
![Imagem da conclusão do formulário no Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5. Aprovar e fechar.

6. Na próxima página, vá para a seção Código de inserção.<br/>
![Imagem do código de inserção do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7. Você verá um código de inserção semelhante ao exemplo abaixo: 

   ` <script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    `<form id="mktoForm_1179"></form> `

    `<script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>`

8. Use os valores disponíveis no código de inserção para preencher a <b>ID do servidor, ID do Munchkin e ID do formulário</b> nos campos dom Marketo no Portal do Cloud Partner. <br\> <br/>

  O exemplo a seguir o ajudará a determinar as IDs dos dados de exemplo acima. <br\>

  serverId = **ys12** <br\>

 munchkinId = **123-PQR-789** <br\>

 formId = **1179** <br\> <br/>

