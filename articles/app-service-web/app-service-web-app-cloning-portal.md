---
title: Clonagem de Aplicativo Web usando o Portal do Azure
description: Saiba como clonar seus Aplicativos Web em novos Aplicativos Web usando o Portal do Azure.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2016
ms.author: ahmedelnably

---
# Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Portal do Azure
O recurso de clonagem nos [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) permite a clonagem fácil de aplicativos Web existentes em um aplicativo recém-criado, em uma região diferente ou na mesma região. Isso permitirá que os clientes implantem vários aplicativos em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativo atualmente só tem suporte para planos de serviço de aplicativos de camada Premium. O novo recurso usa as mesmas limitações que o recurso de Backup dos Aplicativos Web; veja [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Clonagem de um aplicativo existente
O aplicativo Web deve estar sendo executado no modo **Premium** para que você crie um clone do aplicativo Web.

1. No [Portal do Azure](https://portal.azure.com/), abra a folha de seu aplicativo Web.
2. Clique em **Ferramentas**. Em seguida, na folha **Ferramentas**, clique em **Clonar Aplicativo**.
   
    ![][1]
   
   > [!NOTE]
   > Se o aplicativo Web ainda não estiver no modo **Premium**, você receberá uma mensagem indicando os modos com suporte para a clonagem de aplicativo. Neste ponto, você tem a opção de selecionar **Atualizar**.
   > 
   > 
3. Na folha **Clonar Aplicativo**, forneça um nome do novo aplicativo Web, um Grupo de Recursos e um Plano do Serviço de Aplicativo. Além disso, o usuário poderá optar por clonar ou não várias configurações do aplicativo Web de origem.
   
    ![][2]
4. Depois de clicar em **criar**, a plataforma iniciará o trabalho de criação de um clone do aplicativo Web de origem.

## Clonagem de um aplicativo existente como um Ambiente do Serviço de Aplicativo
Na folha **Clonar Aplicativo**, o cliente terá a opção de escolher um pool de aplicativos em um Ambiente do Serviço de Aplicativo existente.

## Restrições atuais
Atualmente, esse recurso está em preview. Estamos trabalhando para adicionar novas funcionalidades com o tempo. A lista abaixo mostra as restrições conhecidas no suporte atual da clonagem de aplicativo no Portal do Azure:

* As configurações do Gerenciador de Tráfego do Azure não são clonadas
* As configurações de escala automática não são clonadas
* As configurações de agendamento de backup não são clonadas.
* As configurações da rede virtual não são clonadas
* O Application Insights não está automaticamente configurado no aplicativo Web de destino
* As configurações de Autenticação Fácil não são clonadas
* A extensão Kudu não é clonada
* As regras de TiP não são clonadas
* O conteúdo do banco de dados não é clonado

### Referências
* [Clonagem de Aplicativo Web usando o PowerShell](app-service-web-app-cloning.md)
* [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](web-sites-backup.md)
* [Como Criar um Ambiente do Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md)
* [Criar um aplicativo Web em um Ambiente de Serviço de Aplicativo](app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Introdução ao ambiente de Serviço de Aplicativo](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png

<!---HONumber=AcomDC_0601_2016-->