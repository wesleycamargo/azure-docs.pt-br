---
title: "Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo do Azure no Linux | Microsoft Docs"
description: "Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, docker, contêiner"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 7e4aab65feac187b48ccca65b35bb94185323506
ms.lasthandoff: 02/17/2017


---

# <a name="using-a-custom-docker-image-for-app-service-on-linux"></a>Como usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux #

O Serviço de Aplicativo fornece pilhas de aplicativos predefinidas no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. O Serviço de Aplicativo no Linux usa contêineres do Docker para hospedar estas pilhas de aplicativos predefinidas. Também é possível usar uma imagem personalizada do Docker para implantar seu aplicativo Web em uma pilha de aplicativos que ainda não foi definida no Azure. As imagens personalizadas do Docker podem ser hospedadas em um repositório público ou privado do Docker.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Como definir uma imagem personalizada do Docker para um aplicativo Web
Você pode definir a imagem personalizada do Docker para aplicativos Web novos e existentes. Quando você cria um aplicativo Web no Linux no [Portal do Azure](https://portal.azure.com), clique em **Configurar contêiner** para definir uma imagem personalizada do Docker:

![Imagem personalizada do Docker para um novo aplicativo Web no Linux][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Como usar uma imagem personalizada do Docker no Hub do Docker ##
Para usar uma imagem personalizada do Docker no Hub do Docker:

1. No [portal do Azure](https://portal.azure.com), localize seu aplicativo Web no Linux e, em **Configurações**, clique em **Contêiner do Docker**.

2.  Selecione **Hub do Docker** como a **Origem da imagem** e, depois, clique em **Pública** ou **Privada** e digite o **Nome da imagem e o nome opcional da marca**, por exemplo `node:4.5`. O **Comando de inicialização** é definido automaticamente com base no que está definido no arquivo de imagem do Docker, mas você pode definir seus próprios comandos.  

    ![Configurar a imagem do repositório público do Hub do Docker][2]

    Quando a imagem é de um repositório privado, também é precisa inserir as credenciais de Hub do Docker como (**Nome de usuário de logon** e **Senha**) para o repositório privado do Hub do Docker.

    ![Configurar a imagem do repositório privado do Hub do Docker][3]

3. Depois de configurar o contêiner, clique em **Salvar**.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>Como usar uma imagem do Docker a partir de um registro da imagem privada ##
Para usar uma imagem personalizada do Docker a partir de um registro da imagem privada:

1. No [portal do Azure](https://portal.azure.com), localize seu aplicativo Web no Linux e, em **Configurações**, clique em **Contêiner do Docker**.

2.  Clique em **Registro privado** como a **Origem da imagem**. Insira o **Nome da imagem e da marca opcional**, **URL do Servidor** para o registro privado, juntamente com as credenciais (**nome de usuário de logon** e **senha**). Clique em **Salvar**.

    ![Configurar a imagem de Docker a partir do registro privado][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Como configurar a porta usada pela sua imagem do Docker ##

Quando você usa uma imagem personalizada do Docker para seu aplicativo Web, você pode usar a variável de ambiente `PORT` em seu Dockerfile, que é adicionado ao contêiner gerado. Considere o exemplo a seguir de um arquivo do docker para um aplicativo Ruby:

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

Na última linha do comando, você pode ver que a variável de ambiente PORT é transmitida em tempo de execução. Lembre-se de que as diferenças entre maiúsculas e minúsculas importam nos comandos.

Ao usar uma imagem existente do Docker criada por outra pessoa, talvez seja necessário especificar uma porta diferente da porta 80 para o aplicativo. Para configurar a porta, adicione um configuração de aplicativo chamada `PORT` com o valor mostrado abaixo:

![Definir a configuração de aplicativo PORT para a imagem personalizada do Docker][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Como voltar a usar uma imagem interna ##

Para trocar uma imagem personalizada para uma imagem interna:

1. No [portal do Azure](https://portal.azure.com), localize seu aplicativo Web no Linux e, em **Configurações**, clique em **Serviço de Aplicativo**.

2. Selecione sua **Pilha em Tempo de Execução** para usar para a imagem interna, em seguida, clique em **Salvar**. 

![Configurar a imagem interna do Docker][5]


## <a name="troubleshooting"></a>Solucionar problemas ##

Quando o aplicativo falha ao iniciar com sua imagem personalizada do Docker, verifique os log do Docker no diretório LogFiles/docker. Acesse esse diretório por meio de seu site SCM ou via FTP. 

![Como usar o Kudu para exibir os logs do Docker][7]

Acesse o site SCM nas **Ferramentas Avançadas** no menu **Ferramentas de Desenvolvimento**.

## <a name="next-steps"></a>Próximas etapas ##

Siga os links a seguir para começar a usar o Serviço de Aplicativo no Linux.   

* [Introdução ao Serviço de Aplicativo no Linux](./app-service-linux-intro.md)
* [Como criar Aplicativos Web no Serviço de Aplicativo no Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Usando a configuração PM2 para Node.js em Aplicativos Web no Linux](./app-service-linux-using-nodejs-pm2.md)
* [Perguntas frequentes sobre Aplicativos Web do Serviço de Aplicativo do Azure no Linux](app-service-linux-faq.md)

Poste perguntas e preocupações em [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png

