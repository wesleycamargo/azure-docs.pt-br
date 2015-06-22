<properties 
   pageTitle="Lista de atualizações aplicadas ao SO convidado do Azure | Azure" 
   description="Lista de atualizações aplicadas a versões do SO convidado" 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="03/18/2015"
   ms.author="adegeo"/>

# Sistema operacional convidado do Azure 

As tabelas a seguir mostram as atualizações do Microsoft Security Response Center (MSRC) aplicadas ao SO convidado do Azure. Pesquise este artigo para determinar se uma atualização específica se aplica ao SO convidado que você está usando. Atualizações sempre são realizadas para a [família][família-explicação] em particular na qual foram introduzidas. 


## SO convidado de março

| ID do boletim | Artigo da KB pai   | Descrição da vulnerabilidade                                                   | SO convidado         | Data da primeira introdução |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-018    | [3032359][3032359] | Atualização de segurança cumulativa para o Internet Explorer                             | 4.18, 3.25, 2.37 | N/D |
| MS15-019    | [3040297][3040297] | Vulnerabilidade no mecanismo de script do VBScript pode permitir a execução remota de código | 4.18, 3.25, 2.37 | N/D |
| MS15-020    | [3041836][3041836] | Vulnerabilidades no Windows poderiam permitir a execução remota de código                 | 4.18, 3.25, 2.37 | N/D |
| MS15-021    | [3032323][3032323] | Vulnerabilidades no driver de fonte Adobe poderiam permitir a execução remota de código          | 4.18, 3.25, 2.37 | N/D |
| MS15-023    | [3034344][3034344] | Vulnerabilidades no driver do modo Kernel poderiam permitir elevação de privilégio     | 4.18, 3.25, 2.37 | N/D |
| MS15-024    | [3035132][3035132] | Vulnerabilidade no processamento de PNG poderia permitir a divulgação de informações           | 4.18, 3.25, 2.37 | N/D |
| MS15-025    | [3038680][3038680] | Vulnerabilidades no Kernel do Windows poderiam permitir elevação de privilégio         | 4.18, 3.25, 2.37 | N/D |
| MS15-027    | [3002657][3002657] | Vulnerabilidade no NETLOGON poderia permitir falsificação                               | 4.18, 3.25, 2.37 | N/D |
| MS15-029    | [3035126][3035126] | Vulnerabilidade no componente do decodificador de fotos do Windows poderia permitir a divulgação de informações | 4.18, 3.25, 2.37 | N/D |

> [AZURE.NOTE] O boletim MS15-031 pode aparecer como desinstalado. No entanto, ele não se aplica a esta versão do SO convidado.

## SO convidado de fevereiro

| ID do Boletim | Artigo da Base de Dados de Conhecimento Pai | Descrição da Vulnerabilidade | Sistema Operacional Convidado | Data da Primeira Introdução |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-009    | [3034682][3034682] | Atualização de segurança para o Internet Explorer                                                   | 4.17, 3.24, 2.36 | N/D |
| MS15-010 | [3036220][3036220] | Vulnerabilidades nos drivers de modo de Kernel do Windows podem permitir a execução remota de código | 4.17, 3.24, 2.36 | N/D |
| MS15-011 | [3000483][3000483] | Vulnerabilidade na política de grupo pode permitir a execução remota de código | 4.17, 3.24, 2.36 | N/D |
| MS15-014 | [3004361][3004361] | Vulnerabilidade no SMB poderia permitir ignorar recurso de segurança | 4.17, 3.24, 2.36 | N/D |
| MS15-015 | [3031432][3031432] | Vulnerabilidade no Microsoft Windows poderia permitir elevação de privilégio | 4.17, 3.24, 2.36 | N/D |
| MS15-016 | [3029944][3029944] | Vulnerabilidade no componente gráfico da Microsoft poderia permitir a divulgação de informações | 4.17, 3.24, 2.36 | N/D |
| N/A         | [3004375][3004375] | Atualização para melhorar a auditoria de linha de comando do Windows<p/>**Observação**: O recurso é instalado, mas a chave do Registro para habilitá-lo está desativada | 4.17, 3.24, 2.36 | N/D |
 

## SO convidado de janeiro

| ID do Boletim | Artigo da Base de Dados de Conhecimento Pai | Descrição da Vulnerabilidade | Sistema Operacional Convidado | Data da Primeira Introdução |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| MS15-001 | [3023266][3023266] | Vulnerabilidade no Windows AppCompatCache poderia permitir elevação de privilégio | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-002    | [3020393][3020393] | Vulnerabilidade no serviço de Telnet do Windows pode causar a execução remota de código   | 4.16, 3.23, 2.35 | 19 de janeiro de 2015           |
| MS15-003 | [3021674][3021674] | Vulnerabilidade no serviço de perfil do usuário do Windows poderia permitir elevação de privilégio | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-004 | [3019978][3019978] | Vulnerabilidade nos componentes do Windows poderia permitir elevação de privilégio | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-005 | [3022777][3022777] | Vulnerabilidade no NLA pode permitir ignorar recurso de segurança | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-006 | [3004365][3004365] | Vulnerabilidade no Relatório de Erros do Windows poderia permitir ignorar recurso de segurança | 4.16, 3.23 2.35 | 19 de janeiro de 2015 |
| MS15-007 | [3014029][3014029] | Vulnerabilidade no Servidor de Políticas de Rede RADIUS poderia causar negação de serviço | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-008 | [3019215][3019215] | Vulnerabilidade no driver de modo de Kernel do Windows poderia permitir elevação de privilégio | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS14-080    | [3008923][3008923]  | Atualização de segurança cumulativa para o Internet Explorer | 4.16, 3.23, 2.35 | 19 de janeiro de 2015 |
| MS15-002    | [3020393][3020393] | Vulnerabilidade no serviço de Telnet do Windows pode causar a execução remota de código   | 4.16, 3.23, 2.35 | 19 de janeiro de 2015           |
| MS15-002    | [3020393][3020393] | Vulnerabilidade no serviço de Telnet do Windows pode causar a execução remota de código   | 4.16, 3.23, 2.35 | 19 de janeiro de 2015           |
 
## SO convidado de dezembro

| ID do Boletim | Artigo da Base de Dados de Conhecimento Pai | Descrição da Vulnerabilidade | Sistema Operacional Convidado | Data da Primeira Introdução |
| ----------- | ------------------- | --------------------------------------------------------------------------- | ---------------- | --------------------- |
| N/A         | [3013776][3013776] | O sistema congela quando você usa uma conta de domínio para iniciar um aplicativo | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 | 
| N/A         | [3013043][3013043] | Dados de sistema de arquivos estão corrompidos em um computador baseado no Windows com mais de um nó NUMA | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3012712][3012712] | Novos blocos de dados inicializam incorretamente quando um VHD diferencial é expandido | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3004905][3004905] | Aprimoramento do Windows Hyper-V para máquinas virtuais Linux com sistemas de arquivos maiores que 2 TB | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3004394][3004394] | Atualização de dezembro de 2014 para Windows Root Certificate Program no Windows | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [2999323][2999323] | O texto para o evento ID 17 é alterado | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3013488][3013488] | Espera longa para reiniciar o servidor do WSUS ao importar arquivos CSA no Windows Server 2012 R2 ou o Windows Server 2012 | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3012325][3012325] | Entradas de banco de dados do Windows APN atualizar para operadoras de celular DIGI, Vodafone e Telekom no Windows 8.1 e Windows 8 | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [3007054][3007054] | Opção de impressão protegida por PIN sempre aparece quando você imprime um documento em um aplicativo da Windows Store no Windows | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [2999802][2999802] | Linhas sólidas, em vez de linhas pontilhadas, são impressas no Windows | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |
| N/A         | [2896881][2896881] | Longo tempo de logon quando você usa o comando AddPrinterConnection VBScript para mapear impressoras para usuários durante o processo de logon do Windows | 4.15, 3.22, 2.34 | 13 de janeiro de 2015 |




[3034682]: http://support.microsoft.com/kb/3034682
[3036220]: http://support.microsoft.com/kb/3036220
[3000483]: http://support.microsoft.com/kb/3000483
[3004361]: http://support.microsoft.com/kb/3004361
[3031432]: http://support.microsoft.com/kb/3031432
[3029944]: http://support.microsoft.com/kb/3029944
[3004375]: http://support.microsoft.com/kb/3004375

[3023266]: http://support.microsoft.com/kb/3023266
[3020393]: http://support.microsoft.com/kb/3020393
[3021674]: http://support.microsoft.com/kb/3021674
[3019978]: http://support.microsoft.com/kb/3019978
[3022777]: http://support.microsoft.com/kb/3022777
[3004365]: http://support.microsoft.com/kb/3004365
[3014029]: http://support.microsoft.com/kb/3014029
[3019215]: http://support.microsoft.com/kb/3019215
[3008923]: http://support.microsoft.com/kb/3008923
[3020393]: http://support.microsoft.com/kb/3020393

[3013776]: http://support.microsoft.com/kb/3013776
[3013043]: http://support.microsoft.com/kb/3013043
[3012712]: http://support.microsoft.com/kb/3012712
[3004905]: http://support.microsoft.com/kb/3004905
[3004394]: http://support.microsoft.com/kb/3004394
[2999323]: http://support.microsoft.com/kb/2999323
[3013488]: http://support.microsoft.com/kb/3013488
[3012325]: http://support.microsoft.com/kb/3012325
[3007054]: http://support.microsoft.com/kb/3007054
[2999802]: http://support.microsoft.com/kb/2999802
[2896881]: http://support.microsoft.com/kb/2896881

[3032359]: http://support.microsoft.com/kb/3032359
[3040297]: http://support.microsoft.com/kb/3040297
[3041836]: http://support.microsoft.com/kb/3041836
[3032323]: http://support.microsoft.com/kb/3032323
[3034344]: http://support.microsoft.com/kb/3034344
[3035132]: http://support.microsoft.com/kb/3035132
[3038680]: http://support.microsoft.com/kb/3038680
[3002657]: http://support.microsoft.com/kb/3002657
[3035126]: http://support.microsoft.com/kb/3035126


[arquivo]: https://msdn.microsoft.com/library/azure/dn391773.aspx
[família-explicação]: cloud-services-guestos-update-matrix.md#guest-os-family-version-and-release-explanation


<!--HONumber=52--> 