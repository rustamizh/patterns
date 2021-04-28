# Паттерны проектирования (с примерами на TypeScript)

## [Порождающие](#creational)

<details>
<summary>Оглавление паттернов</summary>

- [Фабричный метод](#factory-method)

- [Абстрактная фабрика](#abstract-factory)

- [Строитель](#builder)

- [Прототип](#prototype)

- [Одиночка](#singleton)
</details>

## [Структурные](#structural)

<details>
<summary>Оглавление паттернов</summary>

- [Адаптер](#adapter)

- [Мост](#bridge)

- [Компоновщик](#composite)

- [Декоратор](#decorator)

- [Фасад](#faсade)

- [Легковес](#flyweight)

- [Заместитель](#proxy)
</details>

## [Поведенческие](#behavioral)

<details>
<summary>Оглавление паттернов</summary>

- [Стратегия](#strategy)

- [Шаблонный метод](#template-method)

- [Наблюдатель](#observer)

- [Посредник](#mediator)

- [Итератор](#iterator)

- [Посетитель](#visitor)

- [Цепочка обязанностей](#chain-of-responsibility)

- [Команда](#command)

- [Снимок](#memento)

- [Состояние](#state)
</details>

## <a name="behavioral"></a> Поведенческие

### <a name="strategy"></a> Стратегия

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/strategy)

**Назначение**: определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Стратегия позволяет изменять алгоритмы независимо от клиентов, которые ими пользуются.

**Другими словами**: стратегия инкапсулирует определенное поведение с возможностью его подмены.

Паттерн Стратегия предлагает определить семейство схожих алгоритмов, которые часто изменяются или расширяются, и вынести их в собственные классы, называемые стратегиями.

Вместо того, чтобы изначальный класс сам выполнял тот или иной алгоритм, он будет играть роль контекста, ссылаясь на одну из стратегий и делегируя ей выполнение работы. Чтобы сменить алгоритм, вам будет достаточно подставить в контекст другой объект-стратегию.

Важно, чтобы все стратегии имели общий интерфейс. Используя этот интерфейс, контекст будет независимым от конкретных классов стратегий. С другой стороны, вы сможете изменять и добавлять новые виды алгоритмов, не трогая код контекста.

<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Контекст определяет интерфейс, представляющий интерес для клиентов.
 */
class Context {
    /**
     * @type {Strategy} Контекст хранит ссылку на один из объектов Стратегии.
     * Контекст не знает конкретного класса стратегии. Он должен работать со
     * всеми стратегиями через интерфейс Стратегии.
     */
    private strategy: Strategy;

    /**
     * Обычно Контекст принимает стратегию через конструктор, а также
     * предоставляет сеттер для её изменения во время выполнения.
     */
    constructor(strategy: Strategy) {
        this.strategy = strategy;
    }

    /**
     * Обычно Контекст позволяет заменить объект Стратегии во время выполнения.
     */
    public setStrategy(strategy: Strategy) {
        this.strategy = strategy;
    }

    /**
     * Вместо того, чтобы самостоятельно реализовывать множественные версии
     * алгоритма, Контекст делегирует некоторую работу объекту Стратегии.
     */
    public doSomeBusinessLogic(): void {
        // ...

        console.log('Context: Sorting data using the strategy (not sure how it\'ll do it)');
        const result = this.strategy.doAlgorithm(['a', 'b', 'c', 'd', 'e']);
        console.log(result.join(','));

        // ...
    }
}

/**
 * Интерфейс Стратегии объявляет операции, общие для всех поддерживаемых версий
 * некоторого алгоритма.
 *
 * Контекст использует этот интерфейс для вызова алгоритма, определённого
 * Конкретными Стратегиями.
 */
interface Strategy {
    doAlgorithm(data: string[]): string[];
}

/**
 * Конкретные Стратегии реализуют алгоритм, следуя базовому интерфейсу
 * Стратегии. Этот интерфейс делает их взаимозаменяемыми в Контексте.
 */
class ConcreteStrategyA implements Strategy {
    public doAlgorithm(data: string[]): string[] {
        return data.sort();
    }
}

class ConcreteStrategyB implements Strategy {
    public doAlgorithm(data: string[]): string[] {
        return data.reverse();
    }
}

/**
 * Клиентский код выбирает конкретную стратегию и передаёт её в контекст. Клиент
 * должен знать о различиях между стратегиями, чтобы сделать правильный выбор.
 */
const context = new Context(new ConcreteStrategyA());
console.log('Client: Strategy is set to normal sorting.');
context.doSomeBusinessLogic();

console.log('');

console.log('Client: Strategy is set to reverse sorting.');
context.setStrategy(new ConcreteStrategyB());
context.doSomeBusinessLogic();
```

</details>

### <a name="template-method"></a> Шаблонный метод

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/template-method)

**Назначение**: шаблонный метод определяет основу алгоритма и позволяет подклассам переопределять некоторые шаги алгоритма, не изменяя его структуры в целом.

**Другими словами**: шаблонный метод — это каркас, в который наследники могут
подставить реализации недостающих элементов.

Шаблонный метод — это поведенческий паттерн проектирования, который определяет скелет алгоритма, перекладывая ответственность за некоторые его шаги на подклассы. Паттерн позволяет подклассам переопределять шаги алгоритма, не меняя его общей структуры.

Паттерн Шаблонный метод предлагает разбить алгоритм на последовательность шагов, описать эти шаги в отдельных методах и вызывать их в одном шаблонном методе базового класса друг за другом.

Это позволит подклассам переопределять некоторые шаги алгоритма, оставляя без изменений его структуру и остальные шаги, которые для этого подкласса не так важны.

Для начала шаги шаблонного метода можно сделать абстрактными. Из-за этого все подклассы должны будут реализовать каждый из шагов

По-настоящему важным является следующий этап. Теперь мы можем определить общее для всех классов поведение и вынести его в суперкласс. Например шаги открытия, считывания и закрытия документа могут отличаться для разных типов документов, поэтому останутся абстрактными. А вот одинаковый для всех типов документов код обработки данных переедет в базовый класс.

Как видите, у нас получилось два вида шагов: абстрактные, которые каждый подкласс обязательно должен реализовать, а также шаги с реализацией по умолчанию, которые можно переопределять в подклассах, но не обязательно.

Но есть и третий тип шагов — хуки: их не обязательно переопределять, но они не содержат никакого кода, выглядя как обычные методы. Шаблонный метод останется рабочим, даже если ни один подкласс не переопределит такой хук. Однако, хук даёт подклассам дополнительные точки «вклинивания» в шаблонный метод.

**Абстрактный класс** определяет шаги алгоритма и содержит шаблонный метод, состоящий из вызовов этих шагов. Шаги могут быть как абстрактными, так и содержать реализацию по умолчанию.

**Конкретный класс** переопределяет некоторые (или все) шаги алгоритма. Конкретные классы не переопределяют сам шаблонный метод.

<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Абстрактный Класс определяет шаблонный метод, содержащий скелет некоторого
 * алгоритма, состоящего из вызовов (обычно) абстрактных примитивных операций.
 *
 * Конкретные подклассы должны реализовать эти операции, но оставить сам
 * шаблонный метод без изменений.
 */
abstract class AbstractClass {
    /**
     * Шаблонный метод определяет скелет алгоритма.
     */
    public templateMethod(): void {
        this.baseOperation1();
        this.requiredOperations1();
        this.baseOperation2();
        this.hook1();
        this.requiredOperation2();
        this.baseOperation3();
        this.hook2();
    }

    /**
     * Эти операции уже имеют реализации.
     */
    protected baseOperation1(): void {
        console.log('AbstractClass says: I am doing the bulk of the work');
    }

    protected baseOperation2(): void {
        console.log('AbstractClass says: But I let subclasses override some operations');
    }

    protected baseOperation3(): void {
        console.log('AbstractClass says: But I am doing the bulk of the work anyway');
    }

    /**
     * А эти операции должны быть реализованы в подклассах.
     */
    protected abstract requiredOperations1(): void;

    protected abstract requiredOperation2(): void;

    /**
     * Это «хуки». Подклассы могут переопределять их, но это не обязательно,
     * поскольку у хуков уже есть стандартная (но пустая) реализация. Хуки
     * предоставляют дополнительные точки расширения в некоторых критических
     * местах алгоритма.
     */
    protected hook1(): void { }

    protected hook2(): void { }
}

/**
 * Конкретные классы должны реализовать все абстрактные операции базового
 * класса. Они также могут переопределить некоторые операции с реализацией по
 * умолчанию.
 */
class ConcreteClass1 extends AbstractClass {
    protected requiredOperations1(): void {
        console.log('ConcreteClass1 says: Implemented Operation1');
    }

    protected requiredOperation2(): void {
        console.log('ConcreteClass1 says: Implemented Operation2');
    }
}

/**
 * Обычно конкретные классы переопределяют только часть операций базового
 * класса.
 */
class ConcreteClass2 extends AbstractClass {
    protected requiredOperations1(): void {
        console.log('ConcreteClass2 says: Implemented Operation1');
    }

    protected requiredOperation2(): void {
        console.log('ConcreteClass2 says: Implemented Operation2');
    }

    protected hook1(): void {
        console.log('ConcreteClass2 says: Overridden Hook1');
    }
}

/**
 * Клиентский код вызывает шаблонный метод для выполнения алгоритма. Клиентский
 * код не должен знать конкретный класс объекта, с которым работает, при
 * условии, что он работает с объектами через интерфейс их базового класса.
 */
function clientCode(abstractClass: AbstractClass) {
    // ...
    abstractClass.templateMethod();
    // ...
}

console.log('Same client code can work with different subclasses:');
clientCode(new ConcreteClass1());
console.log('');

console.log('Same client code can work with different subclasses:');
clientCode(new ConcreteClass2());
```

</details>

### <a name="observer"></a> Наблюдатель

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/observer)

**Назначение**: определяет зависимость типа «один ко многим» между объектами
таким образом, что при изменении состояния одного объекта все зависящие от него
оповещаются об этом и автоматически обновляются.

**Другими словами**: наблюдатель уведомляет все заинтересованные стороны о произошедшем событии или об изменении своего состояния.

Паттерн Наблюдатель предлагает хранить внутри объекта издателя список ссылок на объекты подписчиков, причём издатель не должен вести список подписки самостоятельно. Он предоставит методы, с помощью которых подписчики могли бы добавлять или убирать себя из списка.

Когда в издателе будет происходить важное событие, он будет проходиться по списку подписчиков и оповещать их об этом, вызывая определённый метод объектов-подписчиков.

Издателю безразлично, какой класс будет иметь тот или иной подписчик, так как все они должны следовать общему интерфейсу и иметь единый метод оповещения.

Увидев, как складно всё работает, вы можете выделить общий интерфейс, описывающий методы подписки и отписки, и для всех издателей. После этого подписчики смогут работать с разными типами издателей, а также получать оповещения от них через один и тот же метод.

<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Интферфейс издателя объявляет набор методов для управлениями подписчиками.
 */
interface Subject {
    // Присоединяет наблюдателя к издателю.
    attach(observer: Observer): void;

    // Отсоединяет наблюдателя от издателя.
    detach(observer: Observer): void;

    // Уведомляет всех наблюдателей о событии.
    notify(): void;
}

/**
 * Издатель владеет некоторым важным состоянием и оповещает наблюдателей о его
 * изменениях.
 */
class ConcreteSubject implements Subject {
    /**
     * @type {number} Для удобства в этой переменной хранится состояние
     * Издателя, необходимое всем подписчикам.
     */
    public state: number;

    /**
     * @type {Observer[]} Список подписчиков. В реальной жизни список
     * подписчиков может храниться в более подробном виде (классифицируется по
     * типу события и т.д.)
     */
    private observers: Observer[] = [];

    /**
     * Методы управления подпиской.
     */
    public attach(observer: Observer): void {
        const isExist = this.observers.includes(observer);
        if (isExist) {
            return console.log('Subject: Observer has been attached already.');
        }

        console.log('Subject: Attached an observer.');
        this.observers.push(observer);
    }

    public detach(observer: Observer): void {
        const observerIndex = this.observers.indexOf(observer);
        if (observerIndex === -1) {
            return console.log('Subject: Nonexistent observer.');
        }

        this.observers.splice(observerIndex, 1);
        console.log('Subject: Detached an observer.');
    }

    /**
     * Запуск обновления в каждом подписчике.
     */
    public notify(): void {
        console.log('Subject: Notifying observers...');
        for (const observer of this.observers) {
            observer.update(this);
        }
    }

    /**
     * Обычно логика подписки – только часть того, что делает Издатель. Издатели
     * часто содержат некоторую важную бизнес-логику, которая запускает метод
     * уведомления всякий раз, когда должно произойти что-то важное (или после
     * этого).
     */
    public someBusinessLogic(): void {
        console.log('\nSubject: I\'m doing something important.');
        this.state = Math.floor(Math.random() * (10 + 1));

        console.log(`Subject: My state has just changed to: ${this.state}`);
        this.notify();
    }
}

/**
 * Интерфейс Наблюдателя объявляет метод уведомления, который издатели
 * используют для оповещения своих подписчиков.
 */
interface Observer {
    // Получить обновление от субъекта.
    update(subject: Subject): void;
}

/**
 * Конкретные Наблюдатели реагируют на обновления, выпущенные Издателем, к
 * которому они прикреплены.
 */
class ConcreteObserverA implements Observer {
    public update(subject: Subject): void {
        if (subject instanceof ConcreteSubject && subject.state < 3) {
            console.log('ConcreteObserverA: Reacted to the event.');
        }
    }
}

class ConcreteObserverB implements Observer {
    public update(subject: Subject): void {
        if (subject instanceof ConcreteSubject && (subject.state === 0 || subject.state >= 2)) {
            console.log('ConcreteObserverB: Reacted to the event.');
        }
    }
}

/**
 * Клиентский код.
 */

const subject = new ConcreteSubject();

const observer1 = new ConcreteObserverA();
subject.attach(observer1);

const observer2 = new ConcreteObserverB();
subject.attach(observer2);

subject.someBusinessLogic();
subject.someBusinessLogic();

subject.detach(observer2);
```

</details>

### <a name="mediator"></a> Посредник

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/mediator)

**Назначение**: определяет объект, инкапсулирующий способ взаимодействия множества объектов.

**Другими словами**: посредник — это клей, связывающий несколько независимых
классов между собой. Он избавляет классы от необходимости ссылаться друг на
друга, позволяя тем самым их независимо изменять и анализировать.

Паттерн Посредник заставляет объекты общаться не напрямую друг с другом, а через отдельный объект-посредник, который знает, кому нужно перенаправить тот или иной запрос. Благодаря этому, компоненты системы будут зависеть только от посредника, а не от десятков других компонентов.

Если раньше при получении клика от пользователя объект кнопки сам проверял значения полей, то теперь его единственной обязанностью будет сообщить посреднику о том, что произошёл клик. Получив извещение, посредник выполнит все необходимые проверки полей. Таким образом, вместо нескольких зависимостей от остальных элементов кнопка получит только одну — от самого посредника.

Чтобы сделать код ещё более гибким, можно выделить общий интерфейс для всех посредников. Наша кнопка станет зависимой не от конкретного посредника создания пользователя, а от абстрактного, что позволит использовать её и с другими посредниками.

Таким образом, посредник скрывает в себе все сложные связи и зависимости между классами отдельных компонентов программы. А чем меньше связей имеют классы, тем проще их изменять, расширять и повторно использовать.

Посредник содержит код взаимодействия нескольких компонентов между собой. Зачастую этот объект не только хранит ссылки на все свои компоненты, но и сам их создаёт, управляя дальнейшим жизненным циклом.

Компоненты не должны общаться друг с другом напрямую. Если в компоненте происходит важное событие, он должен оповестить своего посредника, а тот сам решит — касается ли событие других компонентов, и стоит ли их оповещать. При этом компонент-отправитель не знает кто обработает его запрос, а компонент-получатель не знает кто его прислал.

Посредник может сильно раздуться.

<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Интерфейс Посредника предоставляет метод, используемый компонентами для
 * уведомления посредника о различных событиях. Посредник может реагировать на
 * эти события и передавать исполнение другим компонентам.
 */
interface Mediator {
    notify(sender: object, event: string): void;
}

/**
 * Конкретные Посредники реализуют совместное поведение, координируя отдельные
 * компоненты.
 */
class ConcreteMediator implements Mediator {
    private component1: Component1;

    private component2: Component2;

    constructor(c1: Component1, c2: Component2) {
        this.component1 = c1;
        this.component1.setMediator(this);
        this.component2 = c2;
        this.component2.setMediator(this);
    }

    public notify(sender: object, event: string): void {
        if (event === 'A') {
            console.log('Mediator reacts on A and triggers following operations:');
            this.component2.doC();
        }

        if (event === 'D') {
            console.log('Mediator reacts on D and triggers following operations:');
            this.component1.doB();
            this.component2.doC();
        }
    }
}

/**
 * Базовый Компонент обеспечивает базовую функциональность хранения экземпляра
 * посредника внутри объектов компонентов.
 */
class BaseComponent {
    protected mediator: Mediator;

    constructor(mediator: Mediator = null) {
        this.mediator = mediator;
    }

    public setMediator(mediator: Mediator): void {
        this.mediator = mediator;
    }
}

/**
 * Конкретные Компоненты реализуют различную функциональность. Они не зависят от
 * других компонентов. Они также не зависят от каких-либо конкретных классов
 * посредников.
 */
class Component1 extends BaseComponent {
    public doA(): void {
        console.log('Component 1 does A.');
        this.mediator.notify(this, 'A');
    }

    public doB(): void {
        console.log('Component 1 does B.');
        this.mediator.notify(this, 'B');
    }
}

class Component2 extends BaseComponent {
    public doC(): void {
        console.log('Component 2 does C.');
        this.mediator.notify(this, 'C');
    }

    public doD(): void {
        console.log('Component 2 does D.');
        this.mediator.notify(this, 'D');
    }
}

/**
 * Клиентский код.
 */
const c1 = new Component1();
const c2 = new Component2();
const mediator = new ConcreteMediator(c1, c2);

console.log('Client triggers operation A.');
c1.doA();

console.log('');
console.log('Client triggers operation D.');
c2.doD();
```

</details>

### <a name="iterator"></a> Итератор

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/iterator)

**Назначение**: даёт возможность последовательно обходить элементы составных объектов, не раскрывая их внутреннего представления.

Коллекции — самая распространённая структура данных, которую вы можете встретить в программировании. Это набор объектов, собранный в одну кучу по каким-то критериям.

Но как бы ни была структурирована коллекция, пользователь должен иметь возможность последовательно обходить её элементы, чтобы проделывать с ними какие-то действия.

Но каким способом следует перемещаться по сложной структуре данных? Например, сегодня может быть достаточным обход дерева в глубину, но завтра потребуется возможность перемещаться по дереву в ширину.

Идея паттерна Итератор состоит в том, чтобы вынести поведение обхода коллекции из самой коллекции в отдельный класс.

Объект-итератор будет отслеживать состояние обхода, текущую позицию в коллекции и сколько элементов ещё осталось обойти. Одну и ту же коллекцию смогут одновременно обходить различные итераторы, а сама коллекция не будет даже знать об этом.

К тому же, если вам понадобится добавить новый способ обхода, вы сможете создать отдельный класс итератора, не изменяя существующий код коллекции.

**Шаги реализации**

1. Создайте общий интерфейс итераторов. Обязательный минимум — это операция получения следующего элемента коллекции. Но для удобства можно предусмотреть и другое. Например, методы для получения предыдущего элемента, текущей позиции, проверки окончания обхода и прочие.

2. Создайте интерфейс коллекции и опишите в нём метод получения итератора. Важно, чтобы сигнатура метода возвращала общий интерфейс итераторов, а не один из конкретных итераторов.

3. Создайте классы конкретных итераторов для тех коллекций, которые нужно обходить с помощью паттерна. Итератор должен быть привязан только к одному объекту коллекции. Обычно эта связь устанавливается через конструктор.

4. Реализуйте методы получения итератора в конкретных классах коллекций. Они должны создавать новый итератор того класса, который способен работать с данным типом коллекции. Коллекция должна передавать ссылку на собственный объект в конструктор итератора.

5. В клиентском коде и в классах коллекций не должно остаться кода обхода элементов. Клиент должен получать новый итератор из объекта коллекции каждый раз, когда ему нужно перебрать её элементы.

<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Паттерн Итератор
 *
 * Назначение: Даёт возможность последовательно обходить элементы составных
 * объектов, не раскрывая их внутреннего представления.
 */

interface Iterator<T> {
    // Возврат текущего элемента.
    current(): T;

    // Возврат текущего элемента и переход к следующему элементу.
    next(): T;

    // Возврат ключа текущего элемента.
    key(): number;

    // Проверяет корректность текущей позиции.
    valid(): boolean;

    // Перемотка Итератора к первому элементу.
    rewind(): void;
}

interface Aggregator {
    // Получить внешний итератор.
    getIterator(): Iterator<string>;
}

/**
 * Конкретные Итераторы реализуют различные алгоритмы обхода. Эти классы
 * постоянно хранят текущее положение обхода.
 */

class AlphabeticalOrderIterator implements Iterator<string> {
    private collection: WordsCollection;

    /**
     * Хранит текущее положение обхода. У итератора может быть множество других
     * полей для хранения состояния итерации, особенно когда он должен работать
     * с определённым типом коллекции.
     */
    private position: number = 0;

    /**
     * Эта переменная указывает направление обхода.
     */
    private reverse: boolean = false;

    constructor(collection: WordsCollection, reverse: boolean = false) {
        this.collection = collection;
        this.reverse = reverse;

        if (reverse) {
            this.position = collection.getCount() - 1;
        }
    }

    public rewind() {
        this.position = this.reverse ?
            this.collection.getCount() - 1 :
            0;
    }

    public current(): string {
        return this.collection.getItems()[this.position];
    }

    public key(): number {
        return this.position;
    }

    public next(): string {
        const item = this.collection.getItems()[this.position];
        this.position += this.reverse ? -1 : 1;
        return item;
    }

    public valid(): boolean {
        if (this.reverse) {
            return this.position >= 0;
        }

        return this.position < this.collection.getCount();
    }
}

/**
 * Конкретные Коллекции предоставляют один или несколько методов для получения
 * новых экземпляров итератора, совместимых с классом коллекции.
 */
class WordsCollection implements Aggregator {
    private items: string[] = [];

    public getItems(): string[] {
        return this.items;
    }

    public getCount(): number {
        return this.items.length;
    }

    public addItem(item: string): void {
        this.items.push(item);
    }

    public getIterator(): Iterator<string> {
        return new AlphabeticalOrderIterator(this);
    }

    public getReverseIterator(): Iterator<string> {
        return new AlphabeticalOrderIterator(this, true);
    }
}

/**
 * Клиентский код может знать или не знать о Конкретном Итераторе или классах
 * Коллекций, в зависимости от уровня косвенности, который вы хотите сохранить в
 * своей программе.
 */
const collection = new WordsCollection();
collection.addItem('First');
collection.addItem('Second');
collection.addItem('Third');

const iterator = collection.getIterator();

console.log('Straight traversal:');
while (iterator.valid()) {
    console.log(iterator.next());
}

console.log('');
console.log('Reverse traversal:');
const reverseIterator = collection.getReverseIterator();
while (reverseIterator.valid()) {
    console.log(reverseIterator.next());
}
```

</details>

### <a name="visitor"></a> Посетитель

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/visitor)

**Назначение**: позволяет добавлять в программу новые операции, не изменяя классы объектов, над которыми эти операции могут выполняться.

Ваша задача — сделать экспорт графа в XML. Дело было бы плёвым, если бы вы могли редактировать классы узлов. Достаточно было бы добавить метод экспорта в каждый тип узла, а затем, перебирая узлы графа, вызывать этот метод для каждого узла. Благодаря полиморфизму, решение получилось бы изящным, так как вам не пришлось бы привязываться к конкретным классам узлов.

Но, к сожалению, классы узлов вам изменить не удалось. Системный архитектор сослался на то, что код классов узлов сейчас очень стабилен, и от него многое зависит, поэтому он не хочет рисковать и позволять кому-либо его трогать.

К тому же он сомневался в том, что экспорт в XML вообще уместен в рамках этих классов. Их основная задача была связана с геоданными, а экспорт выглядит в рамках этих классов чужеродно.

Была и ещё одна причина запрета. Если на следующей неделе вам бы понадобился экспорт в какой-то другой формат данных, то эти классы снова пришлось бы менять.

Паттерн Посетитель предлагает разместить новое поведение в отдельном классе, вместо того чтобы множить его сразу в нескольких классах. Объекты, с которыми должно было быть связано поведение, не будут выполнять его самостоятельно. Вместо этого вы будете передавать эти объекты в методы посетителя.

Код поведения, скорее всего, должен отличаться для объектов разных классов, поэтому и методов у посетителя должно быть несколько. Названия и принцип действия этих методов будет схож, но основное отличие будет в типе принимаемого в параметрах объекта, например:

```javascript
class ExportVisitor implements Visitor is
    method doForCity(City c) { ... }
    method doForIndustry(Industry f) { ... }
    method doForSightSeeing(SightSeeing ss) { ... }
    // ...
```

Здесь возникает вопрос: как подавать узлы в объект-посетитель? Так как все методы имеют отличающуюся сигнатуру, использовать полиморфизм при переборе узлов не получится. Придётся проверять тип узлов для того, чтобы выбрать соответствующий метод посетителя.

```javascript
foreach (Node node in graph)
    if (node instanceof City)
        exportVisitor.doForCity((City) node)
    if (node instanceof Industry)
        exportVisitor.doForIndustry((Industry) node)
    // ...
```

Но паттерн Посетитель решает и эту проблему, используя механизм двойной диспетчеризации. Вместо того, чтобы самим искать нужный метод, мы можем поручить это объектам, которые передаём в параметрах посетителю. А они уже вызовут правильный метод посетителя.

```javascript
// Client code
foreach (Node node in graph)
node.accept(exportVisitor)

// City
class City is
method accept(Visitor v) is
v.doForCity(this)
// ...

// Industry
class Industry is
method accept(Visitor v) is
v.doForIndustry(this)
// ...
```

Как видите, изменить классы узлов всё-таки придётся. Но это простое изменение позволит применять к объектам узлов и другие поведения, ведь классы узлов будут привязаны не к конкретному классу посетителей, а к их общему интерфейсу. Поэтому если придётся добавить в программу новое поведение, вы создадите новый класс посетителей и будете передавать его в методы узлов.

Интерфейс посетителей должен содержать методы посещения каждого элемента.

**Важно, чтобы иерархия элементов менялась редко, так как при добавлении нового элемента придётся менять всех существующих посетителей**.

**Шаги реализации**

1. Создайте интерфейс посетителя и объявите в нём методы «посещения» для каждого класса элемента, который существует в программе.

2. Опишите интерфейс элементов. Если вы работаете с уже существующими классами, то объявите абстрактный метод принятия посетителей в базовом классе иерархии элементов.

3. Реализуйте методы принятия во всех конкретных элементах. Они должны переадресовывать вызовы тому методу посетителя, в котором тип параметра совпадает с текущим классом элемента.

4. Иерархия элементов должна знать только о базовом интерфейсе посетителей. С другой стороны, посетители будут знать обо всех классах элементов.

5. Для каждого нового поведения создайте конкретный класс посетителя. Приспособьте это поведение для работы со всеми типами элементов, реализовав все методы интерфейса посетителей. Вы можете столкнуться с ситуацией, когда посетителю нужен будет доступ к приватным полям элементов. В этом случае вы можете либо раскрыть доступ к этим полям, нарушив инкапсуляцию элементов, либо сделать класс посетителя вложенным в класс элемента, если вам повезло писать на языке, который поддерживает вложенность классов.

6. Клиент будет создавать объекты посетителей, а затем передавать их элементам, используя метод принятия.
<details>
<summary>Пример паттерна на TypeScript</summary>

```javascript
/**
 * Интерфейс Компонента объявляет метод accept, который в качестве аргумента
 * может получать любой объект, реализующий интерфейс посетителя.
 */
interface Component {
    accept(visitor: Visitor): void;
}

/**
 * Каждый Конкретный Компонент должен реализовать метод accept таким образом,
 * чтобы он вызывал метод посетителя, соответствующий классу компонента.
 */
class ConcreteComponentA implements Component {
    /**
     * Обратите внимание, мы вызываем visitConcreteComponentA, что соответствует
     * названию текущего класса. Таким образом мы позволяем посетителю узнать, с
     * каким классом компонента он работает.
     */
    public accept(visitor: Visitor): void {
        visitor.visitConcreteComponentA(this);
    }

    /**
     * Конкретные Компоненты могут иметь особые методы, не объявленные в их
     * базовом классе или интерфейсе. Посетитель всё же может использовать эти
     * методы, поскольку он знает о конкретном классе компонента.
     */
    public exclusiveMethodOfConcreteComponentA(): string {
        return 'A';
    }
}

class ConcreteComponentB implements Component {
    /**
     * То же самое здесь: visitConcreteComponentB => ConcreteComponentB
     */
    public accept(visitor: Visitor): void {
        visitor.visitConcreteComponentB(this);
    }

    public specialMethodOfConcreteComponentB(): string {
        return 'B';
    }
}

/**
 * Интерфейс Посетителя объявляет набор методов посещения, соответствующих
 * классам компонентов. Сигнатура метода посещения позволяет посетителю
 * определить конкретный класс компонента, с которым он имеет дело.
 */
interface Visitor {
    visitConcreteComponentA(element: ConcreteComponentA): void;

    visitConcreteComponentB(element: ConcreteComponentB): void;
}

/**
 * Конкретные Посетители реализуют несколько версий одного и того же алгоритма,
 * которые могут работать со всеми классами конкретных компонентов.
 *
 * Максимальную выгоду от паттерна Посетитель вы почувствуете, используя его со
 * сложной структурой объектов, такой как дерево Компоновщика. В этом случае
 * было бы полезно хранить некоторое промежуточное состояние алгоритма при
 * выполнении методов посетителя над различными объектами структуры.
 */
class ConcreteVisitor1 implements Visitor {
    public visitConcreteComponentA(element: ConcreteComponentA): void {
        console.log(`${element.exclusiveMethodOfConcreteComponentA()} + ConcreteVisitor1`);
    }

    public visitConcreteComponentB(element: ConcreteComponentB): void {
        console.log(`${element.specialMethodOfConcreteComponentB()} + ConcreteVisitor1`);
    }
}

class ConcreteVisitor2 implements Visitor {
    public visitConcreteComponentA(element: ConcreteComponentA): void {
        console.log(`${element.exclusiveMethodOfConcreteComponentA()} + ConcreteVisitor2`);
    }

    public visitConcreteComponentB(element: ConcreteComponentB): void {
        console.log(`${element.specialMethodOfConcreteComponentB()} + ConcreteVisitor2`);
    }
}

/**
 * Клиентский код может выполнять операции посетителя над любым набором
 * элементов, не выясняя их конкретных классов. Операция принятия направляет
 * вызов к соответствующей операции в объекте посетителя.
 */
function clientCode(components: Component[], visitor: Visitor) {
    // ...
    for (const component of components) {
        component.accept(visitor);
    }
    // ...
}

const components = [
    new ConcreteComponentA(),
    new ConcreteComponentB(),
];

console.log('The client code works with all visitors via the base Visitor interface:');
const visitor1 = new ConcreteVisitor1();
clientCode(components, visitor1);
console.log('');

console.log('It allows the same client code to work with different types of visitors:');
const visitor2 = new ConcreteVisitor2();
clientCode(components, visitor2);
```

</details>
