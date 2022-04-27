# Привет пацаны

Нашел очень интересную вещь в ts. Думаю, вам стоит заценить. 

## Тема: Convenience generic 

Сразу к примерам, чего булки мять.

Функция, которая "разворачивает" массив. Vanilla JS:
```javascript
function reverse(items) {
  const turnedArr = [];

  for (let i = items.length - 1; i >= 0; i--) {
    turnedArr.push(items[i]);
  }

  return turnedArr;
}
```

Теперь интересно. На ts:
```typescript
function reverse<T>(items: T[]): T[] {
  const turnedArr = [];

  for (let i = items.length - 1; i >= 0; i--) {
    turnedArr.push(items[i]);
  }

  return turnedArr;
}
```

***
Вопрос: че это за хрень: ```reverse<T>```. А вот, функции тоже могут принимать generic'и, прямо как типы и массивы.

(Пример, чтобы освежить память):
```typescript
type MyArrayType<T> = {
  items: T[];
};
// or 
interface MyArrayType<T> {
  items: T[];
}
```

**Суть** в том, что мы можем создавать типизированные функции, в которые бы только один раз пропишем тип, и он будет использоваться и как тип в аргументах, и как возвращаемый тип. 

Возвращаясь к примеру на ts, generic после названия функции (reverse) - это как бы его объявление. Дальше мы можем его использовать в типах аргументов, в возвращаемом типе и в теле функции. 

Из-за generic'а эта функция может принимать массив любого типа.

Можно использовать явно:
```typescript
console.log(reverse<number>([1, 2, 3])) // [3, 2, 1]
```

А можно предоставить определение типа самому ts:
```typescript
console.log(reverse([1, 2, 3])) // [3, 2, 1] | работает так же
```

***

## Момент со стрелочными функциями
Просто так не получится, ts выкинет ошибку.
```typescript
const reverse = <T>(items: T[]) => {...}
```
Туторы предлагают делать так (и оно норм):
```typescript
const reverse = <T extends unknown>(items: T[]) => {...}
// or 
const reverse = <T extends {}>(items: T[]) => {...}
// or
const reverse = <T,>(items: T[]) => {...} // the best

```

***
## Примеры

### Тупо вернуть, что принимаем
```typescript
const foo = <T,>(x: T) => x;
```

### Функция, возвращающая *json response*

```typescript
// через async await
const getJSON = async <T,>(url: string): Promise<T> => {
  const res = await fetch(url);
  return await res.json();
}

// через promise chaining
const getJSON = <T,>(url: string): Promise<T> => {
  return fetch(url)
    .then<T>(res => res.json());
}

// Usage
interface Item {
  id: number;
  name: string;
  isCompleted: boolean;
  parentId?: number;
  children: Item[];
}

const apiResponse = getJSON<Item>("http://localhost:8080/items/2");
```

### Компонент-таблица в react, котоая рендерит что угодно
```typescript
interface ITableProps<TItem> {
  items: TItem[];
  renderItem: (item: TItem) => React.ReactNode;
}

// нужно именно так, через React.FC не выйдет, т.к требуется generic, а мы его еще не написали
export const Table = <TItem, >(props: TableProps<TItem>) => {
  return null;
}

export const SomeComponent = () => {
  return (
    <>
      <Table 
        items={[ { id: 1, firstName: "iluha" }]}
        renderItems={(item) => {
          return null;
        }}
      />
      <Table 
        items={[ { id: 1, species: "rabbit" }]}
        renderItems={(item) => {
          return null;
        }}
      />
    </Table>
  );
}
```

## Ресурсы
* https://basarat.gitbook.io/typescript/type-system/generics#design-pattern-convenience-generic
* https://www.youtube.com/watch?v=hBk4nV7q6-w
