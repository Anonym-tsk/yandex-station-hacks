# Доработки Яндекс Станции

### Управление выводом звука на внешние колонки на Станции Мини

Допустим у вас есть Яндекс Станция Мини и вы хотите подключить её к усилителю или внешней акустике. Проблема в том, что если подключить MiniJack к станции, то звук автоматически переключится на внешнюю акустику, даже если она выключена. Описанная доработка позволяет управлять выводом звука со станции, то есть, вы можете выбрать, будет ли станция играть через встроенный динамик, или через внешнюю акустику.

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/station1.jpeg' width='400' /> <img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/station2.jpeg' width='400' />

Суть доработки: внутрь станции подключаем ESP8266 и оптрон, с помощью которых по сети мы сможем переключать вывод звука.

_Нам понадобится:_

1. Яндекс Станция Мини
2. [Wemos D1 Mini](https://www.wemos.cc/en/latest/d1/d1_mini.html) или любая другая отладочная плата на ESP8266 с питанием 5V
3. Оптрон [PC817](https://radiosvod.ru/tranzistor/pc817) или любой другой
4. Резистор 220 Ом (подойдет любой номиналом 100-330 Ом)
5. Паяльник, припой, провода, макетная плата (опционально), термоусадка, изолента, двусторонний скотч

#### 1. Разбираем станцию

Снизу отгибаем резиновую накладку (можно не срывать полностью) и откручиваем 4 винта

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/disassemble_down.jpeg' width='600' />

Снимаем нижнюю крышку и откручиваем еще 6 винтов – 4 на динамике и 2 на плате

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/disassemble_up.jpg' width='600' />

Вынимаем динамик. Снимаем плату. Для снятия платы нужно отогнуть пластиковую часть корпуса с отверстиями usb и jack, после чего вытянуть плату вверх. Шлейф из платы просто выдёргивается, фиксаторов нет.

#### 2. Принцип модификации

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/pinout.jpg' width='600' />

В гнезде MiniJack есть дополнительный контакт `DET`, который замыкается с контактом `AGND` при вставленном штекере. По замыканию контактов `DET` и `AGND` станция переключает звук на внешнюю акустику. Наша задача сделать, чтобы при вставленном штекере контакты не замыкались, а замыкать их самостоятельно через оптрон.

На плате есть два ряда по 4 контакта. С одной стороны нужно взять `+5V` и `GND` для питания ESP, с другой стороны есть контакты `DET` и `AGND`, которые будем замыкать.

Можно найти и отрезать дорожку `DET` от гнезда, но намного проще шилом или тонкой отверткой просто загнуть контакт в разъёме, чтобы он не замыкался штекером.

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/hole.jpg' width='600' />

#### 3. Модификация

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/scheme.jpg' width='800' />

Собираем по схеме. Оптрон с резистором можно собрать навесным, но я сделал на макетке.

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/opto1.jpeg' width='400' /> <img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/opto2.jpeg' width='400' />

У Wemos используется `GPIO5`, `GND` и `VBUS`

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/wemos1.jpeg' width='400' /> <img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/wemos2.jpeg' width='400' />

К плате станции удобнее припаивать снизу, так будет проще проложить провода

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/wires2.jpeg' width='600' />

#### 4. Прошивка

Для прошивки я использовал [ESPHome](https://esphome.io/)

Конфигурацию можно найти здесь: [yandex-station-mini.yaml](esphome/yandex-station-mini.yaml)

Не забудьте прошить перед сборкой.

#### 5. Сборка

Для прокладки проводов паяльником расширяем паз, в котором лежат провода динамика

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/wires_hole.jpg' width='600' />

Собираем бутерброд из плат на двусторонний скотч, мотаем изолентой и пакуем в корпус колонки, закрепив двусторонним скотчем или термоклеем

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/assemble_glue.jpeg' width='400' /> <img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/assemble_pack.jpeg' width='400' />

Прокладываем провода (можно мотнуть тканевой изолентой в месте прокладки) и собираем станцию в обратном порядке

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/assemble_final.jpeg' width='600' />

#### 6. Интеграция с Home Assistant

Добавляем интеграцию [ESPHome](https://my.home-assistant.io/redirect/config_flow_start/?domain=esphome) в Home Assistant и получаем возможность переключать выход звука Яндекс Станции

<img src='https://raw.githubusercontent.com/Anonym-tsk/yandex-station-hacks/master/assets/homeassistant.jpg' width='400' />

Мой сценарий автоматизации такой: у ресивера есть источник `Yandex Station`, при выборе этого источника громкость станции увеличивается, а выход звука переключается на ресивер. При смене источника на любой другой или выключении ресивера, громкость станции понижается и выход переключается обратно на станцию.

Package для Home Assistant: [packages/receiver.yaml](homeassistant/packages/receiver.yaml)
