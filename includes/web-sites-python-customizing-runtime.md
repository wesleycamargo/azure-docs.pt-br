O Azure determinará a versão do Python a usar para seu ambiente virtual com a seguinte ordem de prioridade:

1. versão especificada no runtime.txt na pasta raiz
1. versão especificada pela definição de Python na configuração do site (a página CONFIGURAR no Portal do Azure)
1. Python 2.7 é o padrão, se nenhuma das opções acima for especificada

Os valores válidos para o conteúdo de 

    \runtime.txt

são:

- python-2.7
- python-3.4

Se a versão micro (terceiro dígito) for especificada, ela será ignorada.


<!--HONumber=52--> 
