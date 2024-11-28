# Table des Matières

1. [Présentation](#présentation)
2. [Installation](#installation)
3. [Hooks](#hooks)
   - [useState](#usestate)
   - [useEffect](#useeffect)
   - [useMemo](#usememo)
   - [useRef](#useref)
   - [Hook personnalisé](#hook-personnalise)
   - [useCallback](#usecallback)
   - [useReducer](#usereducer)
   - [useContext](#usecontext)
   - [useId](#useid)
   - [Autres hooks](#autres-hooks)
4. [React pour les cas spéciaux](#react-pour-les-cas-speciaux)
   - [Portails (Portals)](#portails-portals)
   - [Gestion des erreurs (ErrorBoundary)](#gestion-des-erreurs-errorboundary)
   - [Suspense & lazy](#suspense--lazy)
5. [Gestion d'état global](#gestion-detat-global)
   - [Zustand](#zustand)
   - [Redux Toolkit](#redux-toolkit)
6. [Écosystème de librairies](#ecosysteme-de-librairies)
   - [React Router DOM](#react-router-dom)
   - [React Query](#react-query)
   - [React Hook Form](#react-hook-form)
7. [Rendu côté serveur](#rendu-cote-serveur)
   - [Next.js](#nextjs)
   - [Remix](#remix)
8. [TypeScript](#typescript)
9. [Tests](#tests)

## Présentation

Cette formation ReactJS a pour objectif de fournir des conseils clés pour maîtriser ReactJS et d'enseigner les concepts essentiels nécessaires au développement d'applications React performantes.

**Objectifs**

- Apprenez à créer des applications React 18+ évolutives en utilisant les outils et techniques les plus récents de l'écosystème React.
- Optimisez les performances grâce au découpage du code et au rendu côté serveur (SSR).
- Intégrez TypeScript pour un typage strict et un développement plus fiable.
- Gérez efficacement l'état global de vos applications.
- Assurez la fiabilité de votre application en la testant avec Vitest.

#### Prérequis pour suivre la formation:

Notion de base de React :

- Virtual DOM
- Composants
- JSX
- State
- Props

## Installation

- Voir la doc de vite => https://vite.dev/guide/#scaffolding-your-first-vite-project

## Hooks

### useState

#### **1. Définition**

Le hook `useState` est une fonctionnalité de React qui permet de gérer l'état local d'un composant fonctionnel. Avant l'introduction des hooks, seuls les composants de classe pouvaient avoir un état interne. Avec `useState`, les composants fonctionnels peuvent aussi gérer des données dynamiques.

#### **2. Dans quel cas l'utiliser ?**

- Lorsque vous avez besoin de suivre des données dynamiques dans un composant React.
- Pour gérer des éléments d'interface utilisateur tels que :
  - Les entrées d'un formulaire.
  - Le suivi d'une valeur comme un compteur.
  - L'état d'une modale ouverte ou fermée.
  - Les données d'une liste ou d'un tableau qui changent avec des interactions.

#### **3. Exemples**

##### **Exemple 1: Gestion d'un compteur**

Voici comment utiliser `useState` pour créer un compteur simple :

```jsx
import React, { useState } from "react";

const Counter = () => {
  const [count, setCount] = useState(0); // Déclare une variable d'état "count" initialisée à 0

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={() => setCount(count + 1)}>+ Increment</button>
      <button onClick={() => setCount(count - 1)}>- Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
};

export default Counter;
```

##### **Exemple 2: Gestion d'un champ de formulaire**

```jsx
import React, { useState } from "react";

const Form = () => {
  const [name, setName] = useState(""); // État initial vide

  return (
    <div>
      <h1>Hello, {name ? name : "Guest"}!</h1>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
    </div>
  );
};

export default Form;
```

##### **Exemple 3: Gestion de l'état d'une modale**

```jsx
import React, { useState } from "react";

const Modal = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(!isOpen)}>
        {isOpen ? "Close Modal" : "Open Modal"}
      </button>
      {isOpen && (
        <div style={{ border: "1px solid black", padding: "10px" }}>
          <h2>Modal</h2>
          <p>This is an open modal.</p>
          <button onClick={() => setIsOpen(false)}>Close</button>
        </div>
      )}
    </div>
  );
};

export default Modal;
```

### useEffect

#### **1. Définition**

Le hook `useEffect` permet de gérer les effets secondaires dans un composant React. Un effet secondaire est une action qui survient à la suite d'une interaction ou d'un changement d'état, par exemple :

- Manipuler le DOM.
- Appeler une API.
- Mettre à jour l'état après une certaine condition.
- Souscrire à des événements (par exemple, `resize` ou `scroll`).

Il remplace les méthodes de cycle de vie des composants de classe comme `componentDidMount`, `componentDidUpdate`, et `componentWillUnmount`.

#### **2. Dans quel cas l'utiliser ?**

- Lorsque vous devez effectuer des actions après un rendu du composant.
- Pour interagir avec des API ou des services externes (ex: récupérer des données avec `fetch`).
- Pour souscrire ou nettoyer des ressources, par exemple, en attachant des écouteurs d'événements ou en manipulant des timers.
- Pour effectuer des calculs basés sur des changements d'état ou de props.

#### **3. Exemples**

##### **Exemple 1: Récupération de données depuis une API**

```jsx
import React, { useState, useEffect } from "react";

const DataFetcher = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts"
      );
      const result = await response.json();
      setData(result);
      setLoading(false);
    };
    fetchData();
  }, []); // Le tableau vide [] signifie que cet effet se déclenche une seule fois après le premier rendu.

  return (
    <div>
      {loading ? <p>Loading...</p> : <pre>{JSON.stringify(data, null, 2)}</pre>}
    </div>
  );
};

export default DataFetcher;
```

##### **Exemple 2: Souscription à un événement et nettoyage**

```jsx
import React, { useState, useEffect } from "react";

const WindowResizeListener = () => {
  const [windowWidth, setWindowWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => {
      setWindowWidth(window.innerWidth);
    };

    window.addEventListener("resize", handleResize);

    // Nettoyage de l'événement à la désactivation du composant
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // L'effet s'exécute une seule fois, lors du montage du composant

  return (
    <div>
      <h1>Window Width: {windowWidth}px</h1>
    </div>
  );
};

export default WindowResizeListener;
```

##### **Exemple 3: Mise à jour d'un titre de page en fonction de l'état**

```jsx
import React, { useState, useEffect } from "react";

const PageTitleUpdater = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`; // Mise à jour du titre de la page

    // Aucun nettoyage n'est nécessaire ici, car l'effet ne nécessite pas de nettoyage
  }, [count]); // L'effet se déclenche chaque fois que 'count' change

  return (
    <div>
      <h1>You've clicked {count} times</h1>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
};

export default PageTitleUpdater;
```

### useMemo

#### **1. Définition**

Le hook `useMemo` permet de mémoriser le résultat d'une fonction de calcul afin d'éviter de la recalculer inutilement lors de chaque rendu. Il est utilisé principalement pour optimiser les performances de React en évitant les calculs coûteux qui ne changent pas souvent.  
`useMemo` prend une fonction de calcul et un tableau de dépendances (comme pour `useEffect`). La fonction ne sera exécutée que lorsque les valeurs des dépendances changent.

#### **2. Dans quel cas l'utiliser ?**

- Lorsque vous avez une fonction qui effectue un calcul coûteux ou complexe et que vous voulez éviter de la recalculer à chaque rendu.
- Pour éviter les re-rendus inutiles dans des composants enfants lorsque les props ne changent pas.
- Pour mémoriser un objet ou un tableau qui pourrait être utilisé dans un autre hook ou un composant enfant afin d'éviter sa re-création à chaque rendu.

#### **3. Des exemples concrets de code pour chaque cas ?**

##### **Exemple 1:**

```jsx
import React, { useMemo, useState } from "react";

const App = () => {
  const [count, setCount] = useState(0);
  const [todos, setTodos] = useState([]);
  const calculation = useMemo(() => expensiveCalculation(count), [count]);

  const increment = () => {
    setCount((c) => c + 1);
  };
  const addTodo = () => {
    setTodos((t) => [...t, "New Todo"]);
  };

  return (
    <div>
      <div>
        <h2>My Todos</h2>
        {todos.map((todo, index) => {
          return <p key={index}>{todo}</p>;
        })}
        <button onClick={addTodo}>Add Todo</button>
      </div>
      <hr />
      <div>
        Count: {count}
        <button onClick={increment}>+</button>
        <h2>Expensive Calculation</h2>
        {calculation}
      </div>
    </div>
  );
};

export default App;

const expensiveCalculation = (num) => {
  console.log("Calculating...");
  for (let i = 0; i < 1000000000; i++) {
    num += 1;
  }
  return num;
};
```

##### **Exemple 2:**

```jsx
//App.jsx
import { useState } from "react";
import { createTodos } from "./utils.js";
import TodoList from "./TodoList.js";

const todos = createTodos();

export default function App() {
  const [tab, setTab] = useState("all");
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <button onClick={() => setTab("all")}>Toutes</button>
      <button onClick={() => setTab("active")}>Actives</button>
      <button onClick={() => setTab("completed")}>Terminées</button>
      <br />
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={(e) => setIsDark(e.target.checked)}
        />
        Mode sombre
      </label>
      <hr />
      <TodoList todos={todos} tab={tab} theme={isDark ? "dark" : "light"} />
    </>
  );
}
```

```jsx
//TodoList.js

import { useMemo } from "react";
import { filterTodos } from "./utils.js";

export default function TodoList({ todos, theme, tab }) {
  const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
  return (
    <div className={theme}>
      <p>
        <b>
          Remarque : <code>filterTodos</code> est artificiellement ralentie !
        </b>
      </p>
      <ul>
        {visibleTodos.map((todo) => (
          <li key={todo.id}>
            {todo.completed ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```js
// utils.js

export function createTodos() {
  const todos = [];
  for (let i = 0; i < 50; i++) {
    todos.push({
      id: i,
      text: "Tâche " + (i + 1),
      completed: Math.random() > 0.5,
    });
  }
  return todos;
}

export function filterTodos(todos, tab) {
  console.log(
    "[ARTIFICIELLEMENT LENT] Filtrage de " +
      todos.length +
      " tâches pour l’onglet « " +
      tab +
      " »."
  );
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Ne rien faire pendant 500 ms pour simuler du code extrêmement lent
  }

  return todos.filter((todo) => {
    if (tab === "all") {
      return true;
    } else if (tab === "active") {
      return !todo.completed;
    } else if (tab === "completed") {
      return todo.completed;
    }
  });
}
```

### useRef

#### **1. Définition**

Le hook `useRef` permet de créer une référence mutable qui persiste pendant toute la durée de vie du composant. Contrairement à `useState`, la valeur stockée dans `useRef` ne déclenche pas un nouveau rendu du composant lorsque celle-ci est modifiée.  
`useRef` est couramment utilisé pour :

- Accéder directement à un élément du DOM.
- Conserver une valeur mutable qui ne doit pas provoquer de rendu.

#### **2. Dans quel cas l'utiliser ?**

- **Accéder aux éléments du DOM** : Lorsque vous souhaitez interagir directement avec un élément du DOM (par exemple, pour le focus, la lecture ou l'écriture de la valeur).
- **Conserver une valeur entre les rendus** : Pour stocker une valeur qui ne doit pas provoquer de re-rendu, comme un timer ou un identifiant d'intervalle.
- **Références aux composants enfants** : Si vous devez manipuler des composants enfants en dehors de leur cycle de vie habituel.
- **Optimisation des performances** : Pour éviter des mises à jour inutiles de l'état dans des cas spécifiques où l'on souhaite seulement conserver une valeur mutable.

#### **3. Des exemples concrets de code pour chaque cas ?**

#### **Exemple 1: Conserver une valeur mutable sans déclencher de re-rendu**

```jsx
import React, { useEffect, useRef } from "react";

const Timer = () => {
  const timerRef = useRef(0); // Stocke la valeur mutable du compteur de temps
  const [seconds, setSeconds] = React.useState(0);

  useEffect(() => {
    // Démarrer un intervalle pour mettre à jour le timer
    const intervalId = setInterval(() => {
      timerRef.current += 1; // Met à jour la valeur de `timerRef` sans déclencher de re-rendu
      setSeconds(timerRef.current); // Met à jour l'état pour afficher le nombre de secondes
    }, 1000);

    // Nettoyage de l'intervalle lors du démontage du composant
    return () => clearInterval(intervalId);
  }, []);

  return (
    <div>
      <h1>Timer: {seconds}s</h1>
    </div>
  );
};

export default Timer;
```

#### **Exemple 2: Activer un champ**

```jsx
import { useRef } from "react";

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>Activer le champ de saisie</button>
    </>
  );
}
```

#### **Exemple 3: Exposer une ref vers votre composant**

```jsx
import { forwardRef, useRef } from "react";

const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>Activer le champ de saisie</button>
    </>
  );
}
```

#### **Exemple 4: Défiler jusqu’à une image**

```jsx
import { useRef } from "react";

export default function CatFriends() {
  const listRef = useRef(null);

  function scrollToIndex(index) {
    const listNode = listRef.current;
    // Cette ligne suppose une structure DOM bien définie :
    const imgNode = listNode.querySelectorAll("li > img")[index];
    imgNode.scrollIntoView({
      behavior: "smooth",
      block: "nearest",
      inline: "center",
    });
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToIndex(0)}>Neo</button>
        <button onClick={() => scrollToIndex(1)}>Millie</button>
        <button onClick={() => scrollToIndex(2)}>Bella</button>
      </nav>
      <div>
        <ul ref={listRef} styles={{ width: "200px", diplayL 'flex', overflow: 'hidden' }}>
          <li style={{ display: "iniline" }}>
            <img src="https://placecats.com/neo/300/200" alt="Neo" />
          </li>
          <li style={{ display: "iniline" }}>
            <img src="https://placecats.com/millie/200/200" alt="Millie" />
          </li>
          <li style={{ display: "iniline" }}>
            <img src="https://placecats.com/bella/199/200" alt="Bella" />
          </li>
        </ul>
      </div>
    </>
  );
}
```

### Hook personnalisé

#### **Exemple 1: Un hook personnalisé pour gérer des données dans le localStorage du navigateur**

```jsx
import { useState } from "react";

function useLocalStorage(key, initialValue) {
  // Retrieve the stored value from localStorage
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error("Error accessing localStorage:", error);
      return initialValue;
    }
  });

  // Update localStorage whenever the value changes
  const setValue = (value) => {
    try {
      const valueToStore =
        value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error("Error setting localStorage value:", error);
    }
  };

  return [storedValue, setValue];
}

export default useLocalStorage;

// Usage example
/*
const [theme, setTheme] = useLocalStorage("theme", "light");

<button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
  Toggle Theme
</button>
*/
```

#### **Exemple 2: Un hook personnalisé pour gérer les appels API**

```jsx
import { useState, useEffect } from "react";

function useFetch(url, options) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await fetch(url, options);
        if (!response.ok) {
          throw new Error(`Error: ${response.statusText}`);
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url, options]);

  return { data, error, loading };
}

export default useFetch;

// Usage example
/*
const { data, error, loading } = useFetch("https://jsonplaceholder.typicode.com/users");

if (loading) return <p>Loading...</p>;
if (error) return <p>Error: {error}</p>;

return (
  <ul>
    {data.map((item) => (
      <li key={item.id}>{item.name}</li>
    ))}
  </ul>
);
*/
```

#### **Exemple 3: Un hook personnalisé pour détecter si un élément est survolé**

```jsx
import { useState, useRef, useEffect } from "react";

function useHover() {
  const [isHovered, setIsHovered] = useState(false);
  const ref = useRef(null);

  useEffect(() => {
    const handleMouseOver = () => setIsHovered(true);
    const handleMouseOut = () => setIsHovered(false);

    const node = ref.current;
    if (node) {
      node.addEventListener("mouseover", handleMouseOver);
      node.addEventListener("mouseout", handleMouseOut);

      // Cleanup
      return () => {
        node.removeEventListener("mouseover", handleMouseOver);
        node.removeEventListener("mouseout", handleMouseOut);
      };
    }
  }, [ref]);

  return [ref, isHovered];
}

export default useHover;

// Usage example
/*
const [hoverRef, isHovered] = useHover();

return (
  <div ref={hoverRef} style={{ backgroundColor: isHovered ? "lightblue" : "white" }}>
    {isHovered ? 'hovered' : 'not hovered'}
  </div>
);
*/
```

#### Quelques référence :

- https://fr.react.dev/reference/react/hooks
- https://usehooks.com/
- https://usehooks-ts.com/
- https://github.com/streamich/react-use

### useCallback

#### **1. Définition**

`useCallback` est un hook de React qui mémorise une fonction pour éviter qu'elle ne soit recréée à chaque rendu du composant. Il est utile dans les cas où des fonctions sont passées en props à des composants enfants ou utilisées dans des dépendances pour éviter des re-rendus inutiles.

---

#### **2. Dans quel cas l'utiliser ?**

- **Optimisation des performances** : Éviter que des fonctions ne soient recréées inutilement à chaque rendu.
- **Passage de fonctions en props** : Empêcher les re-rendus inutiles des composants enfants dépendant de ces fonctions.
- **Dépendances dans les hooks** : Empêcher des effets secondaires (dans `useEffect`, `useMemo`, etc.) d'être déclenchés à cause d'une fonction recréée.

---

#### **3. Des exemples concrets de code pour chaque cas ?**

##### **Exemple 1 : Prévention des re-rendus inutiles d'un composant enfant**

```jsx
import React, { useState, useCallback } from "react";

const Child = React.memo(({ onClick }) => {
  const [bool, setBool] = useState(false);
  console.log("Child render");
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Ne rien faire pendant 500 ms pour simuler du code extrêmement lent
  }
  console.log("Child re-rendered");
  return (
    <>
      <button onClick={() => setBool(!bool)}>
        Click Me! {JSON.stringify(bool)}
      </button>
      <button onClick={onClick}>Increment</button>
    </>
  );
});

const Parent = () => {
  const [count, setCount] = useState(0);

  // useCallback empêche la recréation de cette fonction à chaque rendu
  const handleClick = useCallback(() => {
    console.log("Button clicked");
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <p>Parent count: {count}</p>
      {/* <button onClick={() => setCount((prev) => prev + 1)}>Increment</button> */}
      <Child onClick={handleClick} />
    </div>
  );
};

export default Parent;
```

##### **Exemple 2 **

```jsx
// App js

import { useState } from 'react';
import ProductPage from './ProductPage.js';

export default function App() {
  const [isDark, setIsDark] = useState(false);
  return (
    <>
      <label>
        <input
          type="checkbox"
          checked={isDark}
          onChange={e => setIsDark(e.target.checked)}
        />
        Mode sombre
      </label>
      <hr />
      <ProductPage
        referrerId="wizard_of_oz"
        productId={123}
        theme={isDark ? 'dark' : 'light'}
      />
    </>
  );
}
// --------------------------------------------
// ProductPage.js

import { useCallback } from 'react';
import ShippingForm from './ShippingForm.js';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);

  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
}

function post(url, data) {
  // Imaginez que ça envoie une requête...
  console.log('POST /' + url);
  console.log(data);
}

// --------------------------------------------
// ShippingForm.js
import { memo, useState } from 'react';

const ShippingForm = memo(function ShippingForm({ onSubmit }) {
  const [count, setCount] = useState(1);

  console.log('[ARTIFICIELLEMENT LENT] Rendu de <ShippingForm />');
  let startTime = performance.now();
  while (performance.now() - startTime < 500) {
    // Ne rien faire pendant 500 ms pour simuler du code extrêmement lent
  }

  function handleSubmit(e) {
    e.preventDefault();
    const formData = new FormData(e.target);
    const orderDetails = {
      ...Object.fromEntries(formData),
      count
    };
    onSubmit(orderDetails);
  }

  return (
    <form onSubmit={handleSubmit}>
      <p><b>Remarque : <code>ShippingForm</code> est artificiellement ralenti !</b></p>
      <label>
        Nombre d’éléments :
        <button type="button" onClick={() => setCount(count - 1)}>–</button>
        {count}
        <button type="button" onClick={() => setCount(count + 1)}>+</button>
      </label>
      <label>
        Rue :
        <input name="street" />
      </label>
      <label>
        Ville :
        <input name="city" />
      </label>
      <label>
        Code postal :
        <input name="zipCode" />
      </label>
      <button type="submit">Envoyer</button>
    </form>
  );
});

export default ShippingForm;
```

En revanche, il ne faut pas utiliser `useCallback` de manière excessive, car il peut ajouter de la complexité au code sans toujours apporter de bénéfices significatifs. Utilise-le uniquement dans les cas où des optimisations sont réellement nécessaires.

### useReducer

#### **1. Définition**

`useReducer` est un hook de React utilisé pour gérer des états complexes ou dépendants de plusieurs actions. Il fonctionne en combinant une fonction réductrice (`reducer`) et un état initial pour produire un nouvel état en réponse à des actions.

---

#### **2. Dans quel cas l'utiliser ?**

- **États complexes** : Lorsque plusieurs variables d'état sont liées ou dépendantes.
- **Logique d'état centralisée** : Pour regrouper toute la logique de gestion d'état dans une seule fonction (`reducer`).
- **Remplacement de `useState`** : Lorsque `useState` devient difficile à gérer à cause de la complexité des transitions d'état.

---

#### **3. Des exemples concrets de code pour chaque cas ?**

##### **Exemple 1 : Gestion d'un compteur avec des actions multiples**

```jsx
import React, { useReducer } from "react";

// Définition du reducer
const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return { count: 0 };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
};

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>Increment</button>
      <button onClick={() => dispatch({ type: "decrement" })}>Decrement</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </div>
  );
};

export default Counter;
```

##### **Exemple 2: Todo list** :

```jsx
// App
import { useReducer } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task;
        } else {
          return t;
        }
      });
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

export default function TaskApp() {
  const [tasks, dispatch] = useReducer(
    tasksReducer,
    initialTasks
  );

  function handleAddTask(text) {
    dispatch({
      type: 'added',
      id: nextId++,
      text: text,
    });
  }

  function handleChangeTask(task) {
    dispatch({
      type: 'changed',
      task: task
    });
  }

  function handleDeleteTask(taskId) {
    dispatch({
      type: 'deleted',
      id: taskId
    });
  }

  return (
    <>
      <h1>Guide de Prague</h1>
      <AddTask
        onAddTask={handleAddTask}
      />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  { id: 0, text: 'Visiter le musée Franz-Kafka', done: true },
  { id: 1, text: 'Voir un spectacle de marionnettes', done: false },
  { id: 2, text: 'Prendre une photo du mur John Lennon', done: false }
];


//-----------------------------------------
// TaskList.js
import { useState } from 'react';

export default function TaskList({
  tasks,
  onChangeTask,
  onDeleteTask
}) {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <Task
            task={task}
            onChange={onChangeTask}
            onDelete={onDeleteTask}
          />
        </li>
      ))}
    </ul>
  );
}

function Task({ task, onChange, onDelete }) {
  const [isEditing, setIsEditing] = useState(false);
  let taskContent;
  if (isEditing) {
    taskContent = (
      <>
        <input
          value={task.text}
          onChange={e => {
            onChange({
              ...task,
              text: e.target.value
            });
          }} />
        <button onClick={() => setIsEditing(false)}>
          Enregistrer
        </button>
      </>
    );
  } else {
    taskContent = (
      <>
        {task.text}
        <button onClick={() => setIsEditing(true)}>
          Modifier
        </button>
      </>
    );
  }
  return (
    <label>
      <input
        type="checkbox"
        checked={task.done}
        onChange={e => {
          onChange({
            ...task,
            done: e.target.checked
          });
        }}
      />
      {taskContent}
      <button onClick={() => onDelete(task.id)}>
        Supprimer
      </button>
    </label>
  );
}


//-----------------------------------------
// AddTask.js
import { useState } from 'react';

export default function AddTask({ onAddTask }) {
  const [text, setText] = useState('');
  return (
    <>
      <input
        placeholder="Ajouter une tâche"
        value={text}
        onChange={e => setText(e.target.value)}
      />
      <button onClick={() => {
        setText('');
        onAddTask(text);
      }}>Ajouter</button>
    </>
  )
}

//-----------------------------------------
// style.css
* {
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
  margin: 20px;
  padding: 0;
}

h1 {
  margin-top: 0;
  font-size: 22px;
}

h2 {
  margin-top: 0;
  font-size: 20px;
}

h3 {
  margin-top: 0;
  font-size: 18px;
}

h4 {
  margin-top: 0;
  font-size: 16px;
}

h5 {
  margin-top: 0;
  font-size: 14px;
}

h6 {
  margin-top: 0;
  font-size: 12px;
}

code {
  font-size: 1.2em;
}

ul {
  padding-inline-start: 20px;
}

button { margin: 5px; }
li { list-style-type: none; }
ul, li { margin: 0; padding: 0; }
```

##### **Exemple 3: Shopping cart** :

```jsx
// ShoppingCart.js
import React, { useReducer } from "react";
import { cartReducer } from "./cartReducer";
import ProductList from "./ProductList";
import Cart from "./Cart";
import Total from "./Total";

const ShoppingCart = () => {
  const [cartState, dispatch] = useReducer(cartReducer, {
    items: [],
    total: 0,
  });

  const products = [
    { id: 1, name: "Laptop", price: 1000 },
    { id: 2, name: "Headphones", price: 200 },
    { id: 3, name: "Keyboard", price: 150 },
  ];

  const handleAddToCart = (product) => {
    dispatch({ type: "add_item", payload: product });
  };

  const handleRemoveItem = (id) => {
    dispatch({ type: "remove_item", payload: id });
  };

  const handleUpdateQuantity = (id, quantity) => {
    dispatch({ type: "update_quantity", payload: { id, quantity } });
  };

  const handleClearCart = () => {
    dispatch({ type: "clear_cart" });
  };

  return (
    <div style={{ padding: "20px" }}>
      <h1>Shopping Cart</h1>
      <ProductList products={products} onAddToCart={handleAddToCart} />
      <Cart
        items={cartState.items}
        onRemoveItem={handleRemoveItem}
        onUpdateQuantity={handleUpdateQuantity}
        onClearCart={handleClearCart}
      />
      <Total total={cartState.total} />
    </div>
  );
};

export default ShoppingCart;

//-------------------------------------------------------
// ProductList.js
import React from "react";

const ProductList = ({ products, onAddToCart }) => {
  return (
    <div>
      <h2>Products</h2>
      <ul>
        {products.map((product) => (
          <li key={product.id} style={{ marginBottom: "10px" }}>
            <strong>{product.name}</strong> - ${product.price}
            <button
              onClick={() => onAddToCart(product)}
              style={{ marginLeft: "10px" }}
            >
              Add to Cart
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default ProductList;

//-------------------------------------------------------
// Cart.js
import React from "react";

const Cart = ({ items, onRemoveItem, onUpdateQuantity, onClearCart }) => {
  return (
    <div>
      <h2>Cart</h2>
      {items.length === 0 ? (
        <p>Your cart is empty.</p>
      ) : (
        <div>
          <ul>
            {items.map((item) => (
              <li key={item.id} style={{ marginBottom: "10px" }}>
                <strong>{item.name}</strong> - ${item.price} x {item.quantity}
                <button
                  onClick={() => onRemoveItem(item.id)}
                  style={{ marginLeft: "10px" }}
                >
                  Remove
                </button>
                <div style={{ marginTop: "5px" }}>
                  <label>
                    Quantity:{" "}
                    <input
                      type="number"
                      min="1"
                      value={item.quantity}
                      onChange={(e) =>
                        onUpdateQuantity(item.id, parseInt(e.target.value, 10))
                      }
                      style={{ width: "50px" }}
                    />
                  </label>
                </div>
              </li>
            ))}
          </ul>

          <button onClick={onClearCart} style={{ marginTop: "10px" }}>
            Clear Cart
          </button>
        </div>
      )}
    </div>
  );
};

export default Cart;

// -----------------------------------------------------
// cartReducer.js
export const cartReducer = (state, action) => {
  switch (action.type) {
    case "add_item":
      const existingItem = state.items.find(
        (item) => item.id === action.payload.id
      );
      const updatedItems = existingItem
        ? state.items.map((item) =>
            item.id === action.payload.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          )
        : [...state.items, { ...action.payload, quantity: 1 }];
      return {
        ...state,
        items: updatedItems,
        total: calculateTotal(updatedItems),
      };

    case "remove_item":
      const filteredItems = state.items.filter(
        (item) => item.id !== action.payload
      );
      return {
        ...state,
        items: filteredItems,
        total: calculateTotal(filteredItems),
      };

    case "update_quantity":
      const modifiedItems = state.items.map((item) =>
        item.id === action.payload.id
          ? { ...item, quantity: action.payload.quantity }
          : item
      );
      return {
        ...state,
        items: modifiedItems,
        total: calculateTotal(modifiedItems),
      };

    case "clear_cart":
      return {
        items: [],
        total: 0,
      };

    default:
      throw new Error(`Unknown action type: ${action.type}`);
  }
};

// Fonction pour calculer le total du panier
const calculateTotal = (items) =>
  items.reduce((sum, item) => sum + item.price * item.quantity, 0);




//-------------------------------------------------------
// Total.js
import React from "react";

const Total = ({ total }) => {
  return (
    <div style={{ marginTop: "20px" }}>
      <strong>Total: ${total.toFixed(2)}</strong>
    </div>
  );
};

export default Total;


```

### useContext

n/a

### useId

n/a

### Rest

**coming soon**
