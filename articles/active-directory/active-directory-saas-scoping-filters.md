<properties
	pageTitle="Provisionamento de aplicativo com base em atributos com filtros de escopo | Microsoft Azure"
	description="Saiba como usar filtros de escopo para impedir que objetos em aplicativos, que dão suporte a provisionamento automatizado de usuários, sejam provisionados, caso um objeto não satisfaça suas necessidades de negócios."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Provisionamento de aplicativo com base em atributo com filtros de escopo

O objetivo desta seção é explicar como usar filtros de escopo para definir regras baseadas em atributo que determinam quais usuários serão provisionados no aplicativo.





## Cláusulas e grupos de escopo


![Filtro de Escopo][1]




Filtros de escopo são definidos por um ou mais **grupos de escopo** e cada um deles contém uma ou mais **cláusulas**. Para ver as cláusulas de determinado grupo de escopo, expanda-o clicando na seta à esquerda do nome do grupo.

Uma **cláusula** determina quais usuários têm permissão para passar pelo filtro de escopo, avaliando os atributos de cada usuário. Por exemplo, pode haver uma cláusula que requer que o atributo de “estado” de um usuário seja igual a Nova York, o que significa que somente os usuários de Nova York serão provisionados no aplicativo.

![Nome do grupo de escopo][2]



Cada **grupo de escopo** começa com uma **cláusula** obrigatória conforme mostrado na captura de tela acima. Essa cláusula simplesmente informa que o usuário deve primeiro ser atribuído ao aplicativo antes de ser avaliado por seus filtros de escopo. Essa cláusula não pode ser excluída nem modificada.

Você pode adicionar novas cláusulas ou novos grupos de escopo, pressionando o botão adequado. Você pode dar um nome a cada grupo de escopo editando sua propriedade **Nome do Grupo de Escopo**.





## Como os filtros de escopo são avaliados

Durante o provisionamento, testamos cada usuário atribuído em relação a seus filtros de escopo para determinar se esse usuário merece acesso ao aplicativo. Você pode pensar em cada cláusula como sendo um teste que o usuário deve passar para evitar ser filtrado.

Se houver vários grupos de escopo definidos, cada usuário deverá passar em pelo menos um deles para acessar o aplicativo. Em cada grupo de escopo, no entanto, o usuário deve passar por cada cláusula única, a fim de passar para aquele grupo de escopo específico.

Em outras palavras, você pode pensar nos grupos de escopo como sendo agrupados por OR e pode considerar as cláusulas dentro deles como sendo agrupadas por AND. Por exemplo, considere o filtro de escopo abaixo:


![Nome do grupo de escopo][2]


De acordo com esse filtro de escopo, os usuários devem atender aos seguintes critérios para serem provisionados:

1. Eles devem ser atribuídos ao aplicativo.

2. Eles devem trabalhar no departamento de engenharia

3. Eles devem trabalhar em São Francisco ou no Canadá.


##Artigos relacionados

- [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
- [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
- [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
- [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
- [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

<!---HONumber=AcomDC_0211_2016-->