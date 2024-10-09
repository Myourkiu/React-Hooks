# React Hooks

## O que são os React Hooks?
Os React Hooks são funções que permitem o uso de estados e outras funcionalidades sem a escrita de classes, como eram feitas antigamente. Sendo os principais deles:
- useState
- useEffect 
- useContext
- useReducer
- useRef
- useMemo
- useCallback

Cada um possui sua função e forma de lidar com as funcionalidades do React. Nos próximos tópicos, mostrará o nome e o uso de cada um deles.

## useState

O useState permite a adição e gerenciamento de estados em componentes funcionais. Em seu uso, criará uma variável, onde possuirá o primeiro valor (value), que será o estado atual da variável e no segundo valor (setValue), que é a função que será chamada para a adição/atualização de estado. No parâmetro da função useState(), será passado o valor inicial.

```
const [value, setValue] = useState(0)
console.log(value) //0

setValue(10)
console.log(value) //10
```

Pode-se também passar o tipo do useState, que é uma prática comum em ambiente de desenvolvimento.

```
const [value, setValue] = useState<number>(0)
```

## useEffect

O useEffect é utilizado para operar em situações que buscam ambientes fora do componente React, como chamadas a API's. Por padrão, ele é iniciado após a renderização do componente e após cada atualização do mesmo. 

### Explicação da sintaxe:

#### Função:
A função é o que será executado após o efeito ser disparado.

#### Array de dependências:
Já o array de depêndencias, é onde passa as variáveis que define quando o useEffect será reexecutado, ou seja, quando o valor da depedência alterar, será reexecutado. Caso o array esteja vazio,  o efeito será executado apenas uma vez, após a primeira renderização.

```
const [repos, setRepos] = useState([]);
const githubUrl = 'https://api.github.com/users/Myourkiu/repos'

useEffect(() => {
    const fetchRepos = async () => {
      try {
        const response = await axios.get(githubUrl);
        setRepos(response.data);
      } 
      catch (error) {
        console.error(error);
      }
    };

    fetchRepos();
  }, []);
```

## useContext

O useContext é utilizado para criar um contexto que permite compartilhar valores entre componentes sem acontecer o 'prop drilling', que seria passar props por múltiplos níveis de componentes que não precisam utilizá-la diretamente. Ele geralmente é usado para gerenciar estados globais.

### Explicação da sintaxe:

#### Contexto: 
Define um valor que pode ser acessado por qualquer componente dentro da sua árvore. Criado pela função React.createContext().

#### Provider:
Responsável por fornecer o valor do contexto para os componentes, passando via prop 'value'.

#### useContext:
Utilizado dentro de um componente para acessar o valor fornecido pelo provider. Em seu parâmetro, é passado o contexto necessário.

##### Componente de contexto:
```
const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

##### Utilização do useContext:

```
const ThemeToggleButton = () => {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div
      className={`p-5 ${
        theme === 'light'
          ? 'bg-white text-black'
          : 'bg-gray-800 text-white'
      }`}>
      <button
        className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-700"
        onClick={toggleTheme}>
        Alternar tema
      </button>
    </div>
  );
};
```

##### Envolvendo o componente com provider:
```
const App = () => {
  return (
    <ThemeProvider>
      <ThemeToggleButton />
    </ThemeProvider>
  );
};
```

Utilizando o useContext, seu código fica mais limpo e mais organizado, facilitando o gerenciamento de estados globais e escalamento do código.

## useReducer

O useReducer é uma alternativa do useState, porém trabalhando com mais de um estado, ou seja, em situações mais complexas, onde precisa manipular vários estados, o useReducer pode ser uma ótima alternativa.

### Explicação da sintaxe:
Este possui uma sintaxe um pouco mais avançada. Sua escrita pode ser feita da seguinte forma:

```
const [state, dispatch] = useReducer(reducer,{});
```
#### State:
Este comumente é um conjunto de estados, como são vários, este geralmente vai ser um objeto.

#### Dispatch:
Este é a função que vai ser executada de acordo com cada state.

#### Reducer:
Função onde que vai indicar o que vai acontecer a cada dispatch.

#### {}
Este objeto é onde serão passados os states iniciais.

### Função reducer:
Utilizando um exemplo genérico as ações vão ser diminuir e aumentar um count:
```
const reducer = (state, action) => {
  switch(action.type){
    case "incrementCount" :
      return {...state, state.count + 1};
    case "decreamentCount" : 
      return {...state, state.count - 1};
    default:
      return "mensagem de erro"
  }
}
```

### Chamando o useReducer:
Será chamado a função dispatch e o tipo da ação, lembrando que ela precisa ser o mesmo nome da função reducer.

```
//Botão de aumentar o contador

<button onClick={() => dispatch({type: "incrementCount"})}>+</button>

//Botão de diminuir o contador

<button onClick={() => dispatch({type: "decreamentCount"})}>+</button>
```

## useRef

O useRef é um criador de referência mutável, ou seja, mesmo após renderizações, ele mantém seu valor. Geralmente usado para manipular elementos DOM, manter um valor entre renderizações sem renderizar o componente novamente e armazenar timeouts e intervalos.

Sua sintaxe é simples, onde criará uma variável e seu parâmetro vai ser o valor inicial.



```
const myRef = useRef(null);
```

#### Considerações importantes:
- O useRef retorna um objeto com a prop '.current', que pode ser alterada sem causar uma nova renderização.

- É ideal para guardar valores que devem persistir ao longo das renderizações, mas que não precisam atualizar a interface diretamente.

- Todos os elementos HTML possuem uma prop 'ref' que pode ser usado para vincular a referência.

- Diferente do useState, o useRef não causa re-renderizações quando se é alterado.

### Manipulação de elementos DOM:

Neste exemplo, o botão terá uma função que, ao clicar, focará no input.

```
(...)
const inputRef = userRef<HTMLInputElement>(null);

const handleFocus = () => {
  if(inputRef.current){
    inputRef.current.focus();
  }

  return (
    (...)
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>Focus</button>
  )
}
```

### Armazenar um valor entre renderizações:

Neste caso, o useRef vai servir para armazenar os valores entre renderizações da atualização de cada segundo do timer.

```
const [count, setCount] = useState(0); 
  const countRef = useRef(0);

  useEffect(() => {
    const interval = setInterval(() => {
      countRef.current += 1; 
      setCount(countRef.current); 
    }, 1000);

    return () => clearInterval(interval);
  }, []);
```
Ao mudar a variável 'count', o componente vai renderizar novamente e mesmo assim, o valor do useRef será mantido.

### Armazenando intervalos e timeouts:

No exemplo, o userRef vai armazenar referências de intervalos num cronômetro.

```
const intervalRef = useRef<number | null>(null);
  const [time, setTime] = useState(0);

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setTime(prevTime => prevTime + 1);
    }, 1000);

    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current); 
      }
    };
  }, []);
```
Neste caso, o intervalo está sendo armazenado dentro de um ref, mesmo após as renderizações.
<br/>
<br/>

# Algoritmo de Reconciliação no React

**Sugestão:** Ler a documentação oficial de [Reconciliação no React](https://pt-br.legacy.reactjs.org/docs/reconciliation.html) para informações mais precisas e certas sobre esta parte.

### O que é Renderização?
É um processo de três etapas, onde na primeira etapa, o HTML é criado. Na segunda etapa, verifica se existe uma diferença entre o HTML anterior e o atual (se não for a primeira vez que o componente está rodando). Na terceira etapa, caso exista mudanças percebidas na etapa anterior, aplica o algoritmo de renderização.

### Como funciona a Reconciliação?

É importante saber como funciona a segunda etapa de renderização mais precisamente. O React possui uma versão do que está exibido em tela e uma versão chamada de 'Virtual DOM', onde estão as mudanças feitas a partir do HTML anterior.

O algoritmo de reconciliação vai aplicar o que é chamado de 'diffing', que é a análise entre o DOM atual e o Virtual DOM. Caso exista uma diferença entre as duas árvores, esse algoritmo vai trocar apenas o que foi alterado, ao invés de destruir e recriar a parte modificada.

Exemplo:

```
//DOM anterior
<div className="bg-green-500"></div>

//DOM atual
<div className="bg-red-500"></div>
```

Ao chegar no diffing, vai ser percebido que ambas divs são iguais, o que mudou foi apenas a cor da div, logo a única coisa que irá ser destruído e replicado com as alterações vai ser a palavra 'green', que será trocado por 'red', sem a necessidade de destruir a div por inteiro e substituir para a nova.

### Por que saber como funciona a Renderização?

Esta etapa é importante para saber quando usar cada hook, principalmente o useMemo e useCallback, pois são hooks visados para a performance. O problema destes hooks, é que se aplicados errôneamente, podem causar atrasos na aplicação ao invés de otimizar.

# Shallow Compare
### O que é Shallow Compare?
É a técnica que o React usa na comparação de objetos e arrays em que apenas os valores das referências de suas props são comparados. Ou seja, basicamente, se a referência de um objeto ou array dentro de uma estrutura mudar, o React vai entender que o objeto inteiro mudou, mesmo que o conteúdo interno seja o mesmo.

### Exemplificando o Shallow Compare

```
const objA = { name: 'Anna' };
const objB = { name: 'Anna' };

console.log(objA === objB); // false
```
Isso acontece pois, mesmo que os dois objetos tenham uma prop name com o mesmo valor, a comparação === resulta em false, já que ambos possuem espaços diferentes na memória, ou seja, referências diferentes. Isso é o Shallow Comparison.

Caso o código seja montado da seguinte forma:

```
const objA = { name: 'Anna' };
const objB = objA;

console.log(objA === objB); // true
```
Agora o resultado da comparação é true, pois apontam para a mesma referência.

### Relação com o useMemo e useCallback

Ambos dependem do Shallow Compare para decidirem se irão recalcular ou não. As dependências passadas são comparadas pelo Shallow Comparison e, caso uma referência delas mude, o hook vai ser executado novamente.

### Qual a sua importância?

Ele é importante para saber como e quando otimizar seu código, evitando que os componentes seja re-renderizados desnecessariamente.

# Hooks de otimização

## useMemo

O useMemo é um hook de performance, onde ele evita re-cálculos desnecessários no seu código com base em uma depedência.

### Explicação da sintaxe:
Dentro do escopo do useMemo, retornará a função que vai ser executada e no array, a dependência com a qual vai ser disparada a função.
```
useMemo(() => {
  return () => void
}, [])
```
### Exemplificando seu uso:
Neste exemplo, supondo que tenha uma lista de itens e tenha uma função de adicionar itens nela.

```
(...)

const [list, setList] = useState<string[]>([]);
const [newItem, setNewItem] = useState<string>('');

function addItemToList(){
  setList([...list, newItem])
}

return(
(...)
  <input onChange={e => setNewItem(e.target.value) }value={newItem}/>

  <button onClick={addItemToList}>Add Item</button>
)
```
Suponha que precise ter um filtro de items que possuam a letra 'a'.

```
const itemsWithA = list.filter(item => item.includes('a'));
```

Porém, como está vinculado a um useState, todas as vezes que o usuário digitar algo ou adicionar um item na lista, o componente vai re-renderizar, fazendo o cálculo novamente.

Caso a lista seja grande, ao ficar re-renderizando consecutivamente o componente, irá ter perda de performance na aplicação. Para evitar isso, basta adicionar o useMemo.

```
const itemsWithA = useMemo(() => {
  return list.filter(item => item.includes('a'));
},[list])
```
Agora a função só será chamada quando a lista tiver alguma alteração, pois o useMemo vai ficar escutando a lista para ver se ela possui alguma alteração entre a lista anterior e a lista em Virtual DOM.

## useCallback
O useCallback retorna uma função que só será recriada após pelo menos uma de suas dependências mudar, útil para evitar a recriação de funções desnecessárias.

### Explicação da sintaxe:
Dentro do escopo, retornará a função que será memorizada e um array de dependências.

```const memorizedFunction = useCallback(() => {
  return () => void
},[])
```
### Exemplificando seu uso:

Neste exemplo, supondo que tenha uma função de dobrar o valor do contador.

```
const Buttons = () => {
  const [count, setCount] = useState(0);

  const doubleCount = () => {
    setCount(count * 2);
  };

  return (
    (...)
      <button onClick={() => setCount(count + 1)}>
        +1
      </button>
      
      <button onClick={doubleCount}>Double Count</button>

    (...)
  );
};
```

Neste caso, toda vez que o componente pai renderizasse, a função doubleCount seria recriada, causando renderizações desnecessárias.

Para evitar isso, adicionaria o useCallBack na função doubleCount.

```
const doubleCount = useCallback(() => {
    setCount((prevCount) => prevCount * 2);
}, [count]);
```

Agora com a função doubleCount memorizada, só será recriada apenas na mudança do count.

## Por que não usar o useMemo e useCallback em todos os casos?

 Ambos efetuam uma comparação entre a dependência anterior e a que está em Virtual DOM. Porém, em casos simples, não há necessidade de fazer essa comparação, pois possibilidade da re-renderização ser mais rápida do que a comparação em si é grande. Ou seja, provavelmente o custo da memorização seria maior do que simplesmente recriar a função.