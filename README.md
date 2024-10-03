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
Este objeto é onde serão passados os states inciais.

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