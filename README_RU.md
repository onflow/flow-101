# Flow 101 - Квест 

> Other Languages:
> - 🇨🇳 - [简体中文](README_ZH.md)
> - 🌐 - [English](README.md)

Цель данного квеста в тренировке навыков взаимодействия со смарт-контрактами на цепи Flow. 
В этом квесте вы будете взаимодействовать c контрактом [YearbookMinter][Yearbook](https://flow-view-source.com/testnet/account/0x63ffd70144f80d07/contract/YearbookMinter).
Теория необходимая для понимания этого контракта (а также немного базовых концептов языка Cadence) находится внутри 
файла [THEORY_RU.md](/THEORY_RU.md).

Данный файл содержит практическую часть квеста. Любой кто выполните его получит **`Недвижимый Токен Доказательства 
Знания`** *(Soulbound Proof-of-Knowledge NFT)*

##  Обзор Квеста 📖
1. Создание Testnet аккаунта
2. Взаимодействие с контрактом
   - Подпись транзакции (изменение состояния цепи)
   - Выполнение скриптов (запрос состояния цепи)

## Чем Вы Научитесь 💻
1. Как использовать Flow CLI - фундаментальный инструмент разработчика на Flow
2. Как создать аккаунт на тестовой - Testnet - цепи Flow
3. Как отправлять транзакции, например, как подписать 'Ежегодника Flow' (*Flow Yearbook*)

## Приз 🏆

[<img src="https://user-images.githubusercontent.com/27052451/187195585-30fc757d-c6c4-4e24-9c31-70f89c4bf2b2.png" width=200 />](https://floats.city/andrea.find/event/482557017)

Все кто выполнит этот квест получит **[супер-эксклюзивный недвижимый токен доказательства
знания"](https://floats.city/andrea.find/event/482557017)**. Кроме того, что им можно похвастаться, он также даст 
доступ к закрытым каналам на официальном Discord Flow.

*Заметка: Тип NFT, который мы используем - [FLOATs](https://floats.city/) - поддерживается Instagram, так что вы 
сможете поделиться своим достижением с друзьями, членами семьи и коллегами!

## Часто Задаваемые Вопросы ❓

#### Кто может участвовать в этом квесте?
Абсолютно все! Вам не нужно подходить по каким-либо параметрам :) Просто начни!

#### Как много времени займет выполнение задания?
Если считать, что вы умеете пользоваться коммандной строкой - порядка 15 минут!

## Шаг 0 - Необходимый "реквизит"
**Установите / Обновите Flow CLI**: Вам **понадобится** установленный Flow CLI для того, чтобы выполнить квест. 
Перейдите на страницу [Установка Flow CLI (ENG)](https://developers.flow.com/tools/flow-cli/install) и следуйте 
указаниям. Вам всего-лишь надо будет выполнить одну комманду через терминал для установки Flow CLI. Если же он у вас 
уже установлен, убедитесь, что используете последнюю доступную версию (на той же странице вы можете найти инструкцию 
по обновлению)

#### \>> [Установите или Обновите Flow CLI прежде чем начать квест](https://developers.flow.com/tools/flow-cli/install) <<

**Клонируйте данный репозиторий** (по желанию): Данный репозиторий предоставит вам готовые транзакции и скрипты для 
выполнения квеста. Рекомендуемый нами способ - это клонирование репозитория. В качестве альтернативы, вы можете 
скачать репозиторий и создать необходимые файлы вручную. Для того чтобы клонировать данный репозиторий, используйте 
следующую команду:

```sh
git clone https://github.com/onflow/flow-101-quest
cd flow-101-quest
```

## Шаг 1 - Запустите Flow CLI
Для того чтобы использовать Flow CLI в полную силу вам надо инициализировать его созданием конфигурационного файла. 
Перейдите в свою рабочую папку - откуда вы будете запускать Flow CLI - и выполните следующюю команду в терминале:

```
flow init
```

Вы должны увидеть что-то похожее:

```
Configuration initialized
Service account: 0xf8d6e0586b0a20c7

Start emulator by running: 'flow emulator' 
Reset configuration using: 'flow init --reset'
```

## Шаг 2 - Создайте свой аккаунт на Testnet

Прежде чем приступить к подписи Ежегодника, нам понадобиться аккаунт! К счастью для нас мы можем выполнить 
простую команду в Flow CLI:

```
flow accounts create
```

Вот как будет выглядеть процесс:
##### 1. Назовите свой аккаунт (используйте латинские буквы)
Назовите свой аккаунт `hero` - **Герой** - и нажмите <kbd>Enter</kbd>. Дальше следуйте указаниям на экране.

```
Enter an account name: hero
```

> 💡 Вы можете выбрать другое имя, мы просто будем использовать это для того, чтобы ваш опыт был похож на то, что мы 
> описываем в этой инструкции. Если вы решите использовать другое имя, предполагайте, что вместо `hero` должно 
> стоять ваше имя аккаунта и адрес

##### 2. Выберите в качестве сети Flow Testnet
При помощи стрелочек <kbd>↓</kbd> <kbd>↑</kbd> выберите нужную сеть - **Flow Testnet** - и затем нажмите 
<kbd>Enter</kbd>

```
Use the arrow keys to navigate: ↓ ↑ → ← 
? Choose a network: 
    Local Emulator
  ▸ Flow Testnet
    Flow Mainnet
```

##### 3. Сохраните информацию об аккаунте,
Далее вашем вниманию будет предоставлен шаг подтверждения. Нажмите <kbd>y</kbd> и затем <kbd>Enter</kbd>.

```
✔ Flow Testnet

❗ This command will perform the following:
 - Generate a new ECDSA P-256 public and private key pair.
 - Save the private key to hero.private.json and add it to .gitignore.
 - Create a new account on Flow Testnet paired with the public key.
 - Save the newly-created account to flow.json.


? Do you want to continue? [y/N] y
```

##### 4. Пополните свой Testnet аккаунт

```
Please complete the following steps in a web browser:
 1. Complete the captcha challenge.
 2. Click the 'Create Account' button.
 3. Return to this window.

✔ Press <ENTER> to open in your browser...: █

```
После того как вы нажмете <kbd>Enter</kbd>, вам бразуер автоматически будет перенаправлен на [Flow Testnet Faucet]
(https://testnet-faucet.onflow.org/), где вся необходимая информация уже будет заполнена. Единственное, что остается 
сделать это:

```

Please complete the following steps in a web browser:
 1. Complete the captcha challenge.
 2. Click the 'Create Account' button.
 3. Return to this window.

You can also navigate to the link manually: https://testnet-faucet.onflow.org/?key=<key_that_is_pre_populated>

Waiting for your account to be created, please finish all the steps in the browser...

```

- Выполнить CAPTCHA задание
- Нажать на кнопку `Create Account`
- Вернуться к окну терминала
 
 ![Пополнение аккаунта при помощи "крана"](https://i.imgur.com/P6hyGlk.gif)

##### 6. Все готово!

```
🎉 New account created with address 0xebeb17c521a0d375 and name hero.

Here’s a summary of all the actions that were taken:
 - Added the new account to flow.json.
 - Saved the private key to hero.private.json.
 - Added hero.private.json to .gitignore.
```

После того как выполните эти шаги, вы заметите, что у вас появилось 2 новых файла в вашей директории:

1. `flow.json`
2. `hero.private.json` 

Flow CLI также обновил конфигурационный файл `flow.json` ссылкой на второй файл `hero.private.json`, который 
содержит личный (private) ключ аккаунта. Файл с личным ключом автоматически добавиться в `.gitignore` файл, чтобы вы 
ненароком его не опубликовали.

Если вы откроете файлы, вы сможете найти личный ключ и адрес своего аккаунта 👍!

