

<properties 
	pageTitle="Usando o provisionamento declarativo do Azure AD Connect" 
	description="Saiba como usar o provisionamento declarativo do Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Provisionamento declarativo do Azure AD Connect


## Usando o provisionamento declarativo 
Provisionamento declarativo é provisionamento "sem código" e pode ser instalado e configurado usando o Editor de regras de sincronização. O Editor pode ser usado para a instalação e para criar suas próprias regras de provisionamento.

Uma parte essencial de Provisionamento declarativo é a linguagem de expressão usada nos fluxos de atributo. A linguagem usada é um subconjunto de VBA (Visual Basic® for Applications) da Microsoft. Essa linguagem é usada no Microsoft Office e os usuários com experiência em VBScript também a reconhecerão. A Linguagem de expressão de provisionamento declarativo está apenas usando funções e não é uma linguagem estruturada; não existem métodos nem instruções. Em vez disso, as funções serão aninhadas no fluxo do programa expresso.

Para obter mais informações sobre linguagem, consulte [Noções básicas sobre expressões de provisionamento declarativo](https://msdn.microsoft.com/library/azure/dn801048.aspx)

<!---HONumber=62-->