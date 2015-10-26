<properties
	pageTitle="Entradas após várias falhas"
	description="Um relatório que indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="gchander"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2015"
	ms.author="saah;kenhoff"/>

# Entradas após várias falhas
Este relatório indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas. As possíveis causas incluem: <ul><li>o usuário esqueceu sua senha</li><li>o usuário for vítima de um ataque de detecção de senha com força bruta bem-sucedido</li></ul><p>os resultados desse relatório mostrarão o número de falhas consecutivas em tentativas de entrada feitas antes da entrada bem-sucedida e um carimbo de data/hora associado à primeira entrada bem-sucedida.</p><p><b>Configurações de relatório</b>: você pode configurar o número mínimo de falhas consecutivas nas tentativas que precisam ocorrer antes que ela possa ser exibida no relatório. Quando você fizer alterações nessa configuração, é importante observar que essas alterações não serão aplicadas a qualquer entrada com falha existente exibido no momento no relatório existente. Todavia, elas serão aplicadas a todas as futuras entradas. Alterações a esse relatório só podem ser feitas por administradores licenciados.


![Entradas após várias falhas](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)

<!---HONumber=Oct15_HO3-->