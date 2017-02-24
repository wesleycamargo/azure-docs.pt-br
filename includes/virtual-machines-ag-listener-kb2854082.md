A seguir, se algum servidor no cluster estiver executando o Windows Server 2008 R2 ou o Windows Server 2012, você deverá verificar se o hotfix [KB2854082](http://support.microsoft.com/kb/2854082) está instalado em cada um dos servidores locais ou nas VMs do Azure que fazem parte do cluster. Qualquer servidor ou VM que esteja no  cluster, mas não no grupo de disponibilidade, também deverá ter esse hotfix instalado.

Na sessão da área de trabalho remota para cada um de nós do cluster, baixe [KB2854082](http://support.microsoft.com/kb/2854082) para um diretório local. Em seguida, instale o hotfix em cada um dos nós do cluster, em sequência. Se o serviço do cluster está sendo executado no nó do cluster, o servidor é reiniciado no final da instalação do hotfix.

> [!WARNING]
> Parar o serviço de cluster ou reiniciar o servidor afeta a integridade de quorum do seu cluster e o grupo de disponibilidade, e pode fazer com que o cluster fique offline. Para manter a alta disponibilidade do seu cluster durante a instalação, verifique se:
> 
> * O cluster está na integridade de quorum ideal, 
> * Todos os nós de cluster estão online antes de instalar o hotfix em qualquer nó, e
> * Permite a instalação do hotfix para executar até a conclusão em um nó, incluindo reinicialização completa do servidor, antes de instalar o hotfix em qualquer outro nó do cluster.
> 
> 



<!--HONumber=Nov16_HO3-->


