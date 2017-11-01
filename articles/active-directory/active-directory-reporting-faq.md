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
ms.date: 10/12/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: faee3bc9b0b1a10a48a514d830af5045cb047e02
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Perguntas frequentes sobre relatórios do Azure Active Directory

Este artigo inclui respostas a perguntas frequentes sobre os relatórios do Azure AD (Azure Active Directory). Para saber mais, consulte [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P: estou usando as APIs do ponto de extremidade https://graph.windows.net/&lt;tenant-name&gt;/reports/ para obter relatórios de uso de aplicativo integrado e auditoria do Azure AD para os sistemas relatórios programaticamente. Para qual devo mudar?**

**R:** Pesquise em nossa [documentação de referência de API](https://developer.microsoft.com/graph/) para ver como você pode usar as novas APIs para acessar [relatórios de atividade](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Esse ponto de extremidade tem dois relatórios (Auditoria e Entradas) que fornecem todos os dados que você obteve no ponto de extremidade de API antigo. Esse novo ponto de extremidade também tem um relatório de entradas com a licença do Azure AD Premium que você pode usar para obter informações de uso de aplicativo uso de dispositivo e conexão do usuário.


--- 

**P: Estou usando as APIs de ponto de extremidade https://graph.windows.net/&lt;tenant-name&gt;/reports/ para receber relatórios de segurança do Azure AD (tipos específicos de detecções, como credenciais vazadas ou entradas de endereços IP anônimos) em nossos sistemas de relatório de maneira programática. Para qual devo mudar?**

**R:** Você pode usar a [API de eventos de risco de Proteção de Identidade](active-directory-identityprotection-graph-getting-started.md) para acessar detecções de segurança por meio do Microsoft Graph. Esse novo formato oferece maior flexibilidade ao modo de consultar os dados, com filtragem avançada, seleção de campo e mais, além de padronizar eventos de risco em um tipo para integração mais fácil ao SIEMs e outras ferramentas de coleta de dados. Uma vez que os dados estão em um formato diferente, você não pode substituir uma nova consulta para suas consultas antigas. No entanto, [a nova API usa o Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que é o padrão da Microsoft para essas APIs, como O365 ou Azure AD. Para que o trabalho necessário possa estender seus investimentos atuais no MS Graph ou ajudá-lo a começar a fazer a transição para essa nova plataforma padrão.

--- 

**P: O que é a retenção de dados para logs de atividade (de entradas e de auditoria) no Portal do Azure?** 

**R:** Fornecemos sete dias de dados para nossos clientes com gratuitos ou você pode acessar dados por até 30 dias comprando uma licença do Azure AD Premium 1 ou Premium 2. Para saber mais sobre retenção de relatórios, confira [Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md).

--- 

**P: Quanto tempo leva até que eu possa ver os dados de atividade depois que concluir minha tarefa?**

**R:** Logs de atividade de auditoria têm uma latência que varia de 15 minutos a uma hora. Logs de atividade de conexão podem levar de 15 minutos a até 2 horas para alguns registros.

---

**P: É necessário ser um administrador global para ver a atividade de entradas no portal do Azure ou para obter dados por meio da API?**

**R:** Não. Você deve ser um **Leitor de Segurança**, um **Administrador de Segurança** ou um **Administrador Global** para obter dados no portal do Azure ou por meio da API.

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

**R:** Se você mudou recentemente para uma versão Premium (incluindo uma versão de avaliação), você poderá inicialmente ver dados de até 7 dias. Quando os dados acumularem, você verá dados de até 30 dias.

---

**P: Há um evento de risco no Identity Protection, mas não estou vendo a entrada correspondente em todas as entradas. Isso é esperado?**

**R:** Sim, o Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de todas as entradas mostra apenas as entradas interativas.

---

**P: Como posso baixar o relatório "Usuários sinalizados para risco" no portal do Azure?**

**R:**: A opção de baixar o relatório *Usuários sinalizados para risco* será adicionada em breve.

---

**P: Como posso saber o motivo pelo qual uma entrada ou um usuário foi sinalizado como arriscado no Portal do Azure?**

**R:** Clientes do Premium Edition podem saber mais sobre os eventos de risco subjacentes clicando no usuário em "Usuários sinalizados para risco" ou clicando nas "Entradas arriscadas". Clientes das edições Gratuita e Basic podem ver as entradas e os usuários em risco sem as informações de evento de risco subjacente.

---

**P: Como os endereços IP são calculados no relatório de entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. Isso é complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais, frequentemente muito distantes do local no qual o dispositivo do cliente de fato é usado. Devido a esses fatores, a conversão de endereços IP em um local físico é um esforço baseado em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---
