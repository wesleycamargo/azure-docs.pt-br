<propriedades pageTitle="Configurar alertas de cobrança de suas assinaturas do Microsoft Azure | Microsoft Azure" descrição="Descreve como você pode configurar alertas na fatura do Azure para evitar surpresas de cobrança." serviços="billing" documentationCenter="" autores="vikdesai" manager="msmbaldwin" editor=""/ tags="billing" />

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/17/2016"
	ms.author="vikdesai"/>

# Configurar alertas de cobrança de suas assinaturas do Microsoft Azure

Você está preocupado com o quanto você está gastando todos os meses com a sua assinatura do Azure? Se você for o administrador da conta de uma assinatura do Azure, você pode usar o serviço de alerta de cobrança do Azure para criar um alerta personalizado de cobrança que o ajudará a monitorar e gerenciar as atividades de cobrança de suas contas do Azure.

Este é um serviço de visualização, portanto, a primeira coisa que você precisa fazer é registrar-se - visite <a href="https://account.windowsazure.com/PreviewFeatures">a página de Recursos de Visualização </a> no portal de gerenciamento da conta do Azure para fazer isso.

## Definir os alertas de limite e destinatários de email

Depois de receber o email de confirmação de que o serviço de cobrança foi ativado para a sua assinatura, visite <a href="https://account.windowsazure.com/Subscriptions">a página Assinaturas</a> no portal da conta. Clique na assinatura que você deseja monitorar e, em seguida, clique em **Alertas**.

![][Image1]

Em seguida, clique em **Adicionar Alerta** para criar seu primeiro alerta - você pode configurar até cinco alertas de cobrança por assinatura, com um limite diferente e até dois destinatários de email para cada alerta.

![][Image2]

Quando você adiciona um alerta, você informa um nome exclusivo, escolhe um limite de gastos e escolhe os endereços de email para onde os alertas serão enviados. Ao configurar o limite, você pode escolher um **Total de cobrança** ou um **Crédito Monetário** na lista **Alerta para**. Para total de cobrança, um alerta é enviado quando o gasto com a assinatura excede o limite. Para crédito monetário, um alerta é enviado quando os créditos monetários reduzem abaixo do limite. Créditos monetários geralmente se aplicam a avaliações gratuitas e assinaturas associadas às contas do MSDN.

![][Image3]

O Azure é compatível com qualquer endereço de email, mas não verifica o funcionamento do endereço de email, por isso, verifique atentamente se digitou corretamente.

## Verificar os seus alertas

Depois de configurar os alertas, o Centro de Contas lista e mostra quantos mais você pode configurar. Para cada alerta, você verá a data e a hora de envio, se é um alerta do Total de Cobrança ou de Crédito Monetário e o limite definido. O formato de data e hora é 24 horas - Hora Universal Coordenada (UTC) e a data usa o formato aaaa-mm-dd. Clique no sinal de adição de um alerta na lista para editá-lo, ou clique na lixeira para excluí-lo.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=AcomDC_0420_2016-->