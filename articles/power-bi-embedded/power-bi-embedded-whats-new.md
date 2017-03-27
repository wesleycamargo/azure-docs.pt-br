---
title: Novidades no Power BI Embedded
description: Obter as novidades mais recentes sobre o Power BI Embedded
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 07c53a5d6b1881a4c207a2aefed9fcede0fa069e
ms.lasthandoff: 03/14/2017

---
# <a name="whats-new-in-power-bi-embedded"></a>Novidades no Power BI Embedded

Atualizações do **Power BI Embedded** são lançadas regularmente. No entanto, nem todas as versões incluem novos recursos voltados para o usuário; algumas versões se concentram em recursos de serviço de back-end. Destacaremos novos recursos voltados para o usuário aqui. Passe aqui para conferir regularmente.

## <a name="march-2017"></a>Março de 2017

<iframe width="640" height="360" src="https://www.youtube.com/embed/ibuN4DzCl5c?showinfo=0" frameborder="0" allowfullscreen></iframe>

**Recursos de autoatendimento**

* [Criar novo relatório](power-bi-embedded-create-report-from-dataset.md)
* [Relatório SaveAs](power-bi-embedded-save-reports.md)
* Inserir o relatório no modo de Leitura/Editar/Criar novo 
* [Alternar entre os modos de edição/leitura do relatório](power-bi-embedded-toggle-mode.md)

**Conectividade de dados com APIs REST**

* [Criar conjunto de dados](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* Enviar dados por push 

**APIs de gerenciamento**

* Clonar relatório e conjunto de dados
* Associar um relatório a um conjunto de dados diferente

**Exemplos**

* [Amostra de inserção de relatório JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo) atualizada

## <a name="december-2016"></a>Dezembro de 2016

* [Novo exemplo inserido de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>Outubro de 2016

* [Análise Avançada com o Power BI Embedded e o R](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31st-2016"></a>31 de agosto de 2016
Incluído nesta versão:

* Todos os novos SDKs do JavaScript que dão suporte à [filtragem avançada e à navegação de página](power-bi-embedded-interact-with-reports.md).
* Agora, no datacenter central do Canadá, há suporte para o Power BI Embedded. Verifique o [status do datacenter](https://azure.microsoft.com/status/).

## <a name="july-11th-2016"></a>11 de julho de 2016
Incluído nesta versão:

* **Ótimas notícias!** O serviço Power BI Embedded não está mais no modo de visualização - agora é GA (disponível de forma geral).  
* Todas as APIs REST foram movidas de **/beta** para **/v1.0**.
* Os SDKs de JavaScript e .NET foram atualizados para **v1.0**.
* Chamadas à API do Power BI agora podem ser autenticadas usando chaves de API. Tokens de Aplicativo são necessários somente para incorporação. Como parte desse processo, tokens de provisionamento e desenvolvimento foram substituídos nas APIs da versão 1.0, mas eles continuarão a funcionar na versão beta até 30/12/2016. Para saber mais, confira [Autenticando e autorizando com o Power BI Embedded](power-bi-embedded-app-token-flow.md).
* Suporte a RLS (segurança em nível de linha) para tokens de aplicativo e relatórios incorporados. Para saber mais, confira [Segurança em nível de linha com o Power BI Embedded](power-bi-embedded-rls.md).
* Atualizado o aplicativo de exemplo para todas as chamadas à API da **v1.0** .
* O Power BI Embedded dá suporte a SDK do Azure, PowerShell e CLI.
* Os usuários podem exportar dados de visualização para um **.csv**.
* O Power BI Embedded agora tem suporte em todos os mesmos idiomas/localidades que o Microsoft Azure. Para saber mais, confira [Azure – Idiomas](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).


