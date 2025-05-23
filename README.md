# Техническая Документация: Децентрализованная Биржа TajCrypto Exchange

**Версия:** 1.1 (Обновлено: $TAJ - стейблкоин TJS)
**Дата:** 17 апреля 2025 г.

**Оглавление:**

1.  [Введение](#1-введение)
2.  [Архитектура](#2-архитектура)
3.  [Поддерживаемые Блокчейны](#3-поддерживаемые-блокчейны)
4.  [Смарт-контракты](#4-смарт-контракты)
5.  [Токен TajCrypt ($TAJ) - Стейблкоин TJS](#5-токен-tajcrypt-taj---стейблкоин-tjs)
6.  [Интеграция с Alif Pay (Покупка $TAJ за фиат)](#6-интеграция-с-alif-pay-покупка-taj-за-фиат)
7.  [Торговля на DEX](#7-торговля-на-dex)
8.  [Безопасность](#8-безопасность)
9.  [API/SDK (при наличии)](#9-apisdk-при-наличии)
10. [Глоссарий](#10-глоссарий)

---

## 1. Введение

TajCrypto Exchange представляет собой децентрализованную биржу (DEX), разработанную для обеспечения безопасного, прозрачного и некастодиального обмена цифровыми активами. Платформа работает без центрального органа управления, позволяя пользователям торговать напрямую из своих кошельков (P2P) с использованием смарт-контрактов.

**Ключевые особенности:**

* **Децентрализация:** Пользователи полностью контролируют свои приватные ключи и активы. Биржа не хранит средства пользователей.
* **Мультичейн-поддержка:** Платформа поддерживает торговлю активами в сетях Ethereum, BNB Chain и Solana.
* **Нативный Стейблкоин:** TajCrypt ($TAJ) является **стейблкоином, разработанным для поддержания привязки к таджикскому сомони (TJS)**. Он служит стабильным средством обмена и расчетной единицей в экосистеме.
* **Фиатный шлюз:** Реализована возможность покупки стейблкоина $TAJ с использованием платежной системы Alif Pay (кредитные/дебетовые карты) через партнерский сервис, предоставляя прямой доступ к цифровому эквиваленту TJS.

**Цель документации:** Предоставить техническое описание архитектуры, функциональности и механизмов работы TajCrypto Exchange, включая особенности стейблкоина $TAJ.

---

## 2. Архитектура

Архитектура TajCrypto Exchange разработана с упором на децентрализацию, безопасность и поддержку нескольких блокчейнов.

* **Фронтенд (Frontend):** Веб-интерфейс (и/или мобильное приложение), с которым взаимодействуют пользователи. Он обеспечивает подключение кошельков, отображение рыночных данных, формирование и отправку транзакций в соответствующие блокчейны. Разработан с использованием современных фреймворков (например, React.js, Vue.js).
* **Смарт-контракты (Smart Contracts):** Ядро DEX, развернутое в каждой из поддерживаемых сетей (Ethereum, BNB Chain, Solana). Контракты управляют пулами ликвидности, обменом токенов (свопами) и, потенциально, механизмами поддержания стабильности $TAJ (если применимо). Используется модель Автоматического Маркет-Мейкера (AMM).
* **Инфраструктура Узлов (Node Infrastructure):** Для взаимодействия с блокчейнами используются публичные или собственные узлы (например, через Infura, Alchemy или локальные ноды) для чтения данных и отправки транзакций.
* **Мультичейн Компонент:** Поддержка нескольких сетей реализована путем развертывания независимых наборов смарт-контрактов в каждой сети. Торговля активами происходит *внутри* каждой отдельной сети. *Примечание: Прямые кросс-чейн свопы между сетями Ethereum, BNB и Solana могут быть реализованы через интеграцию с внешними мостами (например, Wormhole, LayerZero), если это предусмотрено.*
* **Фиатный Шлюз (Fiat On-Ramp):** Интеграция с внешним провайдером услуг (например, Onramper, MoonPay, Stripe), который поддерживает Alif Pay. Этот компонент обрабатывает фиатные платежи и KYC/AML, после чего инициирует отправку стейблкоина $TAJ на кошелек пользователя.

---

## 3. Поддерживаемые Блокчейны

TajCrypto Exchange оперирует в следующих блокчейн-сетях:

* **Ethereum:**
    * Стандарт токенов: ERC-20.
    * Смарт-контракты: Развернуты контракты Factory, Router, Pair (для AMM пулов), а также контракт токена $TAJ (ERC-20).
    * Кошельки: Поддерживаются кошельки, совместимые с EVM (MetaMask, Trust Wallet, WalletConnect и др.).
    * Особенности: Высокая безопасность, но потенциально высокие комиссии за транзакции (газ).
* **BNB Chain (Binance Smart Chain):**
    * Стандарт токенов: BEP-20.
    * Смарт-контракты: Аналогичны Ethereum (EVM-совместимость), включая контракт $TAJ (BEP-20).
    * Кошельки: MetaMask, Trust Wallet, Binance Chain Wallet и др.
    * Особенности: Низкие комиссии и высокая скорость транзакций по сравнению с Ethereum.
* **Solana:**
    * Стандарт токенов: SPL (Solana Program Library).
    * Смарт-контракты (Программы): Используются программы, написанные на Rust, для реализации AMM (например, на основе Token Swap program) и для токена $TAJ (SPL).
    * Кошельки: Phantom, Solflare, Slope и др.
    * Особенности: Очень высокая пропускная способность и низкие комиссии. Архитектура отличается от EVM.

**Кросс-чейн Взаимодействие:** На данный момент, торговля происходит изолированно в каждой сети. Пользователь должен выбрать сеть в своем кошельке и интерфейсе биржи для взаимодействия с соответствующими пулами ликвидности. Перевод стейблкоина $TAJ *между* сетями требует использования сторонних мостов.

---

## 4. Смарт-контракты

Основа функциональности DEX построена на смарт-контрактах. В EVM-совместимых сетях (Ethereum, BNB Chain) используются стандартные для AMM DEX контракты:

* **Factory Contract:** Единый контракт, отвечающий за создание новых торговых пар (пулов ликвидности) для различных комбинаций токенов, включая пары с $TAJ.
* **Router Contract:** Основной контракт, с которым взаимодействуют пользователи для выполнения обменов (свопов) и добавления/удаления ликвидности. Он маршрутизирует сделки через соответствующие контракты пар.
* **Pair Contract (Пул ликвидности):** Для каждой торговой пары создается отдельный контракт, который хранит резервы двух токенов и реализует логику AMM (например, `k = x * y` для Constant Product Market Maker). Он также выпускает LP-токены для поставщиков ликвидности.
* **Контракт Токена $TAJ:** Отдельный смарт-контракт для стейблкоина $TAJ (ERC-20, BEP-20, SPL), который определяет его свойства и может включать механизмы для поддержания привязки к TJS (например, через обеспечение, сеньораж или другие модели). `[Здесь должна быть ссылка на детальное описание механизма стабильности, если оно доступно]`

**На Solana:** Архитектура программ может отличаться, но функционально выполняет те же задачи: управление пулами SPL-токенов, расчет цен на основе заложенного алгоритма AMM, обработка обменов и управление токеном $TAJ (SPL).

**Механизм Обмена (AMM):** Цена активов в пуле определяется соотношением их резервов. При обмене одного токена на другой изменяется баланс резервов, что приводит к изменению цены по предопределенной математической формуле (например, `k = x * y`). Пулы с $TAJ (например, $TAJ/USDC, $TAJ/ETH) обеспечивают ликвидность для обмена стейблкоина.

**Безопасность:** Все смарт-контракты, включая контракт токена $TAJ и его механизмы стабильности, должны проходить тщательное тестирование и независимый аудит безопасности. Адреса развернутых и аудированных контрактов публикуются для верификации пользователями.
* *Пример адресов (должны быть заменены реальными):*
    * Ethereum Router: `0x...`
    * BNB Chain Router: `0x...`
    * Solana Swap Program ID: `...`
    * Ethereum $TAJ Token: `0x...`
    * BNB Chain $TAJ Token: `0x...`
    * Solana $TAJ Token: `...`

---

## 5. Токен TajCrypt ($TAJ) - Стейблкоин TJS

$TAJ - это **нативный стейблкоин** платформы TajCrypto Exchange, разработанный с целью поддержания стабильной стоимости, **привязанной к таджикскому сомони (1 $TAJ ≈ 1 TJS)**. Он существует как стандартный токен в каждой из поддерживаемых сетей (Ethereum, BNB Chain, Solana).

* **Назначение:**
    * **Стабильное средство обмена:** Предоставляет пользователям стабильный цифровой актив для торговли, расчетов и сохранения стоимости в рамках экосистемы DeFi, минимизируя волатильность.
    * **Базовый актив для торговых пар:** Используется в качестве базового или котируемого актива во многих торговых парах на DEX (например, BTC/$TAJ, ETH/$TAJ).
    * **Расчетная единица:** Может использоваться для расчетов внутри приложений и сервисов, интегрированных с TajCrypto Exchange.
    * **Потенциально:** Участие в управлении (если предусмотрено отдельным governance-токеном или механизмом) или использование в программах доходного фермерства (yield farming).
* **Механизм Стабильности:** `[Здесь необходимо детально описать механизм поддержания привязки к TJS. Например: обеспечен фиатными резервами, крипто-обеспечением, алгоритмический, гибридный. Указать, кто управляет резервами, как происходит выпуск и сжигание токенов для поддержания паритета.]`
* **Токеномика:**
    * Общее предложение (Total Supply): Динамическое, зависит от спроса и механизма поддержания стабильности (например, выпускается при внесении обеспечения/фиата, сжигается при выкупе).
    * Распределение (Allocation): Неприменимо в традиционном смысле для стейблкоинов; токены выпускаются в обращение по мере необходимости для поддержания привязки.
    * Эмиссия/Сжигание: Происходит в рамках механизма стабилизации.
* **Адреса контрактов $TAJ:**
    * Ethereum (ERC-20): `0x...`
    * BNB Chain (BEP-20): `0x...`
    * Solana (SPL): `...`

---

## 6. Интеграция с Alif Pay (Покупка $TAJ за фиат)

TajCrypto Exchange предоставляет возможность покупки **стейблкоина $TAJ** с использованием кредитных/дебетовых карт через платежную систему Alif Pay. Эта функция реализована через интеграцию со сторонним фиатным шлюзом (on-ramp провайдером) и позволяет пользователям легко конвертировать TJS в их цифровой эквивалент $TAJ.

* **Механизм:**
    1.  Пользователь инициирует покупку $TAJ через интерфейс TajCrypto Exchange.
    2.  Интерфейс загружает виджет или перенаправляет пользователя на защищенную страницу партнерского сервиса (фиатного шлюза).
    3.  Пользователь проходит процедуру верификации личности (KYC/AML) **на стороне партнерского сервиса**, если это требуется сервисом и суммой покупки. *TajCrypto Exchange не собирает и не хранит данные KYC.*
    4.  Пользователь вводит данные своей карты Alif Pay и подтверждает платеж (сумма в TJS) на платформе партнера.
    5.  Партнерский сервис обрабатывает фиатный платеж.
    6.  После успешной оплаты партнерский сервис выпускает/отправляет эквивалентное количество стейблкоина $TAJ (с учетом комиссий шлюза) в соответствующей сети (Ethereum, BNB Chain или Solana) на указанный пользователем адрес кошелька.
* **Процесс для пользователя:**
    1.  Перейти в раздел "Купить $TAJ".
    2.  Выбрать желаемое количество $TAJ или сумму в TJS.
    3.  Подключить свой криптокошелек (например, MetaMask, Phantom) и убедиться, что выбрана нужная сеть.
    4.  Следовать инструкциям в виджете/на странице партнера для ввода данных Alif Pay и прохождения KYC (если необходимо).
    5.  Подтвердить платеж.
    6.  Дождаться поступления $TAJ на свой кошелек (время может варьироваться в зависимости от загруженности сети и скорости обработки партнера).
* **Безопасность:** Взаимодействие с фиатным шлюзом происходит через защищенные протоколы (HTTPS). Платежные данные обрабатываются исключительно на стороне сертифицированного платежного провайдера (партнера). Пользователям следует убедиться, что они находятся на легитимной странице партнера перед вводом данных.
* **Ограничения:** Данная функция предназначена **только для покупки стейблкоина $TAJ**. Покупка других криптовалют через Alif Pay напрямую на DEX не поддерживается. Доступность сервиса может зависеть от юрисдикции пользователя и политики партнерского шлюза.

---

## 7. Торговля на DEX

Процесс обмена токенами на TajCrypto Exchange является стандартным для AMM DEX, с активным использованием стейблкоина $TAJ:

1.  **Подключение кошелька:** Пользователь подключает свой некастодиальный кошелек (MetaMask, Trust Wallet, Phantom и т.д.) к интерфейсу биржи.
2.  **Выбор сети:** Пользователь выбирает блокчейн (Ethereum, BNB Chain, Solana), в котором он хочет совершить обмен.
3.  **Выбор токенов:** Пользователь выбирает пару токенов для обмена (например, ETH на $TAJ, $TAJ на USDC, SOL на $TAJ).
4.  **Ввод суммы:** Указывается количество токена, которое пользователь хочет продать или купить. Интерфейс автоматически рассчитывает примерное количество второго токена на основе текущей цены в пуле ликвидности.
5.  **Подтверждение:** Пользователь проверяет детали сделки (курс, проскальзывание, комиссия сети) и подтверждает транзакцию в своем кошельке. Для первого обмена токена может потребоваться предварительное разрешение (Approve) на взаимодействие с контрактом роутера.
6.  **Исполнение:** Транзакция отправляется в блокчейн. После ее подтверждения майнерами/валидаторами обмен считается завершенным, и токены поступают на кошелек пользователя.

**Пулы Ликвидности:**

* Пользователи могут предоставлять ликвидность для торговых пар, особенно для пар со стейблкоином $TAJ (например, $TAJ/USDC, ETH/$TAJ), внося в пул эквивалентную стоимость обоих токенов пары.
* Взамен они получают LP-токены (Liquidity Provider tokens), представляющие их долю в пуле.
* Поставщики ликвидности получают часть торговых комиссий (обычно 0.05% - 0.3% от объема сделки, в зависимости от типа пула - для стейблкоинов комиссии часто ниже), пропорционально их доле в пуле.
* Добавление/удаление ликвидности также является транзакцией в блокчейне и требует подтверждения в кошельке.
* *Риск:* Поставщики ликвидности подвержены риску непостоянных потерь (Impermanent Loss), хотя для пулов стейблкоин/стейблкоин (например, $TAJ/USDC) этот риск значительно ниже.

**Комиссии:**

* **Торговая комиссия (Swap Fee):** Взимается с каждой сделки (например, 0.05%-0.3%), распределяется между поставщиками ликвидности и/или казной протокола.
* **Комиссия сети (Gas Fee / Network Fee):** Оплачивается пользователем майнерам/валидаторам за обработку транзакции в блокчейне. Размер зависит от текущей загруженности сети.

---

## 8. Безопасность

Безопасность является приоритетом для TajCrypto Exchange.

* **Некастодиальность:** Биржа никогда не получает доступ к приватным ключам или средствам пользователей. Активы всегда остаются под контролем пользователя в его кошельке.
* **Аудиты Смарт-контрактов:** Контракты DEX и токена $TAJ (включая механизм стабильности) проходят независимые аудиты безопасности. Отчеты аудитов публикуются для сообщества.
* **Прозрачность:** Все транзакции и код смарт-контрактов публичны и доступны для проверки в блокчейн-эксплорерах. Механизмы обеспечения стейблкоина (если применимо) должны быть максимально прозрачны.
* **Рекомендации для пользователей:**
    * Надежно храните свою сид-фразу (seed phrase).
    * Используйте надежные кошельки и безопасное окружение.
    * Внимательно проверяйте адреса контрактов токенов.
    * Остерегайтесь фишинговых сайтов.
    * Понимайте риски DeFi, включая риски смарт-контрактов и потенциальное (хотя и маловероятное для стейблкоина) отклонение от привязки к TJS.

---

## 9. API/SDK (при наличии)

`[Если у биржи есть публичный API или SDK для разработчиков, здесь следует предоставить информацию о нем: эндпоинты, методы, документация, примеры использования для получения данных о парах, ценах, объемах или для интеграции торговых функций в сторонние приложения.]`

---

## 10. Глоссарий

* **DEX (Decentralized Exchange):** Децентрализованная биржа.
* **AMM (Automated Market Maker):** Алгоритм ценообразования на DEX.
* **Пул Ликвидности (Liquidity Pool):** Смарт-контракт с резервами токенов для обмена.
* **Смарт-контракт (Smart Contract):** Самоисполняющийся код в блокчейне.
* **LP-токен (Liquidity Provider Token):** Токен, подтверждающий долю в пуле ликвидности.
* **Газ (Gas) / Комиссия Сети (Network Fee):** Плата за транзакции в блокчейне.
* **Проскальзывание (Slippage):** Разница между ожидаемой и фактической ценой исполнения сделки.
* **Некастодиальный кошелек (Non-Custodial Wallet):** Кошелек, где ключи контролирует пользователь.
* **Фиатный Шлюз (Fiat On-Ramp):** Сервис для покупки криптовалюты за фиатные деньги.
* **$TAJ:** **Нативный стейблкоин** биржи TajCrypto Exchange, **привязанный к таджикскому сомони (TJS)**.
* **KYC/AML (Know Your Customer / Anti-Money Laundering):** Процедуры верификации личности.
* **Стейблкоин (Stablecoin):** Криптовалюта, разработанная для поддержания стабильной стоимости относительно другого актива (например, фиатной валюты).
* **TJS (Tajikistani Somoni):** Национальная валюта Таджикистана.

---
