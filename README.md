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

Ele é separado em 2 partes, a função e o array de dependências. A função é o que será executado após o efeito ser disparado. Já o array de depêndencias, é onde passa as variáveis que define quando o useEffect será reexecutado, ou seja, quando o valor da depedência alterar, será reexecutado. Caso o array esteja vazio,  o efeito será executado apenas uma vez, após a primeira renderização.

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

