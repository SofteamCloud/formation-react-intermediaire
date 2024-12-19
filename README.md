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
   - [Reste des hooks](#reste-des-hooks)
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
7. [TypeScript](#typescript)
8. [Tests unitaire](#unit-tests)
9. [Aller plus loin](#rendu-cote-serveur)
   - [Next.js](#nextjs)
   - [Remix](#remix)

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

### **Définitions**

`useContext` est un Hook React qui permet d'accéder à des données partagées via un `Context` sans passer les props explicitement à chaque niveau de l'arborescence des composants. Il simplifie la gestion des données globales telles que les thèmes, l'état d'authentification ou les préférences utilisateur.

---

### **Dans quel cas l'utiliser ?**

1. **Partage de données globales** :

   - Lorsque des informations doivent être accessibles dans plusieurs composants, comme le thème (dark/light), les données utilisateur, ou la configuration de l'application.

2. **Éviter le "props drilling"** :

   - Réduire le besoin de transmettre des props à travers de nombreux niveaux de composants.

3. **Faciliter la collaboration entre composants éloignés** :
   - Permet aux composants distants dans l'arborescence de partager et d'utiliser des données globales.

---

### **Exemples concrets**

#### **Exemple 1 : Gestion du `props drilling`**

```jsx
import { useState, createContext, useContext } from "react";
import ReactDOM from "react-dom/client";

const UserContext = createContext();

function Component1() {
  const [user, setUser] = useState("Jesse Hall");

  return (
    <UserContext.Provider value={user}>
      <h1>{`Hello ${user}!`}</h1>
      <Component2 />
    </UserContext.Provider>
  );
}

function Component2() {
  return (
    <>
      <h1>Component 2</h1>
      <Component3 />
    </>
  );
}

function Component3() {
  return (
    <>
      <h1>Component 3</h1>
      <Component4 />
    </>
  );
}

function Component4() {
  return (
    <>
      <h1>Component 4</h1>
      <Component5 />
    </>
  );
}

function Component5() {
  const user = useContext(UserContext);

  return (
    <>
      <h1>Component 5</h1>
      <h2>{`Hello ${user} again!`}</h2>
    </>
  );
}
```

#### **Exemple 3 : Gestion d'un thème global**

```jsx
import React, { createContext, useContext, useState } from "react";

// Création du contexte
const ThemeContext = createContext();

// Composant fournisseur du thème
const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// Composant utilisant le contexte
const ThemeSwitcher = () => {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div
      style={{
        background: theme === "light" ? "#fff" : "#333",
        color: theme === "light" ? "#000" : "#fff",
        padding: "20px",
        textAlign: "center",
      }}
    >
      <p>Le thème actuel est : {theme}</p>
      <button onClick={toggleTheme}>Changer de thème</button>
    </div>
  );
};

// Application principale
const App = () => (
  <ThemeProvider>
    <ThemeSwitcher />
  </ThemeProvider>
);

export default App;
```

#### **Exemple 3 : Gestion d'un utilisateur authentifié**

```jsx
import React, { createContext, useContext, useState } from "react";

// Création du contexte
const AuthContext = createContext();

// Composant fournisseur pour l'authentification
const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const login = (username) => setUser({ username });
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

// Composant qui utilise le contexte pour afficher les informations utilisateur
const UserProfile = () => {
  const { user, logout } = useContext(AuthContext);

  return (
    <div style={{ margin: "20px" }}>
      {user ? (
        <div>
          <p>Connecté en tant que : {user.username}</p>
          <button onClick={logout}>Déconnexion</button>
        </div>
      ) : (
        <p>Non connecté.</p>
      )}
    </div>
  );
};

// Composant qui utilise le contexte pour se connecter
const LoginForm = () => {
  const { login } = useContext(AuthContext);

  return (
    <div style={{ margin: "20px" }}>
      <button onClick={() => login("JohnDoe")}>Connexion</button>
    </div>
  );
};

// Application principale
const App = () => (
  <AuthProvider>
    <UserProfile />
    <LoginForm />
  </AuthProvider>
);

export default App;
```

### useID

### **Définitions**

`useId` est un Hook React qui génère un identifiant unique stable pour un composant. Il est particulièrement utile pour associer des attributs HTML comme `id` à des labels ou autres éléments interactifs dans des formulaires.

---

### **Dans quel cas l'utiliser ?**

1. **Accessibilité** :

   - Associer des labels avec des champs de formulaire (`label` et `input`), ce qui est essentiel pour les utilisateurs de lecteurs d'écran.

2. **Éviter les conflits d'ID** :

   - Générer des identifiants uniques dans des composants réutilisables pour éviter les duplications.

3. **Framework SSR (Server-Side Rendering)** :
   - Garantir que les identifiants générés sont cohérents entre le serveur et le client pour éviter les problèmes d'hydratation.

---

### **Exemples concrets**

#### **Exemple : Utilisation dans un formulaire pour l'accessibilité**

```jsx
import { useId } from "react";

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input type="password" aria-describedby={passwordHintId} />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}

export default function App() {
  return (
    <>
      <h2>Choose password</h2>
      <PasswordField />
      <h2>Confirm password</h2>
      <PasswordField />
    </>
  );
}
```

### Reste des hooks

Regarder React doc https://fr.react.dev/reference/react/hooks

## React pour les cas spéciaux

### Portails (Portals)

### **Définitions**

`createPortal` est une fonction fournie par React qui permet de rendre un composant enfant dans un DOM différent de celui de son parent. Cela est utile pour créer des éléments comme des modals, des tooltips ou des menus déroulants qui doivent apparaître en dehors de la hiérarchie DOM du composant parent.

---

### **Dans quel cas l'utiliser ?**

1. **Modals et popups** :

   - Lorsqu'un composant comme une boîte de dialogue ou une fenêtre modale doit être rendu au premier plan sans être restreint par le style ou le positionnement de ses parents.

2. **Tooltips et menus contextuels** :

   - Pour garantir que ces éléments s'affichent correctement en dehors des limitations de leurs conteneurs parent (par exemple, un `overflow: hidden`).

3. **Gestion du z-index** :
   - Pour éviter des conflits de style et de hiérarchie visuelle avec d'autres éléments.

---

### **Exemples concrets**

#### **Exemple 1 : Création d'une fenêtre modale**

```jsx
import React, { useState } from "react";
// import ReactDOM from 'react-dom';

// const ModalWithPortal = ({ isOpen, onClose, children }) => {
//   if (!isOpen) return null;

//   return ReactDOM.createPortal(
//     <div
//       style={{
//         position: 'fixed',
//         top: 0,
//         left: 0,
//         width: '100vw',
//         height: '100vh',
//         backgroundColor: 'rgba(0, 0, 0, 0.5)',
//         display: 'flex',
//         justifyContent: 'center',
//         alignItems: 'center',
//       }}
//       onClick={onClose}
//     >
//       <div
//         style={{
//           backgroundColor: '#fff',
//           padding: '20px',
//           borderRadius: '10px',
//         }}
//         onClick={(e) => e.stopPropagation()}
//       >
//         <button onClick={onClose} style={{ float: 'right' }}>
//           &times;
//         </button>
//         {children}
//       </div>
//     </div>,
//     document.body // Point d'insertion dans le DOM
//   );
// };

const ModalWithNoPortal = ({ isOpen, onClose, children }) => {
  if (!isOpen) return null;

  return (
    <div
      style={{
        position: "absolute",
        top: 0,
        left: 0,
        backgroundColor: "rgba(0, 0, 0, 0.5)",
        display: "flex",
        justifyContent: "center",
        alignItems: "center",
      }}
      onClick={onClose}
    >
      <div
        style={{
          backgroundColor: "#fff",
          padding: "20px",
          borderRadius: "10px",
        }}
        onClick={(e) => e.stopPropagation()}
      >
        <button onClick={onClose} style={{ float: "right" }}>
          &times;
        </button>
        {children}
      </div>
    </div>
  );
};

const App = () => {
  const [isModalOpen, setModalOpen] = useState(false);

  return (
    <div
      style={{
        width: "400px",
        height: "400px",
        border: "1px solid #222",
        position: "relative",
        overflow: "hidden",
      }}
    >
      <h1>Bienvenue sur mon application</h1>
      <button onClick={() => setModalOpen(true)}>Ouvrir la modale</button>
      <ModalWithNoPortal
        isOpen={isModalOpen}
        onClose={() => setModalOpen(false)}
      >
        <h2>Contenu de la modale</h2>
        <p>Ceci est une fenêtre modale rendue avec React Portals.</p>
      </ModalWithNoPortal>
    </div>
  );
};

export default App;
```

#### **Exemple 2 : Menu contextuel**

```jsx
import React, { useState } from "react";
import ReactDOM from "react-dom";

const ContextMenu = ({ x, y, options, onClose }) => {
  return ReactDOM.createPortal(
    <ul
      style={{
        position: "absolute",
        top: y,
        left: x,
        backgroundColor: "#fff",
        border: "1px solid #ccc",
        listStyle: "none",
        padding: "10px",
        borderRadius: "5px",
      }}
      onClick={onClose}
    >
      {options.map((option, index) => (
        <li key={index} style={{ padding: "5px 10px", cursor: "pointer" }}>
          {option}
        </li>
      ))}
    </ul>,
    document.body
  );
};

const Test = () => {
  const [menu, setMenu] = useState({ isOpen: false, x: 0, y: 0 });

  const openMenu = (e) => {
    e.preventDefault();
    setMenu({ isOpen: true, x: e.pageX, y: e.pageY });
  };

  const closeMenu = () => setMenu({ isOpen: false, x: 0, y: 0 });

  return (
    <div
      onContextMenu={openMenu}
      style={{ width: "100vw", height: "100vh", backgroundColor: "#f0f0f0" }}
    >
      <h1>Clic droit pour ouvrir le menu contextuel</h1>
      {menu.isOpen && (
        <ContextMenu
          x={menu.x}
          y={menu.y}
          options={["Option 1", "Option 2", "Option 3"]}
          onClose={closeMenu}
        />
      )}
    </div>
  );
};

export default Test;
```

### Gestion des erreurs (ErrorBoundary)

### **Définitions**

Un `ErrorBoundary` (limite de gestion des erreurs) est un composant React qui capture les erreurs JavaScript provenant de son arborescence de composants enfants, empêchant ainsi l'application de planter. Les `ErrorBoundary` s'implémentent en utilisant la méthode de cycle de vie `componentDidCatch` et `getDerivedStateFromError`. Ils ne fonctionnent que pour les erreurs au sein des composants React.

---

### **Dans quel cas l'utiliser ?**

1. **Empêcher un plantage global** :

   - Protéger l'expérience utilisateur en isolant les erreurs à des parties spécifiques de l'application.

2. **Afficher un message d'erreur convivial** :

   - Remplacer un écran blanc ou un crash complet par un message explicatif ou une alternative.

3. **Environnement de production** :

   - Collecter les erreurs et les enregistrer dans des outils comme Sentry ou LogRocket.

4. **Isoler des sections à risque** :
   - Lors de l'intégration de bibliothèques tierces ou de composants complexes susceptibles de générer des erreurs.

### **Limites**

Ne capture pas les erreurs :

- Dans les gestionnaires d'événements.
- En dehors des composants React (par exemple, dans des callbacks asynchrones).
- Lors du rendu côté serveur (SSR).

---

### **Exemple concret : Gestion d'erreurs avec un message d'erreur personnalisé**

#### **Création d'un `ErrorBoundary`**

```jsx
// Test.jsx

import React, { useEffect } from "react";
import ErrorBoundary from "./ErrorBoundary";

// Composant susceptible de générer une erreur
const BuggyComponent = () => {
  const handleClick = () => {
    throw new Error("Un bug s'est produit !");
  };

  // useEffect(() => {
  //   throw new Error("Un bug s'est produit !");
  // }, []);

  return (
    <div>
      <button onClick={handleClick}>
        Cliquez ici pour provoquer une erreur
      </button>
      <p>
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Corrupti cumque
        eos exercitationem ullam soluta. Consequuntur perspiciatis nemo
        repellendus eaque officia molestiae labore cum temporibus, doloremque
        sequi, eos culpa saepe sunt.
      </p>
    </div>
  );
};

const Test = () => {
  return (
    <div>
      <h1>Application React avec ErrorBoundary</h1>
      <ErrorBoundary>
        <BuggyComponent />
      </ErrorBoundary>
    </div>
  );
};

export default Test;

// -----------------------------------------------------------------------
// ErrorBoundary.jsx

import React from "react";

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Met à jour l'état pour afficher l'interface utilisateur de secours
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // Enregistre l'erreur et ses détails (par exemple, via un service de logs)
    console.error("Erreur capturée par ErrorBoundary :", error, info);
  }

  render() {
    if (this.state.hasError) {
      // Affiche une interface utilisateur de secours
      return <h1>Oups ! Une erreur est survenue.</h1>;
    }

    // Rendre les enfants normalement
    return this.props.children;
  }
}

export default ErrorBoundary;

```

### Suspense & lazy

### **Définitions**

`React.lazy` est une fonction qui permet de charger un composant de manière asynchrone uniquement lorsqu'il est nécessaire. Cela permet d'améliorer les performances en scindant le code (code-splitting) et en réduisant la taille initiale de l'application. Il est souvent utilisé avec `React.Suspense` pour afficher une interface utilisateur pendant le chargement.

---

### **Dans quel cas l'utiliser ?**

1. **Chargement conditionnel de composants** :

   - Lorsqu'un composant ou une partie de l'interface utilisateur est rarement utilisée ou dépend d'une interaction utilisateur.

2. **Optimisation des performances** :

   - Scinder le code pour éviter de charger l'intégralité de l'application dès le départ.

3. **Applications à grande échelle** :
   - Lorsque plusieurs routes ou fonctionnalités sont indépendantes les unes des autres, permettant de charger uniquement les modules nécessaires.

---

### **Exemples concrets**

#### **Exemple 1 : Chargement différé d'un composant avec React.lazy**

```jsx
// App.jsx

import { useState, Suspense, lazy } from 'react';
import Loading from './Loading.js';

const MarkdownPreview = lazy(() => delayForDemo(import('./MarkdownPreview.js')));

export default function MarkdownEditor() {
  const [showPreview, setShowPreview] = useState(false);
  const [markdown, setMarkdown] = useState('Salut **tout le monde** !');
  return (
    <>
      <textarea value={markdown} onChange={e => setMarkdown(e.target.value)} />
      <label>
        <input type="checkbox" checked={showPreview} onChange={e => setShowPreview(e.target.checked)} />
        Afficher l’aperçu
      </label>
      <hr />
      {showPreview && (
        <Suspense fallback={<Loading />}>
          <h2>Aperçu</h2>
          <MarkdownPreview markdown={markdown} />
        </Suspense>
      )}
    </>
  );
}

// Ajouter un délai fixe pour voir l’état de chargement
function delayForDemo(promise) {
  return new Promise(resolve => {
    setTimeout(resolve, 2000);
  }).then(() => promise);
}

//--------------------------------------------------
// Loading.jsx
export default function Loading() {
  return <p><i>Chargement...</i></p>;
}

//--------------------------------------------------
// Loading.jsx
import { Remarkable } from 'remarkable'; // npm install remarkable

const md = new Remarkable();

export default function MarkdownPreview({ markdown }) {
  return (
    <div
      className="content"
      dangerouslySetInnerHTML={{__html: md.render(markdown)}}
    />
  );
}
```

#### **Exemple 2 : Utilisation de React.lazy dans une application avec plusieurs routes**

```jsx
import React, { Suspense } from "react";
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";

// Importation lazy des pages
const Home = React.lazy(() => import("./Home"));
const About = React.lazy(() => import("./About"));
const Contact = React.lazy(() => import("./Contact"));

const App = () => {
  return (
    <Router>
      <nav>
        <Link to="/">Accueil</Link> | <Link to="/about">À propos</Link> |{" "}
        <Link to="/contact">Contact</Link>
      </nav>

      <Suspense fallback={<p>Chargement de la page...</p>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>
      </Suspense>
    </Router>
  );
};

export default App;

// Home.js
export default function Home() {
  return <h1>Page d'accueil</h1>;
}

// About.js
export default function About() {
  return <h1>À propos</h1>;
}

// Contact.js
export default function Contact() {
  return <h1>Contactez-nous</h1>;
```

## Gestion d'état global

### Zustand

### **Définitions**

`zustand` est une bibliothèque légère pour la gestion d'état dans les applications React. Elle permet de créer un magasin (store) global ou local, avec une API simple et performante. Contrairement à des solutions comme Redux, `zustand` est plus minimaliste et ne nécessite pas de boilerplate supplémentaire (comme des actions ou des reducers).

---

### **Dans quel cas l'utiliser ?**

1. **Gestion d'état global ou local** :

   - Pour partager des données ou des états entre plusieurs composants sans avoir besoin de prop-drilling.

2. **Applications légères** :

   - Idéal pour des projets qui ne nécessitent pas la complexité de Redux ou de Context API.

3. **Performances** :

   - Pour une gestion d'état rapide avec un impact minimal sur le rendu des composants.

4. **Cas complexes** :
   - Utile pour gérer des cas spécifiques comme des WebSockets, des états asynchrones ou des formulaires complexes.

---

### **Exemples concrets**

#### **Exemple 1 : Compteur global avec Zustand**

```jsx
import create from "zustand";

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
}));

const Counter = () => {
  const { count, increment, decrement } = useStore();
  return (
    <div>
      <h1>Compteur : {count}</h1>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </div>
  );
};

export default Counter;
```

#### **Exemple 2 : Compteur global avec Zustand**

```jsx
// -----------------------------------------------------
// cartStore

import { create } from "zustand";

export const useCartStore = create((set) => ({
  items: [],
  total: 0,

  // Ajouter un produit
  addItem: (product) =>
    set((state) => {
      const existingItem = state.items.find((item) => item.id === product.id);

      const updatedItems = existingItem
        ? state.items.map((item) =>
            item.id === product.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          )
        : [...state.items, { ...product, quantity: 1 }];

      return {
        items: updatedItems,
        total: calculateTotal(updatedItems),
      };
    }),

  // Supprimer un produit
  removeItem: (id) =>
    set((state) => {
      const filteredItems = state.items.filter((item) => item.id !== id);
      return {
        items: filteredItems,
        total: calculateTotal(filteredItems),
      };
    }),

  // Mettre à jour la quantité d'un produit
  updateQuantity: (id, quantity) =>
    set((state) => {
      const updatedItems = state.items.map((item) =>
        item.id === id ? { ...item, quantity } : item
      );
      return {
        items: updatedItems,
        total: calculateTotal(updatedItems),
      };
    }),

  // Vider le panier
  clearCart: () => set({ items: [], total: 0 }),
}));

// Fonction pour calculer le total du panier
const calculateTotal = (items) =>
  items.reduce((sum, item) => sum + item.price * item.quantity, 0);


//-------------------------------------------------------
// ShoppingCart.js

import ProductList from "./ProductList";
import Cart from "./Cart";
import Total from "./Total";

const ShoppingCart = () => {
  return (
    <div className="p-6 bg-gray-50 min-h-screen">
      <h1 className="text-2xl font-bold text-center mb-6">Shopping Cart</h1>
      <div className="grid gap-8 md:grid-cols-2">
        <ProductList />
        <Cart />
      </div>
      <Total />
    </div>
  );
};

export default ShoppingCart;

//-------------------------------------------------------
// ProductList.js

import Product from "./Product";
import { useCartStore } from "./store";

const ProductList = () => {
  const addItem = useCartStore((state) => state.addItem);

  const products = [
    { id: 1, name: "Laptop", price: 1000 },
    { id: 2, name: "Headphones", price: 200 },
    { id: 3, name: "Keyboard", price: 150 },
  ];

  return (
    <div>
      <h2 className="text-xl font-semibold text-gray-700 mb-4">Products</h2>
      <ul className="space-y-4">
        {products.map((product) => (
          <Product product={product} key={product.id} />
        ))}
      </ul>
    </div>
  );
};

export default ProductList;

//-------------------------------------------------------
// Product.js

import { useCartStore } from "./store";

const Product = ({ product }) => {
  const addItem = useCartStore((state) => state.addItem);

  return (
    <li className="group block overflow-hidden border border-gray-200 rounded-lg">
      {/* <div className="relative h-64 sm:h-72">
        <img
          src={`https://source.unsplash.com/featured/?${product.name}`}
          alt={product.name}
          className="absolute inset-0 h-full w-full object-cover opacity-100 group-hover:opacity-80"
        />
      </div> */}
      <div className="p-4 bg-white">
        <h3 className="text-lg text-gray-700 group-hover:underline">
          {product.name}
        </h3>
        <p className="mt-1 text-gray-900 font-bold">${product.price}</p>
        <button
          onClick={() => addItem(product)}
          className="mt-3 w-full bg-gray-700 text-white py-2 px-4 rounded hover:bg-gray-600"
        >
          Add to Cart
        </button>
      </div>
    </li>
  );
};

export default Product;

//-------------------------------------------------------
// Cart.js
import { useCartStore } from "./store";

const Cart = () => {
  const { items, removeItem, updateQuantity, clearCart } = useCartStore();

  return (
    <div className="p-4 border border-gray-200 bg-white rounded-lg">
      <h2 className="text-xl font-semibold text-gray-700 mb-4">Cart</h2>
      {items.length === 0 ? (
        <p className="text-gray-500">Your cart is empty.</p>
      ) : (
        <div className="space-y-4">
          <ul className="space-y-4">
            {items.map((item) => (
              <li
                key={item.id}
                className="flex items-center justify-between gap-4 border-b pb-4"
              >
                <div className="flex items-center gap-4">
                  {/*<img
                    src={`https://source.unsplash.com/featured/?${item.name}`}
                    alt={item.name}
                    className="h-16 w-16 rounded object-cover"
                  /> */}
                  <div>
                    <h3 className="text-sm text-gray-900">{item.name}</h3>
                    <p className="text-gray-600">
                      ${item.price} x {item.quantity}
                    </p>
                  </div>
                </div>
                <div className="flex items-center gap-2">
                  <input
                    type="number"
                    min="1"
                    value={item.quantity}
                    onChange={(e) =>
                      updateQuantity(item.id, parseInt(e.target.value, 10))
                    }
                    className="w-12 border-gray-300 rounded text-center"
                  />
                  <button
                    onClick={() => removeItem(item.id)}
                    className="text-red-500 hover:underline"
                  >
                    Remove
                  </button>
                </div>
              </li>
            ))}
          </ul>
          <button
            onClick={clearCart}
            className="block w-full bg-gray-700 text-white py-2 rounded hover:bg-gray-600"
          >
            Clear Cart
          </button>
        </div>
      )}
    </div>
  );
};

export default Cart;

//-------------------------------------------------------
// Total.js
import { useCartStore } from "./store";

const Total = () => {
  const total = useCartStore((state) => state.total);

  return (
    <div className="mt-6 text-center">
      <strong className="text-lg">Total: ${total.toFixed(2)}</strong>
    </div>
  );
};

export default Total;

```

### Redux Toolkit

### **Définitions**

**Redux** Redux est une bibliothèque JavaScript open-source utilisée pour la gestion de l’état des applications. Elle est principalement utilisée avec des bibliothèques comme React ou Angular pour construire des interfaces utilisateur. Redux permet de centraliser l’état de l’application dans un store unique, ce qui facilite la gestion et le suivi des changements d’état.

**Redux Toolkit** est une bibliothèque officielle de gestion d'état pour Redux. Elle simplifie la configuration de Redux en fournissant des outils modernes pour créer un store, définir des slices d'état, et gérer des actions de manière concise et efficace. Redux Toolkit réduit le boilerplate souvent associé à Redux classique tout en offrant de meilleures performances et une expérience de développement améliorée.

---

### **Dans quel cas l'utiliser ?**

1. **Gestion d'état complexe** :

   - Idéal pour les grandes applications où plusieurs fonctionnalités nécessitent un partage d'état global.

2. **Applications évolutives** :

   - Utile pour structurer des applications en modules bien définis grâce aux slices.

3. **Simplification du développement Redux** :

   - Pour éviter le boilerplate lourd de Redux classique (comme les fichiers d'action, les reducers, etc.).

4. **Fonctionnalités avancées** :
   - Fournit un middleware comme `createAsyncThunk` pour gérer les actions asynchrones (par exemple, les appels API).

---

### **Exemples concrets**

#### **Exemple 1 : Configuration de Redux Toolkit avec un compteur simple**

```javascript
// App.jsx

import { useSelector, useDispatch } from "react-redux";
import { increment, decrement, reset } from "./store/counterSlice";
import ShoppingCart from "./ShoppingCartRedux/ShoppingCart";

function App() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Compteur : {count}</h1>
      <br />
      <span className="inline-flex -space-x-px overflow-hidden rounded-md border bg-white shadow-sm">
        <button
          onClick={() => dispatch(decrement())}
          className="inline-block px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50 focus:relative"
        >
          Incrementer
        </button>

        <button
          onClick={() => dispatch(reset())}
          className="inline-block px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50 focus:relative"
        >
          Reset
        </button>

        <button
          onClick={() => dispatch(increment())}
          className="inline-block px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50 focus:relative"
        >
          Decrementer
        </button>
      </span>

      <hr />
      <ShoppingCart />
    </div>
  );
}

export default App;

//-------------------------------------------------
// main.jsx

import store from "./store/store.js";
import { Provider } from "react-redux";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </StrictMode>

//-------------------------------------------------
// counterSlice.js

import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    reset: (state) => {
      state.value = 0;
    },
  },
});

console.log({ counterSlice });

export const { increment, decrement, reset } = counterSlice.actions;
export default counterSlice.reducer;

//-------------------------------------------------
// store.js

import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";
import cartReducer from "./cartSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    cart: cartReducer,
  },
});
export default store;

//-------------------------------------------------
// ShoppingCart.jsx

import ProductList from "./ProductList";
import Cart from "./Cart";
import Total from "./Total";

const ShoppingCart = () => {
  return (
    <div className="p-6 bg-gray-50 min-h-screen">
      <h1 className="text-2xl font-bold text-center mb-6">Shopping Cart</h1>
      <div className="grid gap-8 md:grid-cols-2">
        <ProductList />
        <Cart />
      </div>
      <Total />
    </div>
  );
};

export default ShoppingCart;


//-------------------------------------------------
// ProductList.jsx

import ProductCard from "./ProductCard";

const ProductList = () => {
  const products = [
    { id: 1, name: "Laptop", price: 1000 },
    { id: 2, name: "Headphones", price: 200 },
    { id: 3, name: "Keyboard", price: 150 },
  ];

  return (
    <div>
      <h2 className="text-xl font-semibold text-gray-700 mb-4">Products</h2>
      <ul className="space-y-4">
        {products.map((product) => (
          <ProductCard key={product.id} product={product} />
        ))}
      </ul>
    </div>
  );
};

export default ProductList;


//-------------------------------------------------
// ProductCard.jsx

import { useDispatch } from "react-redux";
import { addItem } from "../store/cartSlice";

const ProductCard = ({ product }) => {
  const dispatch = useDispatch();

  return (
    <li className="group block overflow-hidden border border-gray-200 rounded-lg">
      {/* <div className="relative h-64 sm:h-72">
        <img
          src={`https://source.unsplash.com/featured/?${product.name}`}
          alt={product.name}
          className="absolute inset-0 h-full w-full object-cover opacity-100 group-hover:opacity-80"
        />
      </div> */}
      <div className="p-4 bg-white">
        <h3 className="text-lg text-gray-700 group-hover:underline">
          {product.name}
        </h3>
        <p className="mt-1 text-gray-900 font-bold">${product.price}</p>
        <button
          onClick={() => dispatch(addItem(product))}
          className="mt-3 w-full bg-gray-700 text-white py-2 px-4 rounded hover:bg-gray-600"
        >
          Add to Cart
        </button>
      </div>
    </li>
  );
};

export default ProductCard;


//-------------------------------------------------
// Cart.jsx

import { useSelector, useDispatch } from "react-redux";
import { clearCart } from "../store/cartSlice";
import CartItem from "./CartItem";

const Cart = () => {
  const items = useSelector((state) => state.cart.items);
  const dispatch = useDispatch();

  return (
    <div className="p-4 border border-gray-200 bg-white rounded-lg">
      <h2 className="text-xl font-semibold text-gray-700 mb-4">Cart</h2>
      {items.length === 0 ? (
        <p className="text-gray-500">Your cart is empty.</p>
      ) : (
        <div className="space-y-4">
          <ul className="space-y-4">
            {items.map((item) => (
              <CartItem key={item.id} item={item} />
            ))}
          </ul>
          <button
            onClick={() => dispatch(clearCart())}
            className="block w-full bg-gray-700 text-white py-2 rounded hover:bg-gray-600"
          >
            Clear Cart
          </button>
        </div>
      )}
    </div>
  );
};

export default Cart;


//-------------------------------------------------
// CartItem.jsx

import { useDispatch } from "react-redux";
import { removeItem, updateQuantity } from "../store/cartSlice";

const CartItem = ({ item }) => {
  const dispatch = useDispatch();

  console.log("cartItem");

  return (
    <li className="flex items-center justify-between gap-4 border-b pb-4">
      <div className="flex items-center gap-4">
        {/* <img
          src={`https://source.unsplash.com/featured/?${item.name}`}
          alt={item.name}
          className="h-16 w-16 rounded object-cover"
        /> */}
        <div>
          <h3 className="text-sm text-gray-900">{item.name}</h3>
          <p className="text-gray-600">
            ${item.price} x {item.quantity}
          </p>
        </div>
      </div>
      <div className="flex items-center gap-2">
        <input
          type="number"
          min="1"
          value={item.quantity}
          onChange={(e) =>
            dispatch(
              updateQuantity({
                id: item.id,
                quantity: parseInt(e.target.value, 10),
              })
            )
          }
          className="w-12 border-gray-300 rounded text-center"
        />
        <button
          onClick={() => dispatch(removeItem(item.id))}
          className="text-red-500 hover:underline"
        >
          Remove
        </button>
      </div>
    </li>
  );
};

export default CartItem;



//-------------------------------------------------
// Total.jsx

import { useSelector } from "react-redux";

const Total = () => {
  const totalPrice = useSelector((state) => state.cart.total);

  return (
    <div className="p-4 border border-gray-200 bg-gray-50 rounded-lg">
      <h2 className="text-lg font-semibold text-gray-700">Total</h2>
      <p className="text-xl font-bold text-gray-900 mt-2">
        ${totalPrice.toFixed(2)}
      </p>
    </div>
  );
};

export default Total;


//-------------------------------------------------
// cartSlice.js

import { createSlice } from "@reduxjs/toolkit";

// Fonction utilitaire pour calculer le total
const calculateTotal = (items) => {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
};

const initialState = {
  items: [],
  total: 0,
};

const cartSlice = createSlice({
  name: "cart",
  initialState,
  reducers: {
    addItem(state, action) {
      const item = state.items.find((i) => i.id === action.payload.id);

      if (item) {
        item.quantity += 1;
      } else {
        state.items.push({ ...action.payload, quantity: 1 });
      }

      // Utilisation de la fonction calculateTotal
      state.total = calculateTotal(state.items);
    },

    removeItem(state, action) {
      const itemIndex = state.items.findIndex((i) => i.id === action.payload);

      if (itemIndex !== -1) {
        state.items.splice(itemIndex, 1);
      }

      // Utilisation de la fonction calculateTotal
      state.total = calculateTotal(state.items);
    },

    updateQuantity(state, action) {
      const { id, quantity } = action.payload;
      const item = state.items.find((i) => i.id === id);

      if (item) {
        item.quantity = quantity > 0 ? quantity : 1; // Assurez-vous que la quantité est toujours positive
      }

      // Utilisation de la fonction calculateTotal
      state.total = calculateTotal(state.items);
    },

    clearCart(state) {
      state.items = [];
      state.total = 0; // Réinitialisation directe
    },
  },
});

export const { addItem, removeItem, updateQuantity, clearCart } = cartSlice.actions;
export default cartSlice.reducer;
```

## Écosystème de librairies

### React Router DOM

### **Définitions**

**React Router DOM** est une bibliothèque standard pour la gestion du routage dans les applications React. Elle permet de créer des interfaces utilisateur multi-pages en gérant la navigation entre différentes vues ou "routes" sans recharger la page. Les URL de l'application reflètent ainsi son état, ce qui est essentiel pour des applications modernes.

---

### **Dans quel cas l'utiliser ?**

1. **Applications multi-pages** :

   - Lorsque vous devez organiser votre application en différentes sections accessibles via des URL uniques (exemple : `/home`, `/about`, `/contact`).

2. **Routage dynamique** :

   - Pour des contenus ou des routes basés sur des paramètres (exemple : `/product/:id`).

3. **Applications avec navigation complexe** :

   - Lorsque vous devez gérer des scénarios avancés comme des routes protégées, des redirections, ou des animations entre les pages.

4. **SEO et partage de contenu** :
   - Les URL uniques permettent une meilleure optimisation pour les moteurs de recherche et un partage de contenu simplifié.

---

### **Exemples concrets**

#### **Exemple 1 :**

```javascript
// RouterWithRoutes.jsx
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import Home from "./Home";
import About from "./About";
import Contact from "./Contact";
import NoPage from "./NoPage";

const RouterWithRoutes = () => {
  return (
    <Router>
      <nav>
        <Link to="/">Accueil</Link> | <Link to="/about">À propos</Link> |{" "}
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NoPage />} />
      </Routes>
    </Router>
  );
};

export default RouterWithRoutes;

//---------------------------------------------------
// RouterWithRoutes.jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Home from "./Home";
import About from "./About";
import Contact from "./Contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />,
  },
  {
    path: "/about",
    element: <About />,
  },
  {
    path: "/Contact",
    element: <Contact />,
  },
]);

const RouterWithDataRoutes = () => <RouterProvider router={router} />;

export default RouterWithDataRoutes;

//---------------------------------------------------
// Home.jsx
export default function Home() {
  return <h1>{`Page d'accueil`}</h1>;
}


//---------------------------------------------------
// About.jsx
export default function About() {
  return <h1>{`À propos`}</h1>;
}


//---------------------------------------------------
// Contact.jsx
export default function Contact() {
  return <h1>Contactez-nous</h1>;
}
```

#### **Exemple 2**

```jsx
//---------------------------------------------------
// blogData.js
export const blogPosts = [
  {
    id: 1,
    title: "React Router Basics",
    category: "React",
    content: "Learn the basics of React Router.",
  },
  {
    id: 2,
    title: "Advanced React Router",
    category: "React",
    content: "Deep dive into React Router features.",
  },
  {
    id: 3,
    title: "Node.js Introduction",
    category: "Node",
    content: "Introduction to Node.js fundamentals.",
  },
  {
    id: 4,
    title: "Building APIs with Express",
    category: "Node",
    content: "Learn to build APIs using Express.js.",
  },
  {
    id: 5,
    title: "CSS Grid vs Flexbox",
    category: "CSS",
    content: "When to use Grid or Flexbox.",
  },
  {
    id: 6,
    title: "Tailwind CSS Setup",
    category: "CSS",
    content: "How to set up Tailwind in your project.",
  },
  {
    id: 7,
    title: "JavaScript Closures",
    category: "JavaScript",
    content: "Understanding closures in JavaScript.",
  },
  {
    id: 8,
    title: "Async/Await in JS",
    category: "JavaScript",
    content: "Handle async operations effectively.",
  },
];



//---------------------------------------------------
// Main.jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import { RouterProvider } from "react-router-dom";
import { router } from "./Router.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>
);

//---------------------------------------------------
// Router.jsx
import { createBrowserRouter } from "react-router-dom";
import App from "./App.jsx";
import BlogList from "./blog/BlogList.jsx";
import BlogDetail from "./blog/BlogDetail.jsx";
import "./index.css";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <App />,
    children: [
      { index: true, element: <BlogList /> },
      { path: "blog/:id", element: <BlogDetail /> },
    ],
  },
]);


//---------------------------------------------------
// App.jsx
import { Outlet } from "react-router-dom";
import Sidebar from "./blog/Sidebar";

const App = () => {
  return (
    <div className="flex h-screen bg-gray-100">
      <Sidebar />
      <div className="flex-1 p-6 overflow-y-auto">
        <h1 className="text-3xl font-bold mb-6">My Blog</h1>
        <Outlet />
      </div>
    </div>
  );
};

export default App;


//---------------------------------------------------
// Sidebar.jsx
import { Link } from "react-router-dom";

const Sidebar = () => {
  return (
    <div className="w-60 bg-white shadow-md p-4">
      <h2 className="text-xl font-semibold mb-4">Categories</h2>
      <ul className="space-y-2">
        <li>
          <Link to="/" className="text-blue-600 hover:underline">
            All
          </Link>
        </li>
        <li>
          <Link to="/?category=React" className="text-blue-600 hover:underline">
            React
          </Link>
        </li>
        <li>
          <Link to="/?category=Node" className="text-blue-600 hover:underline">
            Node
          </Link>
        </li>
        <li>
          <Link to="/?category=CSS" className="text-blue-600 hover:underline">
            CSS
          </Link>
        </li>
        <li>
          <Link
            to="/?category=JavaScript"
            className="text-blue-600 hover:underline"
          >
            JavaScript
          </Link>
        </li>
      </ul>
    </div>
  );
};

export default Sidebar;


//---------------------------------------------------
// BlogList.jsx
import { Link, useSearchParams } from "react-router-dom";
import { blogPosts } from "./blogData";

const BlogList = () => {
  const [searchParams] = useSearchParams();
  const category = searchParams.get("category");

  const filteredPosts = category
    ? blogPosts.filter((post) => post.category === category)
    : blogPosts;

  return (
    <div>
      <h2 className="text-2xl font-semibold mb-4">
        {category ? `Category: ${category}` : "All Blog Posts"}
      </h2>
      <ul className="space-y-4">
        {filteredPosts.length > 0 ? (
          filteredPosts.map((post) => (
            <li key={post.id} className="p-4 bg-white shadow rounded">
              <Link
                to={`/blog/${post.id}`}
                className="text-lg font-medium text-blue-600 hover:underline"
              >
                {post.title}
              </Link>
              <p className="text-gray-500">{post.category}</p>
            </li>
          ))
        ) : (
          <p className="text-gray-500">No posts found in this category.</p>
        )}
      </ul>
    </div>
  );
};

export default BlogList;



//---------------------------------------------------
// BlogDetail.jsx
import { useParams, Link } from "react-router-dom";
import { blogPosts } from "./blogData";

const BlogDetail = () => {
  const { id } = useParams();
  const blog = blogPosts.find((post) => post.id === parseInt(id));

  if (!blog) {
    return <p className="text-gray-500">Blog post not found.</p>;
  }

  return (
    <div className="p-4 bg-white shadow rounded">
      <h2 className="text-2xl font-bold mb-4">{blog.title}</h2>
      <p className="text-gray-500 mb-6">Category: {blog.category}</p>
      <p className="text-gray-700">{blog.content}</p>
      <Link to="/" className="mt-6 inline-block text-blue-600 hover:underline">
        Back to Blog List
      </Link>
    </div>
  );
};

export default BlogDetail;
```

### React Query

### **Définitions**

**React Query** est une bibliothèque puissante pour gérer l'état des données asynchrones dans les applications React. Elle simplifie la récupération, la mise en cache, la synchronisation et la mise à jour des données côté client tout en offrant des fonctionnalités comme les rechargements automatiques et le "background refetching".

---

### **Dans quel cas l'utiliser ?**

1. **Gestion des données asynchrones** :

   - Lorsque votre application effectue des requêtes vers une API REST ou GraphQL pour récupérer ou mettre à jour des données.

2. **Mise en cache de données** :

   - Idéal pour éviter des requêtes répétitives en mettant en cache les réponses API.

3. **Synchronisation en temps réel** :

   - Pratique pour des scénarios où les données doivent être régulièrement mises à jour (par exemple, un tableau de bord en direct).

4. **Simplification du code** :

   - Remplace les solutions classiques comme `useEffect` ou `Redux` pour les données asynchrones.

5. **Gestion des erreurs réseau** :
   - Gestion intégrée des erreurs avec des mécanismes de réessai et des états de chargement.

---

### **Exemples concrets**

#### **Exemple 1 : Fetching de données avec `useQuery`**

```javascript
import { useQuery } from "@tanstack/react-query";

const fetchPosts = async () => {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");
  if (!response.ok) {
    throw new Error("Erreur lors de la récupération des publications");
  }
  return response.json();
};

const Posts = () => {
  const { data, isLoading, error } = useQuery({
    queryKey: ["posts"],
    queryFn: fetchPosts,
    // refetchInterval: 1000, // Rechargement toutes les 5 secondes
  });

  if (isLoading) return <p>Chargement...</p>;
  if (error) return <p>Erreur : {error.message}</p>;

  return (
    <ul>
      {data.slice(0, 5).map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};

export default Posts;
```

#### **Exemple 2 : Mutation avec useMutation**

```jsx
import React, { useState } from "react";
import { useMutation, useQueryClient } from "@tanstack/react-query";

const addUser = async (user) => {
  const response = await fetch("https://jsonplaceholder.typicode.com/users", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(user),
  });
  if (!response.ok) {
    throw new Error("Erreur lors de l'ajout de l'utilisateur");
  }
  return response.json();
};

const AddUser = () => {
  const [name, setName] = useState("");
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: addUser, // Nouvelle syntaxe avec mutationFn
    onSuccess: () => {
      // Réactualiser la liste des utilisateurs
      queryClient.invalidateQueries({ queryKey: ["users"] });
    },
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    mutation.mutate({ name });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Nom de l'utilisateur"
        value={name}
        onChange={(e) => setName(e.target.value)}
        required
      />
      <button type="submit">Ajouter</button>
      {mutation.isLoading && <p>Ajout en cours...</p>}
      {mutation.isError && <p>Erreur : {mutation.error.message}</p>}
      {mutation.isSuccess && <p>Utilisateur ajouté avec succès !</p>}
    </form>
  );
};

export default AddUser;
```

### React Hook Form

### **Définitions**

**React Hook Form** est une bibliothèque qui simplifie la gestion des formulaires dans React. Elle est conçue pour être légère, rapide et facile à intégrer dans vos projets. Elle utilise des hooks pour gérer l'état des formulaires, la validation, la soumission et l'affichage des erreurs sans avoir à manipuler l'état local des champs de manière complexe.

---

### **Dans quel cas l'utiliser ?**

1. **Formulaires complexes** :

   - Lorsque vous devez gérer des formulaires avec plusieurs champs, validations, et erreurs sans que le code ne devienne difficile à maintenir.

2. **Performances** :

   - Idéale pour les formulaires avec un grand nombre de champs ou des mises à jour fréquentes, car elle minimise les rendus inutiles.

3. **Validation côté client** :

   - Si vous avez besoin de validation des champs en temps réel ou au moment de la soumission.

4. **Gestion des erreurs de validation** :
   - Pour afficher les erreurs de validation de manière simple, et les lier aux champs spécifiques.

---

### **Exemples concrets**

#### **Exemple 1 : Formulaire simple avec `useForm`**

```jsx
import React from "react";
import { useForm } from "react-hook-form";

const Form = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>Nom</label>
        <input {...register("name", { required: "Le nom est requis" })} />
        {errors.name && <span>{errors.name.message}</span>}
      </div>

      <div>
        <label>Email</label>
        <input
          {...register("email", {
            required: "L'email est requis",
            pattern: {
              value: /^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}$/,
              message: "Email invalide",
            },
          })}
        />
        {errors.email && <span>{errors.email.message}</span>}
      </div>

      <div>
        <label>Mot de passe</label>
        <input
          type="password"
          {...register("password", {
            required: "Le mot de passe est requis",
            minLength: {
              value: 6,
              message: "Le mot de passe doit avoir au moins 6 caractères",
            },
          })}
        />
        {errors.password && <span>{errors.password.message}</span>}
      </div>

      <button type="submit">Soumettre</button>
    </form>
  );
};

export default Form;
```

#### **Exemple 2 : Utilisation de Controller pour des composants contrôlés**

```jsx
import React from "react";
import { useForm, Controller } from "react-hook-form";
import { TextField, Button, Typography, Box } from "@mui/material";

const MUIForm = () => {
  const {
    control,
    handleSubmit,
    formState: { errors },
  } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <Box
      component="form"
      onSubmit={handleSubmit(onSubmit)}
      sx={{
        maxWidth: 400,
        mx: "auto",
        mt: 4,
        display: "flex",
        flexDirection: "column",
        gap: 2,
      }}
    >
      <Typography variant="h5" textAlign="center">
        Formulaire avec MUI
      </Typography>

      {/* Champ Nom */}
      <Controller
        name="name"
        control={control}
        rules={{ required: "Le nom est requis" }}
        render={({ field }) => (
          <TextField
            {...field}
            label="Nom"
            variant="outlined"
            error={!!errors.name}
            helperText={errors.name?.message}
          />
        )}
      />

      {/* Champ Email */}
      <Controller
        name="email"
        control={control}
        rules={{
          required: "L'email est requis",
          pattern: {
            value: /^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}$/,
            message: "Email invalide",
          },
        }}
        render={({ field }) => (
          <TextField
            {...field}
            label="Email"
            variant="outlined"
            error={!!errors.email}
            helperText={errors.email?.message}
          />
        )}
      />

      {/* Champ Mot de passe */}
      <Controller
        name="password"
        control={control}
        rules={{
          required: "Le mot de passe est requis",
          minLength: {
            value: 6,
            message: "Le mot de passe doit contenir au moins 6 caractères",
          },
        }}
        render={({ field }) => (
          <TextField
            {...field}
            type="password"
            label="Mot de passe"
            variant="outlined"
            error={!!errors.password}
            helperText={errors.password?.message}
          />
        )}
      />

      {/* Bouton Soumettre */}
      <Button variant="contained" color="primary" type="submit">
        Soumettre
      </Button>
    </Box>
  );
};

export default MUIForm;
```

## Typescript

### **Définition**

TypeScript est une extension de JavaScript qui introduit un système de typage statique, permettant de détecter les erreurs de type avant l'exécution. Cela améliore la fiabilité et la maintenabilité du code tout en offrant des outils puissants pour le développement moderne.

---

### **Dans quel cas l'utiliser ?**

1. **Amélioration de la lisibilité et de la robustesse du code** : TypeScript aide à documenter clairement les intentions avec des types explicites.
2. **Développement d'applications complexes** : Particulièrement utile dans des projets avec de nombreuses interactions entre les modules et API.
3. **Réduction des bugs** : Permet de détecter les erreurs dès la compilation au lieu d'attendre l'exécution.
4. **Travail en équipe** : Fournit des contrats clairs pour les interfaces et fonctions, facilitant la collaboration.

---

### **Exemple concret**

#### **Exemple 1 : Typer un composant**

```typescript
import { ReactNode } from "react";

interface MyButtonProps {
  title: string;
  disabled: boolean;
}

function MyButton({ title, disabled }: MyButtonProps) {
  return <button disabled={disabled}>{title}</button>;
}

export default function MyApp({
  children,
  styles,
}: {
  children: ReactNode;
  styles: React.CSSProperties;
}) {
  // Infère le type "boolean"
  // const [enabled, setEnabled] = useState(false);
  // const [status, setStatus] = useState<Status>("idle");

  return (
    <div style={{ ...styles }}>
      {children}
      <MyButton title="I'm a disabled button" disabled={true} />
    </div>
  );
}

// useState
// Typage explicite à "boolean"
// const [enabled, setEnabled] = useState<boolean>(false);

// type Status = "idle" | "loading" | "success" | "error";
// const handleStatus = () => setStatus("koko");
```

#### **Exemple 2 : Tapez un événement**

```typescript
import { useState } from "react";

export default function Form() {
  const [value, setValue] = useState("Modifiez-moi");

  function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    setValue(event.currentTarget.value);
  }

  return (
    <>
      <input value={value} onChange={handleChange} />
      <p>Valeur : {value}</p>
    </>
  );
}
```

#### **Exemple 3 : Typer useRef**

```typescript
import React, { useRef } from "react";

const InputFocus: React.FC = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleFocus = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={handleFocus}>Focus Input</button>
    </div>
  );
};

export default InputFocus;
```

#### **Exemple 4 : Typer useContext**

```typescript
import {
  createContext,
  useContext,
  useState,
  Dispatch,
  SetStateAction,
} from "react";

type Theme = "light" | "dark" | "system";
interface contextProps {
  theme: Theme;
  setTheme?: Dispatch<SetStateAction<Theme>>;
}
const ThemeContext = createContext<contextProps>({
  theme: "light",
});

const useGetTheme = () => useContext(ThemeContext);

export default function MyApp() {
  const [theme, setTheme] = useState<Theme>("light");

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <MyComponent />
    </ThemeContext.Provider>
  );
}

function MyComponent() {
  const { theme } = useGetTheme();

  return (
    <div>
      <p>Thème actif : {theme}</p>
    </div>
  );
}
```

#### **Exemple 5 : Typer useReducer**

```typescript
import { useReducer } from "react";

interface State {
  count: number;
}

type CounterAction =
  | { type: "reset" }
  | { type: "setCount"; value: State["count"] };

const initialState: State = { count: 0 };

function stateReducer(state: State, action: CounterAction): State {
  switch (action.type) {
    case "reset":
      return initialState;
    case "setCount":
      return { ...state, count: action.value };
    default:
      throw new Error("Unknown action");
  }
}

export default function App() {
  const [state, dispatch] = useReducer(stateReducer, initialState);

  const addFive = () => dispatch({ type: "setCount", value: state.count + 5 });
  const reset = () => dispatch({ type: "reset" });

  return (
    <div>
      <h1>Bienvenue dans mon compteur</h1>

      <p>Compteur : {state.count}</p>
      <button onClick={addFive}>Ajouter 5</button>
      <button onClick={reset}>Réinitialiser</button>
    </div>
  );
}
```

## Tests unitaire

### **Définition**

Les tests unitaires sont une méthode de test logiciel où les plus petites unités de code (comme les fonctions ou les composants) sont testées individuellement pour vérifier qu'elles fonctionnent comme prévu. Ils garantissent la fiabilité du code et facilitent la maintenance.

---

### **Dans quel cas les utiliser ?**

1. **Validation des fonctionnalités de base** : Assurez-vous qu'une fonction ou un composant réagit correctement aux entrées prévues.
2. **Refactoring ou ajout de nouvelles fonctionnalités** : Les tests unitaires protègent contre les régressions lorsque vous modifiez le code.
3. **Projets critiques ou complexes** : Lorsqu'une panne ou un bug peut avoir des conséquences importantes, les tests unitaires réduisent les risques.
4. **Travail collaboratif** : Assurez que les modifications des autres développeurs n'introduisent pas d'erreurs.

---

### **Exemples concrets**

#### **Configuration**

```
npm install jsdom --save-dev
npm install vitest @testing-library/react @testing-library/jest-dom

package.json :
"scripts": {
  "test": "vitest"
}

vitest.setup.ts:
import '@testing-library/jest-dom';

tsconfig.node.json:
"compilerOptions": {
  "types": ["vitest"]
},

vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// Add Vitest type imports
/// <reference types="vitest" />
import type { UserConfig } from "vite";

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom", // Required for React testing
    setupFiles: "./vitest.setup.ts", // Optional: path to setup file
  },
} as UserConfig); // Add this to ensure `test` is recognized


```

#### **Exemple 1 : Test de base**

```typescript
// Button.tsx
import React from "react";

type ButtonProps = {
  label: string;
  onClick: () => void;
};

export const Button: React.FC<ButtonProps> = ({ label, onClick }) => (
  <button onClick={onClick}>{label}</button>
);

// Button.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { describe, it, expect, vi } from "vitest";
import { Button } from "./Button";

describe("Button component", () => {
  it("renders the button with the correct label", () => {
    render(<Button label="Click Me" onClick={() => {}} />);
    expect(screen.getByText("Click Me")).toBeInTheDocument();
  });

  it("calls onClick when clicked", () => {
    const onClick = vi.fn(); // Mock function
    render(<Button label="Click Me" onClick={onClick} />);
    fireEvent.click(screen.getByText("Click Me"));
    expect(onClick).toHaveBeenCalledTimes(1);
  });
});
```

#### **Exemple 2 : Tester le changement de state**

```typescript
// Counter.tsx
import { useState } from "react";

export const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount((prev) => prev + 1)}>Increment</button>
    </div>
  );
};

// Counter.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { describe, it, expect } from "vitest";
import { Counter } from "./Counter";

describe("Counter component", () => {
  it("increments the count when the button is clicked", () => {
    render(<Counter />);
    const button = screen.getByText("Increment");
    fireEvent.click(button);
    expect(screen.getByText("Count: 1")).toBeInTheDocument();
    fireEvent.click(button);
    expect(screen.getByText("Count: 2")).toBeInTheDocument();
  });
});
```

#### **Exemple 3 : Tester un hook**

```typescript
// useCounter.tsx
import { useState } from "react";

export function useCounter(initialValue: number = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount((prev) => prev + 1);
  const decrement = () => setCount((prev) => prev - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// useCounter.test.tsx
import { renderHook, act } from "@testing-library/react";
import { describe, it, expect } from "vitest";
import { useCounter } from "./useCounter";

describe("useCounter", () => {
  it("should initialize with the default value", () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  it("should initialize with a custom initial value", () => {
    const { result } = renderHook(() => useCounter(5));
    expect(result.current.count).toBe(5);
  });

  it("should increment the count", () => {
    const { result } = renderHook(() => useCounter());

    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });

  it("should decrement the count", () => {
    const { result } = renderHook(() => useCounter(3));

    act(() => {
      result.current.decrement();
    });

    expect(result.current.count).toBe(2);
  });

  it("should reset the count to the initial value", () => {
    const { result } = renderHook(() => useCounter(10));

    act(() => {
      result.current.increment();
      result.current.increment();
      result.current.reset();
    });

    expect(result.current.count).toBe(10);
  });
});
```

## Aller plus loin

### Next.js

### **Définition**

Next.js est un framework React qui permet de créer des applications web performantes et évolutives. Il offre des fonctionnalités avancées comme le rendu côté serveur (SSR), la génération statique de pages (SSG), la gestion des routes et une optimisation automatique des performances. Next.js est conçu pour faciliter le développement d'applications web tout en offrant une expérience utilisateur rapide et fluide.

### **Dans quel cas l'utiliser ?**

1. **Applications avec SEO optimisé** : Utilisé pour les sites web qui nécessitent un rendu côté serveur (SSR) pour un meilleur référencement.
2. **Applications avec des pages statiques** : Génération de pages statiques (SSG) pour un chargement ultra-rapide.
3. **Applications avec du rendu dynamique** : Prise en charge du rendu côté client pour des pages dynamiques avec une navigation fluide.
4. **Projets fullstack** : Permet de gérer le frontend et le backend dans un même projet grâce à ses API routes.

### Remix

### **Définition**

Remix est un framework web basé sur React qui permet de créer des applications web modernes en tirant parti de la puissance des navigateurs et des serveurs. Il se concentre sur la performance, la gestion des données et l'expérience utilisateur en optimisant les chargements de page, en offrant des rendus côté serveur (SSR) et en facilitant la gestion des erreurs et des transitions de navigation.

### **Dans quel cas l'utiliser ?**

1. **Applications avec des besoins de rendu côté serveur (SSR)** : Remix gère efficacement le rendu des pages côté serveur, ce qui est idéal pour les applications nécessitant un bon SEO.
2. **Applications avec une navigation fluide** : Remix permet un chargement rapide des pages et une expérience de navigation fluide en utilisant des techniques avancées comme la mise en cache des données et le chargement différé.
3. **Gestion des erreurs et des formulaires** : Il propose des mécanismes efficaces pour gérer les erreurs et la soumission des formulaires, ce qui simplifie le développement des applications complexes.
4. **Applications avec une architecture fullstack** : Remix permet de construire des applications fullstack tout en offrant des fonctionnalités avancées pour gérer les données et l'état de l'application.

---

### Merci pour votre attention
