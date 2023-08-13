# Задачи по React
## №1 Реализовать счетчик на функциональных компонентах.
```
import { useState } from "react";

export default function App() {
  const [counter, setCounter] = useState(0);

  const changeCounterValue = (value) => () => {
    setCounter((prevCounter) => prevCounter + value);
  };

  return (
    <div className="App">
      <button onClick={changeCounterValue(1)}>увеличить</button>
      <button onClick={changeCounterValue(-1)}>уменьшить</button>
      <h1>{counter}</h1>
    </div>
  );
}
```

## №2 Отрефакторить компонент
```
import { useState, useEffect } from 'react'
import sendMetric from 'metrics';
import sendData from 'data';
import bigComputations from 'utils';

const pleaseReviewMe = ({ argument }) => {
    const [data, setDate] = useState(() => bigComputations(argument));
    const items = [{ id: 1 }, { id: 2 }, { id: 3 }];

    useEffect(() => {
        const handleClick = () => {
            sendMetric('click');
        }

        document.addEventListener('click', handleClick);

        return () => {
            document.removeEventListener('click', handleClick);
        };
    }, []);

    const handleItemClick = id => () => {
        sendData(data, id);
    };

    return (
        <>
            {items.map(({id}) => (
                <div onClick={handleItemClick(id)} key={id}>{id}</div>
            ))}
        </>
    );
};

export default pleaseReviewMe;
```

## №3 Отрефакторить компонент
```
import {useState, useEffect} from "react";

export default function PleaseReviewMe() {
    const [count, setCount] = useState(1);
    const [items, setItems] = useState([{ id: 1 }]);

    const handleClick = () => {
        setCount(prevCount => prevCount + 1);
        setItems(prevItems => [...prevItems, { id: count + 1 }]);
    };

    useEffect(() => {
        const intervalId = setInterval(() => console.log(count), 1000);
        return () => clearInterval(intervalId);
    }, [count])

    return (
        <>
            <ul>
                {items.map(({id}) => (
                    <li key={id}>{id}</li>
                ))}
            </ul>
            <button onClick={handleClick}>add one</button>
        </>
    );
};
```

## №4 Сколько будет произведено ререндеров при обновлении состояния и почему?

**Один** Потому что реакт обхединит несколько изменений стейта в один (батчинг)

## №5 Реализовать хук useFirstRender. При первом рендере хук должен возвращать true, при последующих рендерах возвращать false

```
import { useRef } from 'react';

const useFirstRender = () => {
  const ref = useRef(true);

  if (ref.current) {
    ref.current = false;

    return true;
  }

  return ref.current;
};
```

## №6 Вывести значения полей в консоль, при клиике на форму, учитывая что первый input controlled, а второй input uncontrolled.

```
import { useState, useRef } from "react";

export default () => {
  const [controlledValue, setControlledValue] = useState("");
  const inputRef = useRef("");

  const onClickForm = () => {
    console.log(`controlled: ${controlledValue}`);
    console.log(`uncontrolled: ${inputRef.current.value}`);
  };

  const onChangeText = (e) => {
    setControlledValue(e.target.value);
  };

  return (
    <form onClick={onClickForm}>
      <input
        onChange={onChangeText}
        placeholder="controlled"
        value={controlledValue}
      />
      <input ref={inputRef} placeholder="uncontrolled" />

      <button>Отправить заявку на кредит</button>
    </form>
  );
};
```

## №7 Как сработает при первом и последующем рендере?

При первом рендере будет выведена единица в консоль, при последующих - ничего, т.к массив зависимостей пустой. При размонтировании компонента будет выведена 2

## №8 переписать, чтобы вызывался ререндер

```
import { useState } from 'react';

const Test = () => {
    const [count, setCount] = useState(0);

    const handleIncreaseCount = () => {
        setCount((prevCount) => prevCount + 1);
    };


    return (
        <>
            <h1>{count}</h1>
            <button onClick={handleIncreaseCount}>Increase</button>
        </>
    );
};
```

## №9 Сколько будет произведено ререндеров при обновлении состояния и почему?
один, потому что у реакта есть такая фича как батчинг (объединение изменения нескольких стейтов в один)

## №10 
Необходимо создать React-компонент, который отображает продукты в таблице, позволяя пользователям фильтровать продукты по названию и по категориям.

- Создать компонент "ProductTable", который отображает продукты в формате таблицы.
- Реализовать компонент "SearchBar" для поиска по названию товара и категории.
- Продукты должны фильтроваться на основе данных, полученных от компонента "SearchBar".
- Фильтр должен быть нечувствительным к регистру и обновлять таблицу в реальном времени по мере ввода пользователем текста.

```
import React, { useState } from "react";

const products = [
  { name: "Apple", price: 1, category: "Fruit" },
  { name: "Banana", price: 0.5, category: "Fruit" },
  { name: "Carrot", price: 0.3, category: "Vegetable" },
  { name: "Broccoli", price: 1.2, category: "Vegetable" }
];

const SearchBar = ({ onFilter }) => {
  return <input onChange={onFilter} placeholder="product name/category" />;
};

const ProductTable = ({ products }) => {
  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Category</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {products.map(({ name, price, category }) => (
          <tr key={name}>
            <td>{name}</td>
            <td>{category}</td>
            <td>{price}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
};

const FilterableProductTable = () => {
  const [currentProducts, setCurrentProducts] = useState(products);

  const onFilter = (e) => {
    const searchValue = e.target.value.toLowerCase();

    const newProducts = products.filter((item) => {
      const { name, category } = item;
      if (
        name.toLowerCase().includes(searchValue) ||
        category.toLowerCase().includes(searchValue)
      ) {
        return item;
      }

      return null;
    });

    setCurrentProducts(newProducts);
  };

  return (
    <div>
      <SearchBar onFilter={onFilter} />
      {currentProducts.length ? (
        <ProductTable products={currentProducts} />
      ) : (
        <h1>Not found</h1>
      )}
    </div>
  );
};

export default FilterableProductTable;
```

## №11 как сделать, чтобы компонента отрендерилась 2 раза, когда у нас в одной функции устанавливается 2 state

Думаю, можно использовать хук useEffect. 

useEffect(() => { 
// можно поместить какую-либо логику, которая будет выполнена когда state1 или state2 изменится
}, [state1, state2])

## №12

Ре-рендер будет и у компонента A, и у компонента B. У компонента A потому что будет изменено состояния data, а компонент B перерендерится потому что будет изменение контекста
