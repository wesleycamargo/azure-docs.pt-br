---
title: "Habilitar a Afinidade de Sessão usando o Kit de Ferramentas do Azure para o Eclipse"
description: "Saiba como habilitar a afinidade de sessão usando o Kit de Ferramentas do Azure para o Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 88c595ec-7d85-40bd-9078-8d6be7b3f0fa
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 3fb7a0361717d4616ec21e55c0a202ed8bf8991d
ms.openlocfilehash: a537a71a43b49d510a12d1293d8ee898d66efe63


---
# <a name="enable-session-affinity"></a>Habilitar a afinidade de sessão
No Kit de Ferramentas do Azure para o Eclipse, é possível habilitar a afinidade de sessão HTTP, ou “sessões adesivas”, para suas funções. A seguinte imagem mostra o diálogo de propriedades **Balanceamento de Carga** usado para habilitar o recurso de afinidade de sessão:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Para habilitar a afinidade de sessão para sua função
1. Clique com o botão direito do mouse na função no Explorador de Projetos do Eclipse, clique em **Azure** e em **Balanceamento de Carga**.
2. No diálogo **Propriedades de balanceamento de carga de WorkerRole1** :
   1. Verifique **Habilitar a afinidade de sessão HTTP (sessões autoadesivas) para esta função.**
   2. Para **Ponto de extremidade de entrada a ser usado**, selecione um ponto de extremidade de entrada a ser usado, por exemplo, **http (pública:&80;, privada:&8080;)**. Seu aplicativo deve usar esse ponto de extremidade como seu ponto de extremidade HTTP. Você pode habilitar vários pontos de extremidade para a sua função, mas selecionar apenas um para dar suporte às sessões adesivas.
   3. Recompile seu aplicativo.

Quando habilitadas, se você tiver mais de uma instância de função, as solicitações HTTP provenientes de um determinado cliente continuarão sendo manipuladas pela mesma instância de função.

O Kit de Ferramentas para o Eclipse permite isso instalando um módulo IIS especial chamado ARR (Roteamento de Solicitação do Aplicativo) em cada uma de suas instâncias de função. O ARR redireciona as solicitações HTTP para a instância de função apropriada. O kit de ferramentas reconfigura automaticamente o ponto de extremidade selecionado para que o tráfego HTTP de entrada seja primeiramente roteado para o software ARR. O Kit de Ferramentas também cria um novo ponto de extremidade interno que o servidor Java está configurado para escutar. Esse é o ponto de extremidade usado pelo ARR para redirecionar o tráfego HTTP para a instância de função apropriada. Dessa forma, cada instância de função em sua implantação de várias instâncias serve como um proxy reverso para todas as outras instâncias, habilitando as sessões adesivas.

## <a name="notes-about-session-affinity"></a>Observações sobre a afinidade de sessão
* A afinidade de sessão não funciona no emulador de computação. As configurações podem ser aplicadas no emulador de computação sem interferir no processo de compilação ou na execução do emulador de computação, mas o recurso em si não funciona no emulador de computação.
* Habilitar a afinidade de sessão resultará em um aumento na quantidade de espaço em disco ocupada pela sua implantação no Azure, já que um software adicional será baixado e instalado em suas instâncias de função quando o serviço for iniciado na nuvem do Azure.
* O tempo para inicializar cada função levará mais tempo.
* Um ponto de extremidade interno, para funcionar como um novo roteador de tráfego, como mencionado acima, será adicionado.


## <a name="see-also"></a>Consulte também
[Kit de Ferramentas do Azure para Eclipse][Azure Toolkit for Eclipse]

[Criar um aplicativo Hello World para Azure no Eclipse][Creating a Hello World Application for Azure in Eclipse]

[Instalar o Kit de Ferramentas do Azure para Eclipse][Installing the Azure Toolkit for Eclipse] 

Para saber mais sobre como usar o Azure com o Java, confira o [Centro de Desenvolvedores Java do Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[How to Maintain Session Data with Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699539
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->



<!--HONumber=Jan17_HO2-->


