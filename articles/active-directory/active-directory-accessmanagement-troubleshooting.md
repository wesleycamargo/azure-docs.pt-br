
<properties 
	pageTitle="Solução de problemas de associação dinâmica para grupos| Microsoft Azure" 
	description="Um tópico que lista dicas de solução de problemas para a associação dinâmica para grupos no AD do Azure." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor="Curtis"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Solução de problemas de associações dinâmicas para grupos

| Sintoma | Ação |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Configurei uma regra em um grupo, mas nenhuma associação é atualizada no grupo | Verifique se a configuração Habilitar gerenciamento de grupos delegados está definida como Sim na guia Configurar Diretório. Observe que você só verá essa configuração se tiver entrado com uma conta que tenha uma licença do Active Directory Premium do Azure atribuída a ela. Verifique os valores dos atributos de usuário na regra – há usuários que atendem a regra? |
| Configurei uma regra, mas agora os membros existentes da regra foram removidos | Isso é esperado; membros existentes do grupo são removidos quando uma regra é habilitada ou alterada. O conjunto de usuários resultante da avaliação da regra é adicionado como membros ao grupo. |
| Não vejo as alterações de associação instantaneamente quando adiciono ou altero uma regra; por quê? | A avaliação de associação dedicada é realizada periodicamente em um processo assíncrono em segundo plano. O número de usuários em seu locatário e o tamanho do grupo criado como resultado da regra podem influenciar quanto tempo isso demora para acontecer. Normalmente, locatários com um número pequeno de usuários verão as alterações de associação de grupo em poucos minutos. Locatários com um grande número de usuários podem levar 30 minutos ou mais até que tudo seja preenchido. |

Estes são alguns tópicos que fornecerão informações adicionais sobre o Active Directory do Azure

* [Gerenciamento do acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração das identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->