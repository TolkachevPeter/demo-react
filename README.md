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
