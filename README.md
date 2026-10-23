# Patterns

Примеры паттернов проектирования на C# (.NET 6).

## Структура решения

```
Patterns.sln
├── ClassLibraryPatterns/   — библиотека классов с реализациями паттернов
│   ├── Pen.cs                  Стратегия
│   ├── IceCream.cs             Декоратор (стандартная реализация)
│   ├── IceCreamModified.cs     Декоратор (модернизированная реализация)
│   └── VideoCreator.cs         Наблюдатель
└── ConsolePatterns/        — консольное приложение с демонстрацией
    └── Program.cs              каждый паттерн в отдельном #region
```

## Реализованные паттерны

| Паттерн | Файл | Предметная область | Ключевые классы |
|---|---|---|---|
| Стратегия | [`Pen.cs`](ClassLibraryPatterns/Pen.cs) | Ручки | `Pen`, `IWriteBehavior` |
| Декоратор (стандартный) | [`IceCream.cs`](ClassLibraryPatterns/IceCream.cs) | Мороженое с добавками | `IceCream`, `CondimentDecorator` |
| Декоратор (модернизированный) | [`IceCreamModified.cs`](ClassLibraryPatterns/IceCreamModified.cs) | Мороженое с добавками | `DecorIceCream` |
| Наблюдатель | [`VideoCreator.cs`](ClassLibraryPatterns/VideoCreator.cs) | Автор видео и зрители | `IObservable`, `IObserver`, `VideoCreator`, `Viewer` |

### Стратегия

Поведение «писать» вынесено из иерархии ручек в отдельные классы, реализующие `IWriteBehavior`
(`WriteWithBallPoint`, `WriteWithGel`, `WriteWithFountain`, `WriteWithСapillary`).
Каждая ручка (`BallPointPen`, `GelPen`, `FountainPen`, `CapillaryPen`) делегирует письмо своему объекту поведения,
а метод `SetIWriteBehavior` позволяет заменить поведение во время выполнения.

```csharp
BallPointPen bpp = new BallPointPen();
Console.WriteLine(bpp.Display());
Console.WriteLine(bpp.PerformWrite());
```

### Декоратор (стандартная реализация)

Базовое мороженое (`Plombier`, `Sundae`, `MilkIceCream`) оборачивается декораторами-добавками
(`ChocolateChips`, `Nuts`, `RaspberrySyrup`), унаследованными от `CondimentDecorator`.
Каждый декоратор хранит вложенный объект и дополняет его описание и стоимость (`Cost()`).

```csharp
IceCream sundae = new Sundae();
sundae = new RaspberrySyrup(sundae);
sundae = new Nuts(sundae);
Console.WriteLine(sundae.ToString());
```

### Декоратор (модернизированная реализация)

Вместо цепочки обёрток используется один класс `DecorIceCream`: метод `AddDecor()` возвращает
новый заказ, в котором описание и цена складываются из текущего заказа и добавки.
Мороженое (`DecorPlombier`, `DecorSundae`, `DecorMilkIceCream`) и добавки
(`DecorChocolateChips`, `DecorNuts`, `DecorRaspberrySyrup`) задаются как наследники с параметрами по умолчанию.

```csharp
DecorIceCream order = new DecorMilkIceCream();
order = order.AddDecor(new DecorNuts());
order = order.AddDecor(new DecorRaspberrySyrup());
Console.WriteLine(order.ToString());
```

### Наблюдатель

`VideoCreator` (наблюдаемый объект, `IObservable`) хранит список подписчиков и при выпуске нового видео
(`MakeVideo()`) уведомляет их через `NotifyObservers()`. Зритель `Viewer` (`IObserver`) подписывается в конструкторе,
реагирует на уведомление в `Update()` и может отписаться методом `StopView()`.

```csharp
VideoCreator vc = new VideoCreator();
Viewer v1 = new Viewer("Vanya128", vc);
Viewer v2 = new Viewer("Alex32", vc);
vc.MakeVideo();
v1.StopView();
v2.StopView();
```

## Требования

- [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0) или более новый SDK с установленным .NET 6 Runtime
- Visual Studio 2022 или JetBrains Rider (необязательно)

## Запуск

```
dotnet build Patterns.sln
dotnet run --project ConsolePatterns
```

Вывод паттерна «Наблюдатель» каждый раз разный: название видео и реакция зрителей
выбираются случайно с помощью `Random`.
