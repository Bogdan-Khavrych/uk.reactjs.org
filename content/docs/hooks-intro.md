---
id: hooks-intro
title: Ознайомлення з Хуками
permalink: docs/hooks-intro.html
next: hooks-overview.html
---

*Хуки* — це новинка в React 16.8. Вони дозволяють вам використовувати стан та інші можливості React без написання класу.

```js{4,5}
import React, { useState } from 'react';

function Example() {
  // Створюємо нову змінну стану, яку назвемо "count"
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Ви натиснули {count} разів</p>
      <button onClick={() => setCount(count + 1)}>
        Натисни мене
      </button>
    </div>
  );
}
```

Нова функція `useState` є першим "хуком", про який ми дізнаємося більше. Цей приклад є лише тизером. Не хвилюйтеся, якщо вам поки що нічого не зрозуміло!

**Почати вивчення хуків можна [на наступній сторінці](/docs/hooks-overview.html).** На цій ми пояснимо, чому додаємо хуки до React та як вони можуть допомогти у написанні чудових застосунків.

>Примітка
>
>React 16.8.0 — це перший реліз, який підтримує хуки. При оновленні не забудьте оновити всі бібліотеки, включаючи React DOM.
>React Native підтримує хуки з релізу версії [0.59](https://reactnative.dev/blog/2019/03/12/releasing-react-native-059).

## Відео ознайомлення {#video-introduction}

На React Conf 2018 Софі Алперт (Sophie Alpert) та Ден Абрамов (Dan Abramov) презентували хуки, а потім Райан Флоренс (Ryan Florence) показав як переписати застосунок, використовуючи хуки. Дивіться відео тут:

<br>

<iframe width="650" height="366" src="//www.youtube.com/embed/dpw9EHDh2bM" frameborder="0" allowfullscreen></iframe>

## Повна зворотна сумісність {#no-breaking-changes}

Перед тим, як ми продовжимо, зверніть увагу що хуки:

* **Необовʼязкові.** Можна спробувати хуки в декількох компонентах без переписування будь-якого існуючого коду. Немає нагальної потреби у вивченні чи використанні хуків, якщо вам цього не хочеться.
* **100% зворотна сумісність.** хуки нічого не ламають.
* **Доступні вже зараз.** хуки доступні у релізі 16.8.0.

**Ми не плануємо видаляти класи із React.** Почитати про поступову стратегію впровадження хуків можна у [нижній частині](#gradual-adoption-strategy) цієї сторінки.

**Хуки не заміняють ваші знання концепцій React.** Навпаки, вони надають більш прямий API до концепцій React, які ви вже знаєте: пропси, стан, контекст, рефи та життєвий цикл. Пізніше ми покажемо, як можна комбінувати ці концепції у хуках.

**Якщо вам лише кортить спробувати хуки, просимо [перейти на наступну сторінку!](/docs/hooks-overview.html)** Також ви можете залишитися, щоб дізнатися більше про те, чому ми додаємо хуки і як збираємося їх використовувати без переписування наших застосунків.

## Мотивація {#motivation}

Хуки вирішують великий діапазон скоріш за все непоєднаних проблем у React, на які ми натрапили при написанні та підтриманні десятків тисяч компонентів протягом пʼяти років. Неважливо, ви тільки вивчаєте React, чи використовуєте його щодня, чи навіть віддаєте перевагу іншій бібліотеці зі схожою системою компонентів, можливо, ви впізнаєте деякі з цих проблем.

### Важко перевикористати логіку станів між компонентами {#its-hard-to-reuse-stateful-logic-between-components}

React не має можливості "прикріпити" до компонента поведінку, яку можна перевикористати (наприклад підключення до сховища). Якщо ви вже доволі давно працюєте із React, то скоріше за все ви знайомі із такими петернами як [рендер-пропи](/docs/render-props.html) [та компоненти вищого порядку](/docs/higher-order-components.html), що мають на меті вирішити цю проблему. Але при використанні цих шаблонів проектування, розробник вимушений реструктурувати компоненти, через що код стає заплутаним та важко зрозумілим. Якщо поглянути у React Devtools на типовий застосунок написаний на React, з великою імовірністю ви побачите таку ситуацію, коли компоненти обгортаються у шари провайдерів, споживачів, компонентів вищого порядку, рендер-пропів та інших абстракцій, які ми лагідно назвемо "пекло з обгорток". Незважаючи на те, що їх можливо [відфільтрувати у Devtools](https://github.com/facebook/react-devtools/pull/503), стає зрозуміло, що необхідно створити кращий спосіб повторно використовувати логіку навколо стану.

За допомогою хуків ви можете виокремити логіку стану з компонента, щоб протестувати її або повторно використати. **З хуками зʼявляється можливість перевикористання логіки з відслідкуванням стану без зміни ієрархії ваших компонентів.** Через це стає легко ділитися хуками поміж іншими компонентами чи зі спільнотою.

Поговоримо про це більше у [написанні власних хуків](/docs/hooks-custom.html).

### Складні компоненти стає важко розуміти {#complex-components-become-hard-to-understand}

Нам часто доводилося підтримувати компоненти, що були простими на початку, але переросли у щось вкрай неконтрольоване, із купою побічних ефектів та логіки станів. Кожен життєвий цикл часто містить мікс не повʼязаної між собою логіки. Наприклад, компоненти можуть завантажувати дані у `componentDidMount` та `componentDidUpdate`. При цьому у методі `componentDidMount` може міститися якась не повʼязана логіка, що реєструє обробники подій та виконує очистку в `componentWillUnmount`. Взаємно повʼязаний код, що спільно змінюється опиняється розбитим на шматки, але зовсім не повʼязані між собою його частини — звʼязаними в одному методі. І це призводить до виникнення помилок та невідповідностей.

У більшості випадків просто неможливо розбити ці компоненти на менші, через обширну присутність логіки станів. Крім того, їх важко тестувати. Саме це і є однією із причин, чому багато людей віддають перевагу додаванню сторонніх бібліотек для керування станом. Однак часто це створює складну абстракцію, що призводить до ще важчого перевикористання компонентів та змушує вас перемикатися між різними файлами.

**З хуками можна розділити один компонент на менші функції, базуючись на їхньому спільному призначенні, наприклад, хай то буде встановлення підписки чи завантаження даних**. Це є відмінним від примусового розбиття з використанням методів життєвого циклу. Ви також можете використовувати редюсер для керування локальним станом компоненту, щоб зробити його більш передбачуваним.

Поговоримо про це більше у [використанні хуку Ефекту](/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns).

### Поняття класів заплутує як людей, так і компʼютери {#classes-confuse-both-people-and-machines}

Ми зрозуміли, що класи, окрім складного перевикористання та організації коду, утворюють ще й великий барʼєр до вивчення React. У JavaScript необхідно розуміти механізм роботи `this`, який кардинально відрізняється від інших мов програмування. Також, потрібно пам'ятати про прив'язку обробників подій. А без використання нестабільних [пропозицій до синтаксису](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/), код стає занадто багатослівним. Якщо людям прекрасно зрозуміло, що є пропси, стан та потік даних зверху вниз, то класи викликають проблеми. Відмінності між функціональними та класовими компонентами в React, а також запитання коли який варіант використовувати, навіть досвідчених розробників React приводять до суперечок.

Окрім цього, React-у вже пʼять років і ми хочемо бути певними, що він залишиться акутальним у наступні пʼять. Як [Svelte](https://svelte.dev/), [Angular](https://angular.io/), [Glimmer](https://glimmerjs.com/) та інші показують, [AOT-компіляція](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) компонентів має потенціал в майбутньому. Особливо якщо вона не обмежується шаблонами. Нещодавно ми експерементували із [згортанням компонентів](https://github.com/facebook/react/issues/7323) використовуючи [Prepack](https://prepack.io/) і навіть перші результати є перспективними. Однак, ми збагнули, що у класових компонентах можна ненавмисно утворити такі шаблони, що зведуть нанівець всі попередні оптимізації. Також класи створюють проблеми для сьогоденних інструментів. Наприклад, класи дуже погано мініфікуються, а механізм миттєвого оновлення коду стає дивним та ненадійним. Ми хочемо запропонувати API, із яким код і надалі можна оптимізувати.

**У хуках можна використовувати більшість можливостей React без написання класів**, для вирішення попередньо описаних проблем. Згідно нашої концепції, компоненти React завжди були ближчими до функцій. Використовуючи функції у хуках, ми не жертвуємо практичністю React. хуки забезпечують доступ до імперативних викликів без необхідності вивчeння складних технік функціонального чи реактивного програмування.

>Приклади
>
>[Огляд хуків](/docs/hooks-overview.html) є хорошою відправною точкою вивчення хуків.

## Стратегія поступового впровадження {#gradual-adoption-strategy}

>**TLDR: У нас немає планів зупиняти підтримку класів у React.**

Нам відомо, що розробники React фокусуються на виконанні цілей і не завжди щоразу мають час на перегляд випущеного нового API. Хуки - абсолютно новий концепт, а тому може бути краще почекати виходу більшої кількості прикладів та посібників перед прийняттям рішення про їхнє вивчення чи використання.

Ми також розуміємо, що поріг додавання нових концепцій у React є досить високим. Тому для зацікавлених ми підготували [детальний запит про обговорення](https://github.com/reactjs/rfcs/pull/68), в якому знайдете більше деталей та пояснення нашої мотивації, а також додаткову інформацію щодо технічних аспектів та основи дизайну хуків.

**Важливим є те, що хуки працюють разом із існуючим кодом, тому їх можна впроваджувати поступово.** Немає необхідності прямо зараз мігрувати на хуки. Ми рекомендуємо уникати великого переписання коду, особливо для існуючих складних класових компонентів. Необхідно докласти трішки зусиль, щоб почати "думати хуками". Ми виявили, що найкраще спочатку почати використовувати хуки в нових і не критичних компонентах, а також впевнитися, що всі в команді комфортно їх можуть використовувати. Після того, як ви спробуєте хуки, не соромтеся [надсилати нам](https://github.com/facebook/react/issues/new) відгуки, неважливо хороші чи погані.

Ми мали намір покрити хуками всі існуючі можливі варіанти використання класів, але **ми і надалі будемо підтримувати класові компоненти у найближчому майбутньому.** У Facebook, ми написали десятки тисяч компонентів класами і не прагнемо їх переписувати. Натомість, у новому коді разом із класами ми починаємо використовувати хуки.

## Часті питання {#frequently-asked-questions}

Знайти відповіді на найпоширеніші запитання про хуки можна на [сторінці FAQ](/docs/hooks-faq.html).

## Наступні кроки {#next-steps}

Дійшовши кінця, ви вже повинні мати приблизне бачення проблем, які хуки вирішують, хоча багато подробиць ще залишаються незрозумілими. Не турбуйтеся! **Давайте перейдемо [до наступної сторінки](/docs/hooks-overview.html), де почнемо вивчати хуки на прикладах.**
