# garatujas

ATV1-P1

let list = await loadFromFile(): quando o programa abre, ele já lê o arquivo JSON e carrega todas as tarefas na memória antes de qualquer comando ser executado.

Math.max(...list.map(t => t.id)): percorre a lista pegando só os IDs, depois encontra o maior entre eles. O +1 no final garante que o próximo ID nunca vai colidir com um já existente.

.map((task, index) => ({ task, index })).filter(...): como o .filter() sozinho perderia a posição original da tarefa, o .map() antes "gruda" o índice no objeto. Assim o resultado da busca mostra o número certo pra usar em outros comandos como remove ou complete

String(index).padStart(2, '0'): transforma 1 em 01, 2 em 02 e assim por diante. Faz todos os números da lista ocuparem o mesmo espaço, deixando as colunas alinhadas no terminal.

`\x1b[9m${task.description}\x1b[0m: o \x1b[9m ativa o estilo tachado no terminal e o \x1b[0m desliga tudo depois. O resultado é o texto da tarefa aparecer riscado quando ela está concluída.
