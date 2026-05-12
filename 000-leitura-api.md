//core.ts:

````
// Caminho do arquivo onde os dados são salvos
const jsonFilePath = __dirname + '/data.temp.json';

// Carrega os itens do arquivo assim que o módulo é iniciado
const list: string[] = await loadFromFile();

async function loadFromFile() {
  try {
    const file = Bun.file(jsonFilePath);     // Abre o arquivo
    const content = await file.text();       // Lê o conteúdo como texto
    return JSON.parse(content) as string[];  // Converte o JSON para array
  } catch (error: any) {
    if (error.code === 'ENOENT')  // Se o arquivo não existe ainda, retorna array vazio
      return [];
    throw error;  // Qualquer outro erro, relança
  }
}

async function saveToFile() {
  try {
    // Converte o array para JSON e salva no arquivo
    await Bun.write(jsonFilePath, JSON.stringify(list));
  } catch (error: any) {
    throw new Error("Erro ao salvar os dados no arquivo: " + error.message);
  }
}

async function addItem(item: string) {
  list.push(item);    // Adiciona o item na memória
  await saveToFile(); // Salva no arquivo
}

async function getItems() {
  return list; // Retorna todos os itens
}

async function updateItem(index: number, newItem: string) {
  if (index < 0 || index >= list.length)  // Verifica se o index existe
    throw new Error("Index fora dos limites");
  list[index] = newItem;  // Substitui o item
  await saveToFile();
}

async function removeItem(index: number) {
  if (index < 0 || index >= list.length)  // Verifica se o index existe
    throw new Error("Index fora dos limites");
  list.splice(index, 1);  // Remove 1 elemento na posição index
  await saveToFile();
}

// Exporta as funções para serem usadas em outros arquivos
export default { addItem, getItems, updateItem, removeItem };
````

//apiturma2:


````
import todo from "./core.ts";

// Cria e inicia o servidor na porta 3000
const server = Bun.serve({
  port: 3000,
  routes: {
  
    // Rota raiz — serve o HTML da página
    "/": new Response(Bun.file("./public/index.html")),

    "/api/todo": {
      // Retorna todos os itens da lista
      GET: async () => {
        const items = await todo.getItems()
        return Response.json(items)
      },

      // Adiciona um novo item
      POST: async (req) => {
        const data = await req.json() as any;
        const item = data.item || null;
        if (!item)
          return Response.json('Por favor, forneça um item para adicionar.', { status: 400 });
        await todo.addItem(item);
        return Response.json(data);
   },
  },

    // Rotas que recebem um index na URL (ex: /api/todo/2)
    "/api/todo/:index": {

      // Atualiza o item no index informado
      PUT: async (req) => {
        const index = parseInt(req.params.index); // Converte o index de string para número
        if (isNaN(index))  // Se não for um número válido, rejeita
          return Response.json('Índice inválido. um número inteiro é esperado.', { status: 400 });
        const data = await req.json() as any;
        const newItem = data.newItem || null;
        if (!newItem)
          return Response.json('Por favor, forneça um novo item para atualizar.', { status: 400 });
        try {
          await todo.updateItem(index, newItem);
          return Response.json(`Item no índice ${index} atualizado para "${newItem}".`);
        } catch (error: any) {
          return Response.json(error.message, { status: 400 });
        }
      },

      // Remove o item no index informado
      DELETE: async (req) => {
        const index = parseInt(req.params.index);
        if (isNaN(index))
          return Response.json('Índice inválido.', { status: 400 });
        try {
          await todo.removeItem(index);
          return Response.json(`Item no índice ${index} removido com sucesso.`);
        } catch (error: any) {
          return Response.json(error.message, { status: 400 });
        }
      },
    },

    // EXEMPLO BÁSICO
    "/api/exemplo": {
      // Retorna o timestamp atual
      GET: () => {
        return new Response(`Esse é o exemplo: ${Date.now()}`)
      },

      // Recebe um JSON e devolve ele com a data de hoje adicionada
      POST: async (req) => {
        const data = await req.json() as any;
        data.recebidoEm = new Date().toLocaleDateString("pt-BR");
        return Response.json(data);
      },
    },

    "/api/exemplo/:id": {
      // Atualiza um recurso — devolve o body com o id e a data
      PUT: async (req, params) => {
        const { id } = req.params;
        const data = await req.json() as any;
        data.id = id;
        data.recebidoEm = new Date().toLocaleDateString("pt-BR");
        return Response.json(data);
      },

      // Atualização parcial — além do id e data, lista quais chaves foram enviadas
      PATCH: async (req, params) => {
        const { id } = req.params;
        const data = await req.json() as any;
        data.chavesAtualizadas = Object.keys(data); // Pega os nomes das propriedades do objeto
        data.id = id;
        data.atualizadoEm = new Date().toLocaleDateString("pt-BR");
        return Response.json(data);
      },

      DELETE: (req, params) => {
        const { id } = req.params;
        return new Response(`Recurso com id ${id} deletado`, { status: 200 });
      }
    }
    // FIM DO EXEMPLO BÁSICO

  },

  // Qualquer rota não definida acima cai aqui e retorna 404
  async fetch(req) {
    return new Response(`Not Found`, { status: 404 });
  },
});

console.log(`Server running at http://localhost:${server.port}`);
