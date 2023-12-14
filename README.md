**Использование Unleash во фронтенд-приложениях**

Для интеграции функциональности Unleash в фронтенд-приложение без использования собственного бэкенд-сервера, необходимо использовать как минимум одну библиотеку. Если вы работаете с чистым JavaScript, достаточно `unleash-proxy-client`. Для работы с React, Vue или Angular понадобится дополнительная библиотека.


**Использование Unleash в React-приложениях**

Для интеграции функциональности Unleash в React-приложение установите библиотеку `@unleash/proxy-client-react`.

1. **Установка зависимостей**

   Установите из Nexus `@unleash/proxy-client-react` и `unleash-proxy-client`:

   ```bash
   npm install @unleash/proxy-client-react unleash-proxy-client
   # или
   yarn add @unleash/proxy-client-react unleash-proxy-client
   ```

2. **Инициализация настроек и подключение к приложению**

   Импортируйте `FlagProvider` и инициализируйте конфигурацию Unleash:

   ```jsx
   import { createRoot } from 'react-dom/client';
   import { FlagProvider } from '@unleash/proxy-client-react';

   const config = {
     url: 'https://HOSTNAME/proxy',
     clientKey: 'PROXYKEY',
     refreshInterval: 15,
     appName: 'your-app-name',
   };

   const root = createRoot(document.getElementById('root'));

   root.render(
     <React.StrictMode>
       <FlagProvider config={config}>
         <App />
       </FlagProvider>
     </React.StrictMode>
   );
   ```

   В качестве альтернативы, вы можете передать свой собственный клиент в `FlagProvider`:

   ```jsx
   import { createRoot } from 'react-dom/client';
   import { FlagProvider, UnleashClient } from '@unleash/proxy-client-react';

   const client = new UnleashClient(config);
   const root = createRoot(document.getElementById('root'));

   root.render(
     <React.StrictMode>
       <FlagProvider unleashClient={client}>
         <App />
       </FlagProvider>
     </React.StrictMode>
   );
   ```


3. **Использование**

   - **Проверка статуса фича-тогла**

     ```jsx
     import { useFlag } from '@unleash/proxy-client-react';

     const TestComponent = () => {
       const enabled = useFlag('travel.landing');

       if (enabled) {
         return <SomeComponent />;
       }
       return <AnotherComponent />;
     };

     export default TestComponent;
     ```


   - **Отложенный рендеринг до получения флагов**

     ```jsx
     import { useFlagsStatus } from '@unleash/proxy-client-react'

     const MyApp = () => {
       const { flagsReady, flagsError } = useFlagsStatus();

       if (!flagsReady) {
         return <Loading />;
       }
       return <MyComponent error={flagsError}/>;
     }
     ```

==========================================

**Использование Unleash в проектах на чистом JavaScript**

Для работы с Unleash в проектах на чистом

 JavaScript используйте библиотеку `unleash-proxy-client`.

1. **Установка зависимостей**

   Установите `unleash-proxy-client`:

   ```bash
   npm install unleash-proxy-client
   # или
   yarn add unleash-proxy-client
   ```

2. **Добавление кода и инициализация SDK**

   Импортируйте `UnleashClient` и инициализируйте клиент:

   ```javascript
   import { UnleashClient } from 'unleash-proxy-client';

   const unleash = new UnleashClient({
       url: 'https://HOSTNAME/proxy',
       clientKey: 'proxy-key',
       appName: 'my-webapp'
   });

   unleash.start();
   ```

3. **Ожидание готовности клиента**

   Слушайте событие 'ready' для работы с фича-тоглами:

   ```javascript
   unleash.on('ready', () => {
     if (unleash.isEnabled('proxy.demo')) {
       console.log('proxy.demo is enabled');
     } else {
       console.log('proxy.demo is disabled');
     }
   });
   ```

4. **Работа c фича-тоглами**

   Работайте с вариантами фича-тоглов:

   ```javascript

   // Запуск начального запроса и фонового опроса
   unleash.start();

   // Получение варианта фича-тогла
   const variant = unleash.getVariant('proxy.demo');
   if (variant.name === 'blue') {
     // Работа с вариантом 'blue'...
   }
   ```

Эти инструкции помогут вам успешно интегрировать Unleash в ваше фронтенд-приложение на React или в проект на чистом JavaScript.

Библиотеки для Vue/Angular на данный момент не тестировались, так как данные фреймворки используются реже.




=================

### Общая Инструкция по Реализации Бэкапа своими руками

#### Шаг 1: Определение Стратегии Бэкапа

1. **Выбор Хранилища Бэкапа**:
   Определите, какое хранилище использовать для бэкапа (например, файловая система, база данных, in-memory хранилище и т.д.). Мы предлагаем сделать бэкап с помощью файловой системы.

2. **Формат Бэкапа**:
   Решите, в каком формате будут сохраняться данные (JSON, XML, сериализованные объекты и т.д.). Далее пример будет с JSON, как это реализовано уже в стандартной библиотеке.

#### Шаг 2: Интеграция Системы Бэкапа

1. **Сохранение Данных**:
   После каждого успешного запроса к серверу Unleash, сохраняйте полученные данные о состоянии feature toggles в выбранное хранилище.

2. **Обработка Событий Unleash**:
   Используйте доступные в вашем SDK для Unleash события или хуки для перехвата данных.

#### Шаг 3: Восстановление из Бэкапа

1. **Проверка Доступности Unleash**:
   Регулярно проверяйте, доступен ли сервер Unleash.

2. **Восстановление Данных**:
   Если сервер Unleash недоступен, используйте последние сохраненные данные из бэкапа для управления feature toggles.

#### Шаг 4: Интеграция Восстановления в Приложение

1. **Логика Фолбэка**:
   Внедрите логику, которая определяет, когда использовать данные из бэкапа, и когда - данные с сервера Unleash.


### Пример на Java

#### Шаг 1: Сохранение Данных

```java

// Предположим, что unleash уже инициализирован
Unleash unleash = ...

unleash.subscribe(new UnleashSubscriber() {
    @Override
    public void on(UnleashEvent event) {
        // Сохраняем данные в файл при каждом изменении состояния feature toggles
        try (FileWriter writer = new FileWriter("backup.json")) {
            writer.write(event.toString()); // Используйте соответствующий формат сериализации
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
});
```

#### Шаг 2: Восстановление из Бэкапа
Если сервер unleash недоступен, то читаем данные из локального бэкапа.

```java

public class BackupManager {

    public static String loadBackup() {
        try {
            return new String(Files.readAllBytes(Paths.get("backup.json"))); // можно поместить в /tmp/backup.json и из докера прокинуть файл на сервер, чтобы он сохранился на случай перезагрузки докера.
        } catch (IOException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

Этот пример показывает базовую реализацию системы бэкапа на Java. Вы можете адаптировать эту концепцию под любой другой язык программирования, следуя общим принципам изложенной инструкции.
