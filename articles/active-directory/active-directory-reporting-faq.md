---
title: "Perguntas frequentes sobre relatórios do Azure Active Directory | Microsoft Docs"
description: "Perguntas frequentes sobre relatórios do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: e6dcd3f6f9c7c8765409c3b0d50e4b3843bab5c6
ms.openlocfilehash: e39ee63d190308b87ebeb43adeb8b3e5db86df57
ms.contentlocale: pt-br
ms.lasthandoff: 02/22/2017

---
# Perguntas frequentes sobre relatórios do Azure Active Directory
<a id="azure-active-directory-reporting-faq" class="xliff"></a>

Este artigo inclui respostas para FAQs (perguntas frequentes) sobre os relatórios do Azure Active Directory.  
Para obter mais detalhes, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P: O que é a retenção de dados para logs de atividade (de entradas e de auditoria) no Portal do Azure?** 

**R:** Fornecemos 7 dias de dados para nossos clientes com acesso gratuito e, alternando para uma licença Azure AD Premium 1 ou Premium 2, você pode acessar dados por até 30 dias. Para obter mais detalhes sobre a retenção, consulte [Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md)

--- 

**P: Quanto tempo leva até que eu possa ver os dados de atividade depois que concluir minha tarefa?**

**R:** Logs de atividade de auditoria têm uma latência variando de 15 minutos a uma hora. Logs de atividade de entrada têm uma latência variando de 15 minutos para a maioria dos registros até 2 horas para alguns registros.

---

**P: É necessário ser um administrador global para ver a atividade de logs no Portal do Azure ou para obter dados por meio da API?**

**R:** Não. Você pode ser um **Leitor de segurança**, um **Administrador de segurança** ou um **Administrador Global** para ver dados de relatório no Portal do Azure ou acessando-os por meio da API.

---

**P: Posso obter informações do log de atividades do Office 365 por meio do Portal do Azure?**

**R:** Embora os logs de atividades do Office 365 e de atividades do Azure AD compartilhem muitos dos recursos de diretório, se você quiser uma exibição completa dos logs de atividades do Office 365, vá para o Centro de administração do Office 365 para obter informações de log de atividades do Office 365.

---


**P: Quais APIs devo usar para obter informações sobre logs de atividades do Office 365?**

**R:** Use as APIs de gerenciamento do Office 365 para acessar os [logs de atividades do Office 365 por meio de uma API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: Quantos registros posso baixar do Portal do Azure?**

**R:** Você pode baixar até 120 mil registros do Portal do Azure. Os registros são classificados pelos *mais recentes* e, por padrão, você obterá os 120 mil registros mais recentes. 

---

**P: Quantos registros posso consultar por meio da API de atividades?**

**R:** Você pode consultar até 1 milhão de registros (se você não usar o operador top, que classifica o registro pelo mais recente). Se usar o operador "top", você poderá consultar até 500 mil registros. Você pode encontrar consultas de exemplo sobre como usar a API [aqui](active-directory-reporting-api-getting-started.md).

---

**P: Como obtenho uma licença premium?**

**A:** Consulte [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md) para obter uma resposta para essa pergunta.

---

**P: Em quanto tempo deverei ver dados de atividades depois de obter uma licença premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, você poderá ver os mesmos dados. Se você não tiver nenhum dado, isso levará um ou dois dias.

---

**P: Eu vejo dados do último mês depois de obter uma licença premium do Azure AD?**

**R:**: Se você mudou recentemente para uma versão Premium (incluindo uma versão de avaliação), você poderá inicialmente ver dados de até 7 dias. Quando os dados acumularem, você verá dados de até 30 dias.

 
---


