---
title: "Configurações de Registro do Cloud App Discovery para Serviços de Proxy | Microsoft Docs"
description: "O objetivo deste tópico é fornecer as etapas que você precisa executar para configurar a porta necessária nos computadores que executam o agente Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 7f2a3c4bf8fda9dd235986be8b0ceaa8eb8313e0
ms.contentlocale: pt-br
ms.lasthandoff: 12/29/2016

---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Configurações de Registro do Cloud App Discovery para serviços de proxy
Por padrão, o agente Cloud App Discovery é configurado para usar somente as portas 80 ou 443. Se você estiver planejando instalar o Cloud App Discovery em um ambiente com um servidor proxy que está usando uma porta personalizada (nem 80 nem 443), precisa configurar seus agentes para usar essa porta. A configuração baseia-se em uma chave do Registro.

O objetivo deste tópico é fornecer as etapas que você precisa executar para configurar a porta necessária nos computadores que executam o agente Cloud App Discovery.

**Para modificar a porta usada pelo computador que está executando o agente Cloud App Discovery, execute as seguintes etapas:**

1. Inicie o editor do Registro. <br> ![Executar](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Navegue até ou crie a seguinte chave do Registro:  <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 
3. Crie um novo valor de **cadeia de caracteres múltipla** chamado **Portas**. ![Novo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Para abrir a caixa de diálogo **Editar Cadeia de Caracteres Múltipla** , clique duas vezes no valor Portas.
5. Na caixa de texto de dados Valor, digite os seguintes valores e adicione todas as portas personalizadas usadas pela sua organização:  <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Editar Cadeia de Caracteres Múltipla](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Clique em **OK** para fechar a caixa de diálogo **Editar Cadeia de Caracteres Múltipla**.

**Recursos adicionais**

* [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md) 


