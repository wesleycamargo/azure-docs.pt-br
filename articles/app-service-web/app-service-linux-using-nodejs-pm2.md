---
title: "Usando a configuração PM2 para Node.js em aplicativos Web no Linux | Microsoft Docs"
description: "Usando a configuração PM2 para Node.js em aplicativos Web no Linux"
keywords: "serviço de aplicativo do Azure, aplicativo web, nodejs, pm2, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a46f52198e956406c2384c45ed4323aff27259a0


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Usar a configuração PM2 para Node.js em aplicativos Web no Linux
Se você definir a pilha de aplicativos para o Node.js para aplicativos Web no Linux, poderá ter a opção de definir um arquivo de inicialização do Node.js, como mostrado na imagem a seguir.

![Definir um arquivo de inicialização do Node.js][1]

Você pode usar isso em uma das seguintes tarefas:

* Especificar o script de inicialização para seu aplicativo Node.js (por exemplo: /bin/server.js).
* Especificar o arquivo de configuração PM2 a ser usado para seu aplicativo Node.js (por exemplo: /foo/process.json).
  
  > [!NOTE]
  > Se você quiser que seus processos do Node.js sejam reiniciados automaticamente quando determinados arquivos forem modificados, use a configuração PM2. Caso contrário, seu aplicativo não será reiniciado quando receber as notificações de alteração (por exemplo, quando o código do aplicativo for alterado).
  > 
  > 

Você pode verificar a [documentação do arquivo de processos](http://pm2.keymetrics.io/docs/usage/application-declaration/) do Node.js para todas as opções, mas veja a seguir um exemplo do que você pode usar como seu arquivo process.json:

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Os aspectos importantes a observar nessa configuração são:

* A propriedade "script" especifica o script de inicialização do aplicativo.
* A propriedade "instances" especifica quantas instâncias do processo de nó serão iniciadas. Se você estiver executando seu aplicativo em VMs maiores com vários núcleos, é uma boa ideia para maximizar seus recursos definir um valor mais alto aqui.
* A matriz "watch" especifica todos os arquivos para os quais você deseja reiniciar o processo de nó quando eles forem alterados.
* No momento, para "watch_options", é necessário especificar "usePolling" como true devido ao modo como o conteúdo do aplicativo é montado.

## <a name="next-steps"></a>Próximas etapas
* [O que é Serviço de Aplicativo no Linux?](app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png



<!--HONumber=Nov16_HO3-->


