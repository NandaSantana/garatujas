# garatujas

ATV1-P1

let list = await loadFromFile(): quando o programa abre, ele já lê o arquivo JSON e carrega todas as tarefas na memória antes de qualquer comando ser executado.

Math.max(...list.map(t => t.id)): percorre a lista pegando só os IDs, depois encontra o maior entre eles. O +1 no final garante que o próximo ID nunca vai colidir com um já existente.

.map((task, index) => ({ task, index })).filter(...): como o .filter() sozinho perderia a posição original da tarefa, o .map() antes "gruda" o índice no objeto. Assim o resultado da busca mostra o número certo pra usar em outros comandos como remove ou complete

String(index).padStart(2, '0'): transforma 1 em 01, 2 em 02 e assim por diante. Faz todos os números da lista ocuparem o mesmo espaço, deixando as colunas alinhadas no terminal.

`\x1b[9m${task.description}\x1b[0m: o \x1b[9m ativa o estilo tachado no terminal e o \x1b[0m desliga tudo depois. O resultado é o texto da tarefa aparecer riscado quando ela está concluída.

ATV1-P2

items armazenando uma Promise => private items: Promise<Item[]>: em vez de guardar o array direto, guarda a Promise que vai resolver nele. Isso permite que o carregamento do arquivo comece no construtor e qualquer método que precise dos dados só espera ela terminar com await this.items.

toggleCompleted => this.completed = !this.completed: inverte o estado da tarefa. Se estava true vira false e vice-versa, sem precisar verificar o valor atual.

.map reconstruindo objetos ao carregar JSON.parse(data).map((itemData) => new Item(itemData.description, itemData.completed)):  o arquivo salva dados brutos, então ao ler é necessário transformar cada objeto de volta em uma instância real da classe Item, com todos os métodos disponíveis.

ATV1-P3

Bun.serve: Sobe um servidor HTTP real na porta 3000. Toda requisição que chegar cai na função fetch, que recebe o request e decide o que fazer.

const startIndex = (page - 1) * limit e itemsData.slice(startIndex, endIndex): calcula qual fatia do array mostrar. Também devolve totalPages, hasNextPage e hasPrevPage pra quem consome a API saber navegar.

Cada bloco de rota tem seu próprio try/catch, então um erro no DELETE não derruba o servidor inteiro nem interfere nas outras rotas.

Escrevi tudo que eu esquecia a função, em grande maioria.
