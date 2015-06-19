<properties 
	pageTitle="Opções de segurança para Máquinas Virtuais do Azure" 
	description="Rápida visão geral dos principais recursos de segurança para máquinas virtuais do Azure e links para detalhes" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Opções de segurança para Máquinas Virtuais do Azure

<p>As tarefas e a configuração de segurança das máquinas virtuais do Azure são, em grande parte, iguais às de qualquer outra máquina virtual ou computador físico. Entre elas estão noções básicas como usar senhas fortes, não usar nomes de usuário bem conhecidos e estabelecer uma política e uma agenda para atualizar o sistema operacional e os aplicativos. Além disso, recomendamos as seguintes práticas e fornecemos recursos que é possível usar para implementá-las:

- Executar antivírus/anti-malware

- Usar ACLs (listas de controle de acesso) de rede em pontos de extremidade
 
##Executar antivírus/anti-malware

O Azure oferece diversas opções para soluções de antivírus/anti-malware, mas cabe a você gerenciar isso. Por exemplo, você precisará decidir quando criar a máquina virtual e instalar atualizações. Você pode adicionar suporte a antivírus ao criar a máquina virtual ou depois. Atualmente, três soluções são oferecidas como extensões de segurança, que podem ser instaladas em máquinas virtuais novas e existentes:

- [Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Implantando soluções antimalware em máquinas virtuais do Azure](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)
 

##Usar (listas de controle de acesso) ACLs de rede em pontos de extremidade de máquina virtual

As ACLs de rede possibilitam permitir ou negar seletivamente tráfego de entrada em um ponto de extremidade de máquina virtual. Essa capacidade de filtragem de pacotes proporciona uma camada adicional de segurança. Para obter detalhes sobre como isso funciona e links para instruções, consulte [Sobre ACLs (listas de controle de acesso)](http://go.microsoft.com/fwlink/?LinkId=506655).

##Recursos adicionais
[Recursos](http://azure.microsoft.com/support/trust-center/resources/) no Microsoft Azure Trust Center

<!---HONumber=58--> 