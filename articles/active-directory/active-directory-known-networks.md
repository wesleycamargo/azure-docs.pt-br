<properties 
	pageTitle="Redes conhecidas | Microsoft Azure" 
	description="Configurando redes conhecidas, você pode evitar ter endereços IP que pertencem à sua organização incluídos nos relatórios Entradas de várias regiões geográficas e Entradas de endereços IP com atividade suspeita." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="femila"  
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2016" 
	ms.author="markvi"/>

# Redes conhecidas


Você pode usar os relatórios de uso e de acesso do Active Directory do Azure para obter visibilidade quanto à integridade e a segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem estar, de modo que pode fazer planos adequados para mitigar esses riscos.

É possível que os relatórios "*Entradas de várias regiões geográficas*" e "*Entradas de endereços IP com atividade suspeita*" sinalizem incorretamente os endereços IP que realmente pertencem à sua organização.

Por exemplo, isso pode ocorrer quando:

- Um usuário em um escritório em Boston ao conectar-se remotamente a um data center em San Francisco aciona o relatório "Entradas de várias regiões geográficas"

- Um usuário de sua organização que tenta entrar diversas vezes com uma senha incorreta aciona o relatório "Entradas de endereços IP com atividade suspeita”

Para impedir que esses casos gerem relatórios de segurança enganadores, você deverá adicionar intervalos de endereços IP conhecidos à lista de endereços IP públicos de sua organização.


###Para adicionar os intervalos de endereços IP públicos da sua organização, execute as seguintes etapas: 

1.	Entre no [portal de gerenciamento do Azure](https://manage.windowsazure.com).

2.	No painel esquerdo, clique em **Active Directory**. <br><br>![Como o Cloud App Discovery funciona](./media/active-directory-known-networks/known-netwoks-01.png)

3.	Na guia **Diretório**, selecione o diretório.

4.	No menu na parte superior, clique em **Configurar**. <br><br>![Como o Cloud App Discovery funciona](./media/active-directory-known-networks/known-netwoks-02.png)

5.	Na guia Configurar, vá para **os intervalos de endereços IP públicos da sua organização** <br><br>![Como o Cloud App Discovery funciona](./media/active-directory-known-networks/known-netwoks-03.png)

6.	Clique em **Adicionar Intervalos de Endereços IP conhecidos**.

7.	Adicione seus intervalos de endereços na caixa de diálogo que aparece e clique no botão de verificação quando você terminar. <br><br>![Como o Cloud App Discovery funciona](./media/active-directory-known-networks/known-netwoks-04.png)


**Recursos adicionais**


* [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md)
* [Entradas de endereços IP com atividade suspeita](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Entradas de várias regiões geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)

<!---HONumber=AcomDC_0727_2016-->