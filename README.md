# CheatBase

## Авторы

**sheezzmee**, **sabaka_babaka**

Дискорд сервер: https://discord.gg/gMAJX2AdBS - здесь будут размещаться готовые скрипты

## Основные функции

1. **Изменение прототипа классов**: полный контроль над игровыми классами
   
    Для полного контроля над игровыми классами в CheatBase предоставляется функция `prototypeHook`. Эта функция позволяет вам подменить или расширить функции классов.
    
    ### Сигнатура функции:
    
    ```javascript
    cheatBase.utils.prototypeHook(constructor, selector, callback, after, args);
    ```
    
    #### Параметры:
    
    - `constructor`: class - конструктор класса.
    - `selector`: string - селектор для поиска нужной функции, например `i:0`, где `i:0` говорит, что нужно искать функцию под индексом 0. `constructor.prototype.entries` позволяет получить индексы всех функций этого класса.
    - `callback`: function - функция, которая будет вызываться перед или после вызова оригинальной функции.
    - `after`: boolean [default false] - определяет очередность вызова callback функции.
      - `true`: каллбек функция будет вызвана после выполнения оригинальной функции. Последним аргументом будет передан return оригинальной функции.
      - `false`: каллбек функция будет вызвана перед выполнением оригинальной функции. Если вернуть любое значение, то прервется выполнение и оригинальная функция не будет вызвана.
        - Если вернуть undefined ```return;```, то прервется выполнение callback функции, но оригинальная функция все равно будет вызвана.
        - Если вернуть 'skip', то caller получит undefined, и оригинальная функция не будет вызвана.
    - `args`: boolean [default false] - включает arguments в виде отдельного аргумента, это нужно для подмены аргументов.
    
    ### Пример использования:
    
    ```javascript
    // Пример подмены функции в классе CameraComponent
    class CameraComponent {
      getCameraParams(params) {
        params.fov = fov;
        params.position.init(position);
        params.rotation.init(rotation);
      }
    }
    
    // Использование prototypeHook для изменения поведения функции
    cheatBase.utils.prototypeHook(CameraComponent, 'i:1', params => {
      params[fov] = 1.3;
    }, true);
    ```
    
    В данном примере происходит подмена функции `getCameraParams` в классе `CameraComponent`. При вызове этой функции происходит выполнение новой функции, указанной в `prototypeHook`, что позволяет изменить или дополнить поведение класса в игре.

2. **Эвенты**: добавление слушателей для получения игровых сообщений и отправка своих сообщений

    CheatBase предоставляет механизм для обработки игровых событий (эвентов) и отправки своих сообщений. Вы можете добавлять слушателей для получения сообщений от игры и отправлять свои собственные сообщения.
    
    ### Включение отображения сообщений в консоли:
    
    ```javascript
    cheatBase.dispatchLog = true;
    ```
    
    ### Примеры слушателей для получения игровых сообщений:
    
    1. Слушатель на событие "OnBattleCanvasReady":
    
       ```javascript
       addEventListener('OnBattleCanvasReady', event => {
         const action = event.action; // параметры сообщения
         // Обработка события...
       });
       ```
    
    2. Слушатель на событие "onPlayerCreated":
    
       ```javascript
       addEventListener('onPlayerCreated', player => {
         // Обработка события...
       });
       ```
    
    3. Слушатель на обновление позиции:
    
       ```javascript
       player.on(FullStateCorrectionMessage, message => {
         // Обработка события...
       });
       ```
    
    ### Примеры отправки своих сообщений:
    
    1. Обновление позиции игрока:
    
       ```javascript
       player.send(new FullStateCorrectionMessage);
       ```
    
    2. Wallhack:
    
       ```javascript
       for (const player of cheatBase.gameClasses.players) {
         if (!player.isEnemy)
           continue;
    
         player.send(new ShowHighlight(14629098, false));
       }
       ```
    
    3. Покупка 1000000 мин:
    
       ```javascript
       cheatBase.dispatch(new PurchaseByCrystal(cheatBase.utils.toLong('10007277'), 1000000, new PurchaseSourceCategory('', 0)));
       ```
    
    Эти примеры демонстрируют, как можно эффективно взаимодействовать с игровыми событиями и сообщениями в CheatBase, открывая новые возможности для управления игровым процессом.

## Структура проекта

```js
CheatBase/
|-- store: Store
|-- dispatchLog: boolean
|-- dispatch: function(action: any)
|-- utils/
|   |-- toLong: function(numberStr: string): Long
|   |-- getSimpleName: function(object: object): string
|   |-- getNamedClasses: function(object: object): object
|   |-- getRandomArbitrary: function(min: number, max: number): number
|   |-- getPropertyByIndex: function(object: object, index: number)
|   |-- getPropertyByName: function(object: object, simpleName: string, returnName: boolean = false)
|   |-- find: function(object: object, selector: string): [string, any]
|   |-- prototypeHook: function(constructor: class, selector: string, callBack: function, after: boolean = false, args: boolean = false)
|   |-- proxyHook: function(constructor: class, selector: string, callBack: function)
|   |-- isKeyPressed: function(key: string): boolean
|   |-- pressKey: function(code: string)
|-- features/
|   |-- notifications/
|       |-- alert: function(textLines: string[], buttonYesListener: function | null = null, buttonCloseListener: function | null = null, timeInMs: number = 10000, title: string | null = null)
|       |-- warning: function(textLines: string[], buttonYesListener: function | null = null, buttonCloseListener: function | null = null, timeInMs: number = 10000, title: string | null = null)
|       |-- info: function(textLines: string[], buttonYesListener: function | null = null, buttonCloseListener: function | null = null, timeInMs: number = 10000, title: string | null = null)
|   |-- textMessages/
|       |-- white: function(text: string, lifeTimeMs: number = 1000)
|       |-- orange: function(text: string, lifeTimeMs: number = 1000)
|       |-- red: function(text: string, lifeTimeMs: number = 1000)
|       |-- blue: function(text: string, lifeTimeMs: number = 1000)
|   |-- battle/
|       |-- activateBattle: function(id: string)
|       |-- activateBattleAtLink: function(link: string)
|       |-- exitFromBattle: function()
|       |-- fight: function(id: string, battleTeam: 'TEAM_A' | 'TEAM_B' | 'NONE')
|   |-- containers/
|       |-- openContainer: function(id: string, count = number)
|   |-- battleChat/
|       |-- isInputActive: function(): boolean
|       |-- sendMessage: function(message: string, teamMessage: boolean = false)
|       |-- spectatorMessage: function(message: string)
|       |-- systemMessage: function(message: string)
|       |-- developerMessage: function(message: string)
|   |-- supplies/
|       |-- activateSupplyByName: function(supply: string)
|       |-- getSupplies: function(): {string:function}
|   |-- battleMap/
|       |-- isSpace: function(): boolean
|       |-- getKillZone: function(): AABB
|-- gameClasses/
|   |-- world: World | null
|   |-- localPlayer: Player | null
|   |-- gameMode: BattleEntity | null
|   |-- inventoryModel: InventoryModel | null
|   |-- game: BattleEntity | null
|   |-- hud: BattleEntity | null
|   |-- players: Player[] | null
|-- runAfterPhysicsUpdate: function[]
|-- forbiddenMessages: class[]

Player/
|-- entity: BattleEntity | null
|-- local: boolean
|-- body: Body | null
|-- state: BodyState | null
|-- map: Map | null
|-- id: Long
|-- team: BattleTeam
|-- name: string | null
|-- get isEnemy: boolean
|-- get tankState: string
|-- sendState: function(state: TankState)
|-- send: function(message: class)
|-- on: function(messageClass: class, handler: function, priority: number = 0, dispatchOnce: boolean = false)
|-- get: function(kClass: class): class
```

## Как начать использовать

1. **Установка Tampermonkey (^5.0.0):** https://www.tampermonkey.net/

2. **Установка чит-базы:** [click](https://raw.githubusercontent.com/sheezzmee/CheatBase/main/cheatBase.user.js)

3. **Создание скрипта**:

   ```javascript
    // ==UserScript==
    // @name         Название вашего скрипта
    // @match        https://*.test-eu.tankionline.com/browser-public/index.html?*
    // @match        https://tankionline.com/play*
    // @icon         https://www.google.com/s2/favicons?sz=64&domain=tankionline.com
    // @grant        none
    // @run-at       document-start
    // ==/UserScript==
    
    addEventListener('cheat-base-ready', cheatBase => {
        console.log(cheatBase);
    });
   ```
   
---
☺️
* BTC: bc1q46nyucl4629wae043hvytgslkmh7zp53ed3ndd
* ETH: 0x42e6da6A70A0bd6034F072fa34D21615148FeE88
* USDT (CRC20): 0x42e6da6A70A0bd6034F072fa34D21615148FeE88
* USDT (TRC20): TTxSQSwWm483k6nMzfPJRpWEP4qaFT6Q8W

Happy hacking! 🎮
