<properties
    pageTitle="Entradas após várias falhas"
    description="Um relatório que indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>


# <a name="sign-ins-after-multiple-failures"></a>Entradas após várias falhas
Este relatório indica os usuários que obtiveram êxito após várias tentativas de entrada malsucedidas consecutivas. Entre as possíveis causas estão:

- O usuário esqueceu a senha</li><li>O usuário for vítima de uma ataque de força bruta de detecção de senha bem-sucedido

Os resultados desse relatório mostrarão o número de tentativas consecutivas de entrada com falha feitas antes da entrada bem-sucedida e um carimbo de data/hora associado à primeira entrada bem-sucedida.

**Configurações do Relatório**: é possível configurar o número mínimo de tentativas consecutivas de entrada com falha que devem ocorrer antes que ele possa ser exibido no relatório. Quando você fizer alterações nessa configuração, é importante observar que essas alterações não serão aplicadas a qualquer entrada com falha existente exibido no momento no relatório existente. Todavia, elas serão aplicadas a todas as entradas futuras. Alterações a esse relatório só podem ser feitas por administradores licenciados.


![Entradas após várias falhas](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)



<!--HONumber=Oct16_HO2-->


