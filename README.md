### Стратегия

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/strategy)

**Назначение**: определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Стратегия позволяет изменять алгоритмы независимо от клиентов, которые ими пользуются.

**Другими словами**: стратегия инкапсулирует определенное поведение с возможностью его подмены.

Паттерн Стратегия предлагает определить семейство схожих алгоритмов, которые часто изменяются или расширяются, и вынести их в собственные классы, называемые стратегиями.

Вместо того, чтобы изначальный класс сам выполнял тот или иной алгоритм, он будет играть роль контекста, ссылаясь на одну из стратегий и делегируя ей выполнение работы. Чтобы сменить алгоритм, вам будет достаточно подставить в контекст другой объект-стратегию.

Важно, чтобы все стратегии имели общий интерфейс. Используя этот интерфейс, контекст будет независимым от конкретных классов стратегий. С другой стороны, вы сможете изменять и добавлять новые виды алгоритмов, не трогая код контекста.


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

### Шаблонный метод

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


### Наблюдатель

[ссылка на паттерн](https://refactoring.guru/ru/design-patterns/observer)

**Назначение**: определяет зависимость типа «один ко многим» между объектами
таким образом, что при изменении состояния одного объекта все зависящие от него
оповещаются об этом и автоматически обновляются.

**Другими словами**: наблюдатель уведомляет все заинтересованные стороны о произошедшем событии или об изменении своего состояния.

Паттерн Наблюдатель предлагает хранить внутри объекта издателя список ссылок на объекты подписчиков, причём издатель не должен вести список подписки самостоятельно. Он предоставит методы, с помощью которых подписчики могли бы добавлять или убирать себя из списка.

Когда в издателе будет происходить важное событие, он будет проходиться по списку подписчиков и оповещать их об этом, вызывая определённый метод объектов-подписчиков.

Издателю безразлично, какой класс будет иметь тот или иной подписчик, так как все они должны следовать общему интерфейсу и иметь единый метод оповещения.

Увидев, как складно всё работает, вы можете выделить общий интерфейс, описывающий методы подписки и отписки, и для всех издателей. После этого подписчики смогут работать с разными типами издателей, а также получать оповещения от них через один и тот же метод.


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