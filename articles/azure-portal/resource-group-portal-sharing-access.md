<properties
	pageTitle="Compartilhamento e controle de acesso de painéis do Azure | Microsoft Azure"
	description="Saiba como gerenciar o compartilhamento e o controle de acesso dos painéis do Azure."
	services="azure-resource-manager,azure-portal"
    keywords="portal, compartilhamento, acesso"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Compartilhamento e controle de acesso de painéis do Azure

O acesso às informações exibidas pela maioria dos blocos no portal é controlado pelo [Controle de Acesso Baseado em Função](./active-directory/role-based-access-control-configure.md) do Azure. Para integrar painéis ao ecossistema diretamente, todos os painéis publicados são implementados como recursos do Azure. Do ponto de vista do controle de acesso, os painéis não são diferentes de uma máquina virtual ou de uma conta de armazenamento.

Aqui está um exemplo. Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe tenham recebido atribuições para as funções de **proprietário**, **colaborador** ou **leitor** da assinatura. Os usuários que são proprietários ou colaboradores poderão listar, exibir, criar, modificar ou excluir painéis na assinatura. Os usuários que são os leitores poderão listar e exibir os painéis, mas não poderão modificá-los ou excluí-los. Os usuários com acesso de leitor poderão fazer edições locais em um painel publicado (por exemplo, ao solucionar um problema), mas não terão a opção de publicar essas alterações no servidor. Eles terão a opção de fazer uma cópia privada do painel para uso pessoal.

Observe que os blocos individuais no painel irão impor seus próprios requisitos de controle de acesso baseados nos recursos de que apresentam dados. Isso significa que você pode criar um painel que pode ser compartilhado mais amplamente e ainda proteger os dados em blocos individuais.

<!---HONumber=AcomDC_0622_2016-->