<div align="center">
  
# Autonomy validator node on Akash Network
# Нода валидатора сети Autonomy, развертка в Akash Network.
  
</div>
  
<div align="center">

![pba](https://user-images.githubusercontent.com/23629420/163564929-166f6a01-a6e2-4412-a4e9-40e54c821f05.png)
| [Akash Network](https://akash.network/) | [Forum Akash Network](https://forum.akash.network/) | 
|:--:|:--:|
___
Before you start - subscribe to our news channels: 

Прежде чем начать - подпишитесь на наши новостные каналы:

| [Discord Akash](https://discord.gg/WR56y8Wt) | [Telegram Akash EN](https://t.me/AkashNW) | [Telegram Akash RU](https://t.me/akash_ru) | [TwitterAkash](https://twitter.com/akashnet_) | [TwitterAkashRU](https://twitter.com/akash_ru) |
|:--:|:--:|:--:|:--:|:--:|

</div>

<div align="center">
  
| [Discord Autonomy](https://t.me/palomachain) | [Twitter Autonomy](https://twitter.com/paloma_chain) |
|:--:|:--:|
  
</div>

<div align="center">
  
[English version](https://github.com/Dimokus88/paloma#english-version) | [Русская версия](https://github.com/Dimokus88/Autonomy/blob/main/README.md#%D1%80%D0%B0%D0%B7%D0%B2%D0%B5%D1%80%D1%82%D0%BA%D0%B0-%D0%BD%D0%BE%D0%B4%D1%8B-autonomy)
 
</div>

### Развертка ноды Autonomy.

Разверните деплой ноды **Autonomy** с помощью **Akashlytics**  ([Инструкция по использованию здесь](https://github.com/Dimokus88/guides/blob/main/Akashlytics/RU-guide.md)) установив свой пароль для **root** пользователя и имя ноды в соответствующих переменных.

<div align="center">
  
![image](https://user-images.githubusercontent.com/23629420/182032552-04d768ff-ac90-4592-9d38-2e00e8fb4455.png)
 
</div>

На данном этапе развернута **RPC** нода. Перейдя на переадресованный порт **26657** во вкладке ```LEASES``` откроется websocket ноды, где будет доступна ее актуальная информация.

<div align="center">
  
![image](https://user-images.githubusercontent.com/23629420/182032797-70a74454-75dd-4910-8a30-9a88a1715531.png)

![image](https://user-images.githubusercontent.com/23629420/182032818-069eef95-8242-459f-b503-ad8322261482.png)
 
</div>

Если вас интересует нода валидатора - перейдите к следующему пункту.

### Запуск валидатора Autonomy

Подключитесь к работающей ноде по протоколу **SSH**, используя переадресованный **22** порт, пользователь **roo**t и пароль заданный вами в **deploy.yml**:

![image](https://user-images.githubusercontent.com/23629420/182032966-3fa2ffae-5348-4a2c-a4e8-5d33c57ba320.png)

Выполните:

```
source ~/.bashrc
```

Откройте config.toml:

```
nano /root/.autonomy/config/config.toml
```

В разделе **State Sync** измените значение поля ```enable = true``` на ```enable = false```

![image](https://user-images.githubusercontent.com/23629420/182035602-c88af532-321d-4f0b-84b3-32382a8f6fa8.png)

Нажмите сочетание клавиш ```ctrl+x``` , затем ```'y'``` и клавишу ```Enter```, сохранив изменения.

Перезапустите службу ноды командой ```sv restart autonomy``` .

Проверьте статус синхронизации ноды командой ```curl -s localhost:26657/status | jq .result.sync_info.catching_up``` . Если статус **false** - то можете приступить к созданию валидатора. Если статус **true** - дождитесь полной синхронизации.

* Создайте кошелек **Autonomy** или импортируйте по **seed** фразе(Придумайте и змените <WALLET_NAME> своим именем кошелька):

Для создания кошелька используйте команду **(Сохраните SEED фразу иначе вы рискуете потерять все токены и доступ к кошельку!)**

```
autonomy keys add <WALLET_NAME>
```

Для импорта кошелька по seed фразе используйте команду:

```
autonomy keys add <WALLET_NAME> --recover
```

* Проверьте наличие токенов на балансе, для создания валидатора необходимо иметь более 1aut счету(1aut = 1 000 000 uaut).

```
autonomy query bank balances <ADDRESS>
```

* Команда создания валидатора выглядит так(с автоматическим делегированием 1aut) :

```
autonomy tx staking create-validator --amount="1000000$denom" --pubkey=$($binary tendermint show-validator) --moniker="$MONIKER"	--chain-id="$chain"	--commission-rate="0.10" --commission-max-rate="0.20" --commission-max-change-rate="0.01" --min-self-delegation="1000000" --gas="auto"	--from=<ADDRESS> --fees="5550$denom" -y
```

Проверьте созданного валидатора заменив <MONIKER> именем вашего валидатора:

```
autonomy q staking validators -o json | jq .validators[].description.moniker | grep <MONIKER>
```

* Делегируйте на себя оставшиеся токены, предварительно уточнив оставшийся баланс (оставьте 1 000 000 uat для оплаты газа транзакций):

```
autonomy tx staking delegate <VALOPER> <amount>uaut --from <ADDRESS> --chain-id $chain --fees 555uaut -y
```

* Собрать награды:

```
autonomy tx distribution withdraw-rewards <VALOPER> --from <ADDRESS> --fees 500uaut --commission --chain-id $chain -y
```
Другие команды по управлению нодой [можете найти здесь](https://github.com/Dimokus88/guides/blob/main/Cosmos%20SDK/Guide_RU.md#%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-%D0%B4%D0%BB%D1%8F-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%BD%D0%BE%D0%B9-%D1%81%D1%82%D0%BE%D1%80%D0%BE%D0%BA%D0%B8-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%BE%D0%B2-%D1%8D%D0%BA%D0%BE%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%8B-cosmos-sdk).

[К началу](https://github.com/Dimokus88/Autonomy/blob/main/README.md#autonomy-validator-node-on-akash-network)

Спасибо что воспользовались Akash Network!
