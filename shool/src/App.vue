<script setup>
import { ref, watch } from 'vue'
import {reactive} from 'vue'

const usedList = ref([])
const usedIndList = ref([])
const roomIndex = ref([1, 2, 3, 4, 5, 6])
const roomLetter = ref(['A', 'B', 'C', 'D', 'E', 'F'])

const roomList = ref([{name: 'Странствие',
  btn1: 'Вверх', btn2: 'Вниз', btn3: 'Налево', btn4: 'Направо',
  fun1: goUp, fun2: goDown, fun3: goLeft, fun4: goRight,
  msgtext: 'Вы решили идти. Куда дальше?'
},
  {name: 'Победа',
    btn1: 'Уйти', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
    fun1: goTown, fun2: '', fun3: '', fun4: '',
    msgtext: "Противник побеждён"
  },
  {name: 'Поражение',
    btn1: 'Да', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
    fun1: res, fun2: '', fun3: '', fun4: '',
    msgtext: 'Вы проиграли'
  },
  {name: 'Бой',
    btn1: 'Атаковать', btn2: 'Увернуться', btn3: 'Убежать', btn4: 'N/A',
    fun1: attack, fun2: dodge, fun3: goTown, fun4: '',
    msgtext: `Вы вступаете в бой!`
  },
    //Дальше случайные комнаты. Начинается с 4
  {name: 'Магазин',
    btn1: 'Купить 10 здоровья (10)', btn2: 'Купить оружие (30)', btn3: 'Уйти', btn4: 'N/A',
    fun1: buyHealth, fun2: buyWeapon, fun3: goTown, fun4: '',
    msgtext: 'Вы вошли в магазин.'
  },
  {name: 'Биомасса', //5
    btn1: 'Сразиться с биомасса', btn2: 'Убежать', btn3: 'N/A', btn4: 'N/A',
    fun1: fightBio, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Биомасса преграждает вам дорогу!'
  },
  {name: 'Донорский аппарат', //6
    btn1: 'Обменяться (10 зд. -> 10 зол.)', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: exchange, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Перед вами возвышается донорский аппарат. Если дать ему крови, что он даст взамен?'
  },
  {name: 'Кривое зеркало', //7
    btn1: 'Остаться', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: exchangeSan, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в комнату, в центре которой стоит кривое зеркало, а рядом - чаша и трубка, проведённая к ней. Под зеркалом табличка, гласящая: "Посмотри, если посмеешь."' +
        'По началу вы видите только себя. Останетесь ли вы, чтобы посмотреть, что будет? дальше'
  },
  {name: 'Серый человек', //8
    btn1: 'Сражаться', btn2: 'Бежать', btn3: 'N/A', btn4: 'N/A',
    fun1: fightGrey, fun2: pseudoTown, fun3: '' , fun4: '',
    msgtext: 'Вдруг из стены выходит серая фигура. Резко, у вас начинается головная боль. Что-то подзказывает вам, что избежать этого боя не получиться. Но в чём же подвох? Что не так с этим странным человеком? '
  },
  {name: 'Комната обмена', //9
    btn1: 'Первая', btn2: 'Вторая ', btn3: 'Третья ', btn4: 'Четвёртая ',
    fun1: exchange1, fun2: exchange2, fun3: exchange3, fun4: exchange4,
    msgtext: 'Вы попадаете в ловушку. Вас окружили четыре двери. В каю из них вы войдёте?'
  },
  {name: 'Архив 1', //10
    btn1: 'Ячейка 1', btn2: 'Ячейка 2', btn3: 'Ячейка 3', btn4: 'Уйти',
    fun1: archive1, fun2: archive2, fun3: archive3, fun4: goTown,
    msgtext: 'Вы попадаете в пыльный архив. Всё в этом месте выглядит ужасно знакомым. Рискнёте ли вы почитать забытые документы?'
  },
  {name: 'Архив 2', //11
    btn1: 'Ячейка 1', btn2: 'Ячейка 2', btn3: 'Ячейка 3', btn4: 'Уйти',
    fun1: archive4, fun2: archive5, fun3: archive6, fun4: goTown,
    msgtext: 'Вы попадаете в чистый архив. Всё в этом месте выглядит чужим. Рискнёте ли вы почитать забытые документы?'
  },
  {name: 'Потерянный', //12
    btn1: 'Сражаться', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightRock, fun2: '', fun3: '', fun4: '',
    msgtext: 'Перед вами стоит человек. Это не зверь, не монстр, такая же потерянная душа, как и ты. Он явно потерял рассудок и настроен агрессивно, перекрывая дорогу к выходу. Вы вынуждены принять бой'
  },
  {name: 'Банкир', //13
    btn1: 'Сражаться', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightMoney, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы подходите к огромным вратам, напоминающим вход в дворец. Только в убеждаете себя в том, что они могут быть выходом, как тут же из них вылетает человек, полностью покрытый мехами и золотыми украшениями. Кажется, он настроен агрессивно.'
  },
  {name: 'Допельгагнер', //14
    btn1: 'Сражаться', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightReplica, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в комнату, которая выглядит как зеркальный лабиринт. На стенах мелькают десятки ваших отражений. Вдруг, одно из них выходит из того, что как вам казалось, было очередным зеркалом. Странно, но это существо выглядит точь в точь как вы.'
  },
  {name: 'Рыцарь', //15
    btn1: 'Сражаться', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightKnight, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'В комнате начинает дуть сильный ветер. Сначала вы не понимате откуда он дует, но затем его источник появляется перед вами. Тут же массивная фигура в рыцарских доспехах'
  },
  {name: 'Комната охраны', //16
    btn1: 'Посмотреть обстановку', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: inspection, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы натыкаетесь на странную комнату, которая выглядит как комната охраны. Кажется, экраны показывают происходящее в комнатах вокруг.'
  },
  {name: 'Казино', //17
    btn1: 'Ставка', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: bet, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в комнату с игровым автоматов. Рядом с ним стоит долговязый человек, вызывающий у вас тревогу воим присутствием. Кажется, он предлагает вам сделать ставку.'
  },
  {name: 'Аптечный автомат', //18
    btn1: 'Купить пилюлю', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: buypill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в комнату, посреди которой стоит вендинговая машина. ВНутри неё находятся разные пилюли. Похоже выбирать их вы не можете, и вам попадётся случайная. Рискнёте ли вы?'
  },
  {name: 'Микрозайм', //19
    btn1: 'Взять заём', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: credit, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы натыкаетесь на подозрительную будку, в которой сидит загадочный человек. Похоже он может выдать вам денег в долг. Но что же произойдёт, если вы этот долг не верёте?'
  },
  {name: 'VIP', //20
    btn1: 'Попробовать войти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: vip, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'На входе в комнату вас останавливает статуя в виде рыцаря. "Сюда можно только людям с достаточным количеством опыта" - говорит он. Сможете ли вы войти?'
  },
  {name: 'Заражение', //21
    btn1: 'Уйти', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
    fun1: infection1, fun2: '', fun3: '', fun4: '',
    msgtext: 'Вдруг, в вас прилетает дротик! Похоже комната - это ловушка. Вы чувствуете себя нехорошо...'
  },
  {name: 'Фонтан', //22
    btn1: 'Испить', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: cure, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из земли бьёт целебный фонтан. Он манит вас, но точно ли вы хотите испить его сейчас?'
  },
  {name: 'Стройка', //23
    btn1: 'Построить комнату (500)', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: construction, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы натыкаетесь на странный пульт. Похоже, вы можете посторить какую угодно комнату на ыундаменте уже существующей.'
  },
  {name: 'Налог!', //24
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: skipl, fun2: '', fun3: '', fun4: '',
    msgtext: 'Заплатите налог! Вы теряете деньги'
  },
  {name: 'Метро', //25
    btn1: 'Сесть на поезд', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: daddygun1, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Похоже, вы наткнулись на метро. Но куда же оно ведёт? Тут нет ни рельсов, ни тоннеля. Поедете ли вы'
  },
    ////
  {name: 'Сомелье', //26
    btn1: 'Сражаться', btn2: 'Убежать', btn3: 'N/A', btn4: 'N/A',
    fun1: fightSomelie, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в огромный банкетный зал. Там сидит человек, жадно потребляющий пищу. Замечая вас, он бросает всё и начинает двигаться в вашу сторону'
  },
  {name: 'Медсестра', //27
    btn1: 'Сражаться', btn2: 'Убежать', btn3: 'N/A', btn4: 'N/A',
    fun1: fightMed, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в место, которое выглядит как заброшенный кабинет врача. Поняв, что тут ничего нет, вы уже собираетесь уходить, однако резко из под одной из кушеток вылезает женщина в красном платье, толстым слоем замотанная в бинты. Похоже, она настроена агрессивно.'
  },
  {name: 'Император', //28
    btn1: 'Построить комнату (500)', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightEmperor, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Вы входите в величественный зал с колонами. Посреди зала стоит Император. Он безмолвен, но по позе ясно, что он хочет битвы.'
  },
  {name: 'Локатор', //29
    btn1: 'Включить радар', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: radar, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Похоже, эта комната - что-то вроде радара. Что будет, если его включить?'
  },
  {name: 'Тренажерный зал', //30
    btn1: 'Позаниматься (10 зл => 1 xp)', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: exchangeXp, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Здесь можно потренироваться. Это полезно, однако за абонемент придётся платить'
  },
  {name: 'ХАОС, ХАОС!', //31
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: goJevilTown, fun2: '', fun3: '', fun4: '',
    msgtext: 'ХАОС, ХАОС! КТО ВРАЩАЕТ ЭТОТ МИР? ВСЕ КОМНАТЫ ПЕРЕМЕШАЛИСЬ!'
  },
  {name: 'Эхо-камера', //32
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: goEchoTown, fun2: '', fun3: '', fun4: '',
    msgtext: 'Вы попадаете в очень странное место, где все звуки кажутся глубже и словно впиваются в глубины вашего сознания. Кажется, все ваши эффекты стали дольше. (Даже если у вас их не было)'
  },
  {name: 'Черепаха', //33
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightTurtle, fun2: '', fun3: '', fun4: '',
    msgtext: 'На вас нападает странное существо с огромным панцирем. Хватит ли у вас сил пробить этот панцирь?'
  },
  {name: 'Зомби', //34
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightZombie, fun2: '', fun3: '', fun4: '',
    msgtext: 'На вас выбегает безумный прокажённый человек. Несмотря на истощённый вид, он похоже резок как чёрт.'
  },
  {name: 'Пожарный', //35
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: fightFirefighter, fun2: '', fun3: '', fun4: '',
    msgtext: 'На вас нападает мужчина в форме и маске пожарного. В его руке зажигательная смесь. Очевидно, добром это не кончиться.'
  },
  {name: 'Кнопка?', //36
    btn1: 'Нажать', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: destruction, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'В этой комнате просто есть странная кнопка. Нажать её?'
  },
  {name: 'Воровство', //37
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: theft, fun2: '', fun3: '', fun4: '',
    msgtext: 'Посреди комнаты стоит странная коробка. Вы с недоверием приближаетесь к ней...'
  },
  {name: 'Ловушка', //38
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: zemlya, fun2: '', fun3: '', fun4: '',
    msgtext: 'Вдруг, в вас прилетает стрела! Похоже комната - это ловушка.В следующий раз будьте аккуратнее!'
  },
  {name: 'Выход', //39
    btn1: 'Выйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: winwin, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Это - выход. Ваша конечная точка. Соберите 7 ключей и наконец сможете покинуть этот кошмар'
  },
  {name: 'Руины', //41
    btn1: 'Уйти', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: goTown, fun2: '', fun3: '', fun4: '',
    msgtext: 'Похоже, здесь произошёл взрыв. Всё вокруг обратилось в бессмысленные руины, делать тут нечего...'
  }

]);
const weaponary = ref([{nameW: 'Палка', atack: 5},
  {nameW: 'Клинок', atack: 30},
  {nameW: 'Молоток', atack: 50},
  {nameW: 'Меч', atack: 100}])
let health = ref(300)
let weaponIndex = ref(0)
const enemyList = ref([
  {nameM: "Потерянный", lvl: 5, healthM: 100},
  {nameM: "Серый человек",  lvl: 5, healthM: 150}, //Ун
  {nameM: "Рыцарь",  lvl: 1, healthM: 250}, //Гн
  {nameM: "Банкир", lvl: 5, healthM: 250}, //Ал
  {nameM: "Реплика", lvl: weaponary.value[weaponIndex.value].atack, healthM: health.value}, // Зав
  {nameM: "Биомасса", lvl: 1, healthM: 20},
  {nameM: "Медсестра", lvl: 5, healthM: 250}, //Пх
  {nameM: "Император", lvl: 6, healthM: 300}, //Гр
  {nameM: "Сомелье", lvl: 5, healthM: 250},  //Чр
  {nameM: "Черепаха", lvl: 3, healthM: 6000},
  {nameM: "Зомби", lvl: 10, healthM: 50},
  {nameM: "Пожарный", lvl: 5, healthM: 100}
])
const impArts = []

let rli = ref(2)
let rii = ref(2)
let sanity = ref(300)
let gold = ref(100)
let message = ref('')
let debug = false
let btn1 = ref('Вверх')
let btn2 = ref('Вниз')
let btn3 = ref('Налево')
let btn4 = ref('Направо')
let enemyIndex = ref(0)
//let coolimage = ref('https://static.tildacdn.com/tild3937-6431-4562-b137-356666366466/placeholderimg-1024x.png')
const inventory = ref(["палка"]);
let roomLIndex = ref(0)
let absent2 = ref(true)
let absent3 = ref(true)
let absent4 = ref(true)
let statsM = ref(false)
let healthM = ref(enemyList.value[enemyIndex.value].healthM)
let nameM = ref(enemyList.value[enemyIndex.value].nameM)
let fightdisp = ref(false)
let xp = ref(0)
//let figlimigli = ref(`${rli}${rii}`)
let greyPrank = false
let insanePrank = false
const randonify = () => {
  let chirme = Math.floor(Math.random() * (38 - 4 + 1)) + 4
  while (usedList.value.includes(chirme)) {
    chirme = Math.floor(Math.random() * (38 - 4 + 1)) + 4
  }
  usedList.value.push(chirme)
  return chirme
}
const utilityRooms = ref([
    {name: 'Архив 1.1',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback, fun2: '', fun3: '', fun4: '',
      msgtext: 'Вы находите странную фотографию. На ней изображены вы и какая-то незнакомая вам женщина. Кто бы это мог быть?'
    },
    {name: 'Архив 1.2',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback, fun2: '', fun3: '', fun4: '',
      msgtext: 'Вы находите скомканый кусок бумаги. На нём аккуратным подчерком выведены следующие слова: "БРОСЬ ЭТО ДЕЛО! Просто смирись и живи дальше. Этот демон не вернёт её тебе, как бы ты этого не хотел! Я смотрел на твои устройства и, как эксперт, могу сказать, что они - нерабочий мусор. Одумайся! Ты преследуешь свой хвост. Ты потеряешь так очень много времени. А если ты его догонишь, то ты вообще можешь исчезнуть. Прошу, хватит этим заниматься!"'
    },
    {name: 'Архив 1.3',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback, fun2: '', fun3: '', fun4: '',
      msgtext: 'Вы находите записку, на которой спешным корявым подчерком написаны следующие слова: "Я это ты. Я знаю что эту записку прочитаю именно я, поскольку в этом подземелье нет больше никого живого. Если ты это читаешь, то знай, что ты проходишь этот путь уже тысячный раз. Это петля, у которой нет выхода. Если ты хочешь выйти по настоящему, тебе нужно открыть VIP-комнату. Она - твоя последняя надежда. Я уже не успею, они почти сломали дверь."'
    },
    {name: 'Архив 2.1',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback2, fun2: '', fun3: '', fun4: '',
      msgtext: ' Из отчёта ОСП от [УДАЛЕНО]: "Сущность, получившая кодовое название EGAC#99-01, также известная как Демон хаоса, на данный момент является одной из мощнейших сущностей, когда либо созданный Матерью. Его основная способность заключается в абсолютно хаотичном, но при этом весьма сильном преображении реальности вокруг себя. Несмотря на фактическую способность управлять реальностью, существо, похоже, не обладает разумом или собственной волей. Хаотично воплощаясь в разных местах, оно несёт разрушения и сильный ущерб..."'
    },
    {name: 'Архив 2.2',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback2, fun2: '', fun3: '', fun4: '',
      msgtext: 'Отчёт ОСП об инциденте №6567: "Во время седьмого воплощения EGAC#99-01, оперативник [УДАЛЕНО] вступил в контакт с существом, в результате которого провалился внутрь его тела. Дальнейшая его судьба неизвестна. Руководство зоны [УДАЛЕНО] приняло решение о признании пострадавшего погибшим"'
    },
    {name: 'Архив 2.3',
      btn1: 'Обратно', btn2: 'N/A', btn3: 'N/A', btn4: 'N/A',
      fun1: plsback2, fun2: '', fun3: '', fun4: '',
      msgtext: 'Из записки руководству зоны [УДАЛЕНО] от [УДАЛЕНО]: "Как вы не понимаете? Он не погиб! Он всё ещё жив там, по ту сторону мира. Я знаю почему вы пытаетесь замять эту историю. Вы просто панически боитесь признать, что вы не можете достать его оттуда. Я прав?"'
    }],
);
const emoji = [
'','','','','🏪','🩸', '💉', '🧠', '🕴', '🔁', '🔲', '🔳', '🧍‍♂️', '💸', '👥', '🤺', '🎦', '🎰', '💊', '🏧', '🥇', '☣', '⛲', '🛠', '💷', '🚇', '🧛‍♂️', '💝️', '🤴', '📻', '🏆', '😵', '🔊', '🐢', '🧟‍♂️', '👨‍🚒', '🔘', '🐱‍👤', '🏹', '🚪', '✖' //36/36
]
const xyzlift = ref([
  {fig: "A1", fun: randonify(), vall: ''}, {fig: "A2", fun: randonify(), vall: ''}, {fig: "A3", fun: randonify(), vall: ''}, {fig: "A4", fun: randonify(), vall: ''}, {fig: "A5", fun: randonify(), vall: ''}, {fig: "A6", fun: randonify(), vall: ''},
  {fig: "B1", fun: randonify(), vall: ''}, {fig: "B2", fun: randonify(), vall: ''}, {fig: "B3", fun: randonify(), vall: ''}, {fig: "B4", fun: randonify(), vall: ''}, {fig: "B5", fun: randonify(), vall: ''}, {fig: "B6", fun: randonify(), vall: ''},
  {fig: "C1", fun: randonify(), vall: ''}, {fig: "C2", fun: randonify(), vall: ''}, {fig: "C3", fun: 39, vall: ''}, {fig: "C4", fun: randonify(), vall: ''}, {fig: "C5", fun: randonify(), vall: ''}, {fig: "C6", fun: randonify(), vall: ''},
  {fig: "D1", fun: randonify(), vall: ''}, {fig: "D2", fun: randonify(), vall: ''}, {fig: "D3", fun: randonify(), vall: ''}, {fig: "D4", fun: randonify(), vall: ''}, {fig: "D5", fun: randonify(), vall: ''}, {fig: "D6", fun: randonify(), vall: ''},
  {fig: "E1", fun: randonify(), vall: ''}, {fig: "E2", fun: randonify(), vall: ''}, {fig: "E3", fun: randonify(), vall: ''}, {fig: "E4", fun: randonify(), vall: ''}, {fig: "E5", fun: randonify(), vall: ''}, {fig: "E6", fun: randonify(), vall: ''},
  {fig: "F1", fun: randonify(), vall: ''}, {fig: "F2", fun: randonify(), vall: ''}, {fig: "F3", fun: randonify(), vall: ''}, {fig: "F4", fun: randonify(), vall: ''}, {fig: "F5", fun: randonify(), vall: ''}, {fig: "F6", fun: randonify(), vall: ''},
])
for (let rry of xyzlift.value) {
  console.log(rry.fun)
}
/*const displayT = [
  {id: "A1", vall: ''}, {id: "B1", vall: ''}, {id: "C1", vall: ''}, {id: "D1", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
  {id: "A2", vall: ''}, {id: "B2", vall: ''}, {id: "C2", vall: ''}, {id: "D2", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
  {id: "A3", vall: ''}, {id: "B3", vall: ''}, {id: "C3", vall: ''}, {id: "D3", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
  {id: "A4", vall: ''}, {id: "B4", vall: ''}, {id: "C4", vall: ''}, {id: "D4", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
  {id: "A5", vall: ''}, {id: "B5", vall: ''}, {id: "C5", vall: ''}, {id: "D5", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
  {id: "A6", vall: ''}, {id: "B6", vall: ''}, {id: "C6", vall: ''}, {id: "D1", vall: ''}, {id: "E1", vall: ''}, {id: "F1", vall: ''},
] */
const jk0 = watch(health, (newX) => {
  if (health.value <= 0) {
    lose()
  }
})
const getFunByFig = (figValue) => {
  const found = xyzlift.value.find(item => item.fig === figValue)
  return found.fun
}
const getVallByFig = (vallValue) => {
  const found = xyzlift.value.find(item => item.fig === vallValue)
  return found.vall
}
const getIDByFig = (figValue) => {
  const found = xyzlift.value.find(item => item.fig === figValue)
  return xyzlift.value.lastIndexOf(found)
}
const getIDByFun = (funValue) => {
  const found = xyzlift.value.find(item => item.fun === funValue)
  console.log(found)
  return xyzlift.value.lastIndexOf(found)
}
function getRoomByName(nameValue) {
  const found = roomList.value.find(item => item.name === nameValue)
  return roomList.value.lastIndexOf(found)
}
function getFunById(idValue) {
  const found = xyzlift.value.find(item => item.id === idValue)
  return xyzlift.value.lastIndexOf(found)
}
const stringify = (letter, number) => {
  letter += String(number)
  return letter
}

function update() {
  message.value = roomList.value[roomLIndex.value].msgtext
  btn1.value=roomList.value[roomLIndex.value].btn1
  btn2.value=roomList.value[roomLIndex.value].btn2
  btn3.value=roomList.value[roomLIndex.value].btn3
  btn4.value=roomList.value[roomLIndex.value].btn4
  change1.value=roomList.value[roomLIndex.value].fun1
  if (roomList.value[roomLIndex.value].fun2 !== '') {
    absent2.value = true;
    change2.value=roomList.value[roomLIndex.value].fun2
  } else {
    absent2.value = false;
  }
  if (roomList.value[roomLIndex.value].fun3 !== '') {
    absent3.value = true;
    change3.value=roomList.value[roomLIndex.value].fun3
  } else {
    absent3.value = false;
  }
  if (roomList.value[roomLIndex.value].fun4 !== '') {
    absent4.value = true;
    change4.value=roomList.value[roomLIndex.value].fun4
  } else {
    absent4.value = false;
  }
}


let dangerArray = []
function radar() {
  let danger1 = getIDByFun(5)
  dangerArray.push(danger1)
  let danger2 = getIDByFun(8)
  dangerArray.push(danger2)
  let danger3 = getIDByFun(9)
  dangerArray.push(danger3)
  let danger4 = getIDByFun(12)
  dangerArray.push(danger4)
  let danger5 = getIDByFun(13)
  dangerArray.push(danger5)
  let danger6 = getIDByFun(14)
  dangerArray.push(danger6)
  let danger7 = getIDByFun(15)
  dangerArray.push(danger7)
  let danger8 = getIDByFun(21)
  dangerArray.push(danger8)
  let danger9 = getIDByFun(24)
  dangerArray.push(danger9)
  let danger10 = getIDByFun(26)
  dangerArray.push(danger10)
  let danger11 = getIDByFun(27)
  dangerArray.push(danger11)
  let danger12 = getIDByFun(28)
  dangerArray.push(danger12)
  let danger13 = getIDByFun(31)
  dangerArray.push(danger13)
  let danger14 = getIDByFun(33)
  dangerArray.push(danger14)
  let danger15 = getIDByFun(34)
  dangerArray.push(danger15)
  let danger16 = getIDByFun(35)
  dangerArray.push(danger16)
  let danger17 = getIDByFun(37)
  dangerArray.push(danger17)
  let danger18 = getIDByFun(38)
  dangerArray.push(danger18)
  console.log(dangerArray)
  for (let dangerId of dangerArray) {
    if(dangerId >= 0) {
      xyzlift.value[dangerId].vall = "⚠"
    }
  }
}

const sanityWatcher = watch(roomLIndex, (newX) => {
  if (sanity.value <= 50) {
    insanePrank = true
    let rlii = roomLetter.value[rli.value]
    let riii = roomIndex.value[rii.value]
    let shigechi = stringify(rlii, riii)
    let shitindex = getIDByFig(shigechi)
    xyzlift.value[shitindex].vall = emoji[Math.floor(Math.random() * (2 - 25 + 1)) + 25]
  } else (
      insanePrank = false
  )
})

function winwin() {
  if (impArts.length >= 7 && !(impArts.includes('Потёртая медаль'))) {
    console.log(impArts)
    message.value = "Вы открываете дверь и видите... бесконечную опустошающую бездну. Вы падаете на колени и вам больно от осознани того, что всё это было зря. Вы вспоминаете все те десятки тысяч раз, когда вы пытались выйти, однако находили лишь разочарование. Это место просто так не отпускает... Чтож, похоже придётся сделать ещё один круг..."
    btn1 = "Открыть глаза"
    change1.value = res
    change2.value = ''
    change3.value = ''
    change4.value = ''
  } else if (impArts.length >= 7 && impArts.includes('Потёртая медаль')) {
    message.value = "Вы открываете дверь и видите... пшеничное поле. Почему оно здесь? Зачем оно здесь? Это реальный мир? Неужели удалось выбраться? Все эти вопросы гаснут, по итогу исчезая в бесконечном чувстве умиротворения. Даже если у вас не получилось выйти, это уже не важно. Наконец-то вы обрели место, где можете отдохнуть от бесконечного хода по кругу... Вы ложитель в пшеничное поле и закрываете глаза"
    change1.value = ''
    change2.value = ''
    change3.value = ''
    change4.value = ''
  } else {
    message.value = "Вы собрали не все ключи"
  }
}

function inspection() {
  let qroomLIndex = ref()
  let shigechi = stringify(roomLetter.value[rli.value], roomIndex.value[rii.value])
  let shitindexu = getIDByFig(shigechi) - 6
  let shitindexd = getIDByFig(shigechi) + 6
  let shitindexl = getIDByFig(shigechi) - 1
  let shitindexr = getIDByFig(shigechi) + 1
      /* console.log(`${shitindexu} - верхний индеекс, ${xyzlift.value[shitindexu].fun}`)
  console.log(`${shitindexd} - нижний индекс, ${xyzlift.value[shitindexd].fun}`)
  console.log(`${shitindexl} - левый индекс, ${xyzlift.value[shitindexl].fun}`)
  console.log(`${shitindexr} - правый индекс, ${xyzlift.value[shitindexr].fun}`) */
  if (shitindexu !== null) {
    if (typeof xyzlift.value[shitindexu] !== 'undefined') {
      console.log(xyzlift.value[shitindexu])
      console.log(typeof xyzlift.value[shitindexu])
      qroomLIndex.value = xyzlift.value[shitindexu].fun
      xyzlift.value[shitindexu].vall = emoji[qroomLIndex.value]
    }
  }
  if (shitindexd !== null) {
    if (typeof xyzlift.value[shitindexd] !== 'undefined') {
    qroomLIndex.value = xyzlift.value[shitindexd].fun
    xyzlift.value[shitindexd].vall = emoji[qroomLIndex.value]}
  }
  if (shitindexl !== null) {
    if (typeof xyzlift.value[shitindexl] !== 'undefined') {
    qroomLIndex.value = xyzlift.value[shitindexl].fun
    xyzlift.value[shitindexl].vall = emoji[qroomLIndex.value]}
  }
  if ( shitindexr !== null) {
    if ( typeof xyzlift.value[shitindexr] !== 'undefined') {
    qroomLIndex.value = xyzlift.value[shitindexr].fun
    xyzlift.value[shitindexr].vall = emoji[qroomLIndex.value]}
  }
}

function goUp() {
  if (rii.value === 0) {
    message.value = "Вы не можете пройти вверх! Там стена."
  } else {
    rii.value--
    let rlii = roomLetter.value[rli.value]
    let riii = roomIndex.value[rii.value]
    let shigechi = stringify(rlii, riii)
    roomLIndex.value = getFunByFig(shigechi)
    let shitindex = getIDByFig(shigechi)
    xyzlift.value[shitindex].vall = emoji[roomLIndex.value]
    update(roomList.value[roomLIndex.value]);
  }
}
function goDown() {
  if (rii.value === 5) {
    message.value = "Вы не можете пройти вниз! Там стена."
  }
  else {
    rii.value++
    let rlii = roomLetter.value[rli.value]
    let riii = roomIndex.value[rii.value]
    let shigechi = stringify(rlii, riii)
    roomLIndex.value = getFunByFig(shigechi)
    let shitindex = getIDByFig(shigechi)
    xyzlift.value[shitindex].vall = emoji[roomLIndex.value]
    update(roomList.value[roomLIndex.value]);
  }
}
function goLeft() {
  if (rli.value === 0) {
    message.value = "Вы не можете пройти налево! Там стена."
  } else {
    rli.value--
    let rlii = roomLetter.value[rli.value]
    let riii = roomIndex.value[rii.value]
    let shigechi = stringify(rlii, riii)
    roomLIndex.value = getFunByFig(shigechi)
    let shitindex = getIDByFig(shigechi)
    xyzlift.value[shitindex].vall = emoji[roomLIndex.value]
    update(roomList.value[roomLIndex.value]);
  }
}
function goRight() {
  if (rli.value === 5) {
    message.value = "Вы не можете пройти направо! Там стена."
  } else {
    rli.value++
    let rlii = roomLetter.value[rli.value]
    let riii = roomIndex.value[rii.value]
    let shigechi = stringify(rlii, riii)
    roomLIndex.value = getFunByFig(shigechi)
    let shitindex = getIDByFig(shigechi)
    xyzlift.value[shitindex].vall = emoji[roomLIndex.value]
    update(roomList.value[roomLIndex.value]);
  }
}
function goTown() {
  roomLIndex.value = 0;
  fightdisp.value = false
  update(roomList.value[roomLIndex.value]);
}

function goJevilTown() {
  for (let field of xyzlift.value) {
    for (let i = 0; i < 36; i++) {
      xyzlift.value[i].vall = ''
    }
    if (field.fig === "C3") {
      field.fun = 39
    }
  }
  usedList.value.length = 0
  for (let field of xyzlift.value) {
    if (field.fig !== "C3") {
      field.fun = randonify()
    }
  }
  roomLIndex.value = 0;
  fightdisp.value = false
  update(roomList.value[roomLIndex.value]);
}

function res() {
  greyPrank = false
  xp.value = 0;
  health.value = 100;
  sanity.value = 100;
  depcount = 0
  gold.value = 50;
  rii.value = 2
  rli.value = 2
  weaponIndex.value = 0;
  inventory.value = ["stick"];
  impArts.length = 0
  for (let i = 0; i < 36; i++) {
    xyzlift.value[i].vall = ''
  }
  goJevilTown()
}
function buyHealth() {
  if (gold.value >= 10) {
    gold.value -= 10;
    health.value += 10;
  } else {
    message.value = 'Недостаточно сбережений'
  }
}
function buyWeapon() {
  if (weaponIndex.value <= 2)  {
    if (gold.value >= 30) {
      gold.value -= 30;
      weaponIndex.value += 1;
      let newWeapon = ref(weaponary.value[weaponIndex.value].nameW)
      inventory.value.push(newWeapon.value)
      message.value = `${newWeapon.value} добавлен в инвентарь.`
      message.value += `У вас в инвентаре: ${inventory.value} `
    } else {
      message.value = 'Недостаточно сбережений'
    }
  } else  {
    message.value = 'У вас уже имеется самое сильное оружие!'
    btn2.value = "Продать старое оружие"
    change2.value = sellWeapon
  }}

function attack() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  health.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  if (isMonsterHit) {
    healthM.value -= weaponary.value[weaponIndex.value].atack + Math.floor(Math.random() * xp.value) + 1;
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  if (health.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
    } else if (enemyIndex.value === 0) {
      defeatMonster();
      message.value = "Дело сделано... ваши руки трясутся, но у вас не было другого выбора..."
      let rory = getIDByFun(12)
      console.log(rory)
      xyzlift.value[rory].fun = 0
      xyzlift.value[rory].vall = ''
      roomList.value[3].fun3 = goTown
    } else if (enemyIndex.value === 4) {
      defeatMonster();
      message.value = "Существо обратилось в бесформенную массу глины. Из этой массы в лепите ключ."
      if (!(impArts.includes("Глиняный ключ"))) {
        impArts.push("Глиняный ключ")
      }
    }else if (enemyIndex === 9) {
      gold.value += 1000
      roomList.value[33].fun1 = ''
    } else if (enemyIndex === 10) {
      gold.value += 1000
      roomList.value[34].fun1 = ''
    } else if (enemyIndex === 11) {
      gold.value += 1000
      roomList.value[35].fun1 = ''
      //larpzo
    }  else {
      defeatMonster();
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}
function getMonsterAttackValue() {
  const hit = (enemyList.value[enemyIndex.value].lvl * 5) - (Math.floor(Math.random() * xp.value));
//  console.log(hit);
  return hit > 0 ? hit : 0;
}

function isMonsterHit() {
  return Math.random() > .2 || health.value < 20;
}

function attackSan() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  sanity.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  if (isMonsterHit) {
    healthM.value -= weaponary.value[weaponIndex.value].atack + Math.floor(Math.random() * xp.value) + 1;
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  if (sanity.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
      console.log('YES')
    } else {
      defeatMonster();
      message.value = "Уняв головную боль, вы чувствуете в вашей руке ключ. Как он там оказался? Резко ваш разум становится яснее."
      if (!(impArts.includes("Изогнутый ключ"))) {
      impArts.push("Изогнутый ключ")
      }
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}

function attackMon() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  if (inventory.value.length !== 1 && gold.value <= 0) {
    message.value += " Ваш " + inventory.value.pop() + " продан!.";
    gold.value += 60
    weaponIndex.value--;
  } else if (inventory.value.length === 1 && gold.value <= 0) {
    health.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  } else {
    gold.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  }
  if (health.value <= 0) {
    lose();
  }
  if (isMonsterHit) {
    console.log(weaponIndex.value)
    healthM.value -= weaponary.value[weaponIndex.value].atack + Math.floor(Math.random() * xp.value) + 1;
    console.log(weaponIndex.value)
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  if (sanity.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
      console.log('YES')
    } else {
      defeatMonster();
      message.value = "Вы собрали всё золото, что осталось от банкира. В нём вы нашли золотой ключ, инкрустированный брилиантами"
      gold.value += 5000
      if (!(impArts.includes("Инкрустированный ключ"))) {
        impArts.push("Инкрустированный ключ")
      }
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}
function attackChrev() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  health.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  healthM.value += getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  if (isMonsterHit) {
    healthM.value -= weaponary.value[weaponIndex.value].atack + Math.floor(Math.random() * xp.value) + 1;
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  if (health.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
    } else if (enemyIndex.value === 8) {
      defeatMonster();
      message.value = "После победы над врагом, вы снимаете с его шеи массивный слегка погнутый красный ключ."
      if (!(impArts.includes("Красный ключ"))) {
      impArts.push("Красный ключ")
      }
    } else {
      defeatMonster();
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}
let attackbonus = 0
function attackKnight() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  if (isMonsterHit) {
    attackbonus += 10
    healthM.value -= weaponary.value[weaponIndex.value].atack + Math.floor(Math.random() * xp.value) + 1;
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  health.value -= (getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl) + attackbonus);
  if (health.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 'ido[iepufpe9ifefhfefiefefef') {
      winGame();
    } else if (enemyIndex.value === 2) {
      defeatMonster();
      message.value = "Рыцарь повержен. Из доспехов на его груди вывалилось нечто отдалённо напоминающее ключ. При детальном рассмотрении вы понимаете, что это приспособление необходимо скорее для уничтожения, чем для вскрытия замков. Тем не менее, вы оставляете это себе."
      if (!(impArts.includes("Отмычка-уничтожитель"))) {
      impArts.push("Отмычка-уничтожитель")
      }
    } else {
      defeatMonster();
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}

function attackMed() {
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  health.value -= getMonsterAttackValue(enemyList.value[enemyIndex.value].lvl);
  if (isMonsterHit) {
    healthM.value -= xp.value
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  if (health.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
      console.log('YES')
    } else {
      defeatMonster();
      xp.value += 50
      message.value = "Император, преклоняя перед вами колено протягивает вам руки. В них что-то бсетит и вы решаете присвоить это себе. Вам кажется, что вас благословили, пускай вы и не хотели этого благословения."
      if (!(impArts.includes("Хрустальный ключ"))) {
      impArts.push("Хрустальный ключ")}
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}

function attackMedTrue() {
  if (isMonsterHit) {
    healthM.value -= (health.value - sanity.value)
    console.log((health.value - sanity.value))
    console.log(healthM.value + "Здоровье монстра")
  } else {
    message.value = `Вы не попали ):`
  }
  message.value = `${enemyList.value[enemyIndex.value].nameM} атакует!`
  message.value += `Вы атакуете его в ответ своим ${weaponary.value[weaponIndex.value].nameW}.`
  sanity.value -= 3
  health.value -= 6
  if (health.value <= 0) {
    lose();
  } else if (healthM.value < 0) {
    if (enemyIndex.value === 2) {
      winGame();
      console.log('YES')
    } else {
      defeatMonster();
      message.value = "Сражённая медсестра протягивает вам коробку в форме сердца. Внутри лежит красный ключ в какой-то липкой бурой субстанции."
      if (!(impArts.includes("Хрустальный ключ"))) {
      impArts.push("Липкий ключ")}
    }
  }
  if (Math.random() <= .1 && inventory.value.length !== 1) {
    message.value += " Ваш " + inventory.value.pop() + " ломается.";
    weaponIndex.value--;
  }
}

function defeatMonster() {
  gold.value += Math.floor(enemyList.value[enemyIndex.value].lvl * 6.7);
  xp.value += enemyList.value[enemyIndex.value].lvl;
  roomLIndex.value = 1;
  update(roomList.value[roomLIndex.value])
  if (enemyIndex.value !== 2) {
    healthM.value = enemyList.value[enemyIndex.value].healthM;
  }
}
function goFight() {
  roomLIndex.value = 3
  update(roomList.value[roomLIndex.value]);
  if (enemyIndex == 0) {
    roomList.value[3].fun3 = ''
  }
  statsM.value = true;
}
function dodge() {
  message.value = `Вы увернулись от атаки ${enemyList.value[enemyIndex.value].name}. `
}
// Зона жёстких сражений. -------------------
function fightRock() {
  enemyIndex.value = 0;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
}

function fightGrey() {
  enemyIndex.value = 1;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackSan
}

function fightKnight() {
  enemyIndex.value = 2;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackKnight
}

function fightMoney() {
  enemyIndex.value = 3;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackMon
}

function fightReplica() {
  enemyIndex.value = 4;
  healthM.value = health.value
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
}
function fightBio() {
  enemyIndex.value = 5;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
}
function fightMed() {
  enemyIndex.value = 6;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackMedTrue
}
function fightEmperor() {
  enemyIndex.value = 7;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackMed
}
function fightSomelie() {
  enemyIndex.value = 8;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  change1.value=attackChrev
}
function fightTurtle() {
  enemyIndex.value = 9;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
}
function fightZombie() {
  enemyIndex.value = 10;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
}
let firebool = false
let firecount = 0
function fightFirefighter() {
  enemyIndex.value = 11;
  healthM.value = enemyList.value[enemyIndex.value].healthM
  nameM.value = enemyList.value[enemyIndex.value].nameM
  fightdisp.value = true
  goFight()
  firebool = true
  const jk0 = watch(roomLIndex, (newX) => {
    firecount++
    health.value -= 100
    if (firecount > 5) {
      firecount = 0
      jk0()
      firebool = false
    }
  })
}
let rori = false
let balsalm = ref('')
////////////////////////////
let chet1
let nechet1
let depcount = 0
console.log(depcount)
function chet() {
  chet1 = true
  stavka()
}
function nechet() {
  nechet1 = true
  stavka()
}
function bet() {
  if (depcount === 5) {
  message.value = 'Извините, вы не можете больше ставить'
} else {
    change1.value = chet
    change2.value = nechet
    btn1.value = "Поставить на чётное"
    btn2.value = "Поставить на нечётное"
  }
}
function stavka() {
  change1.value = dep
  btn1.value = "Крутить число!"
  change2.value = ''
  btn2.value = ''
  message.value = "Ставьте! (Принимаются ставки до 5000)"
  rori = true
  //console.log(balsalm.value)

}
let dodep = ref('')
function dep() {
  depcount++
  if (balsalm.value === '') {
    message.value = "Введите ставку!"
  }
  rori = false
  let mimi = Math.floor(Math.random() * (40 - 4 + 1)) + 4
  if (Number(balsalm.value) > 5000) {
      message.value = 'Принимаются ставки только до 5000'
      depcount--
      bet()
  } else if ((mimi % 2) === 0 & chet1) {
    console.log(`Чётное - ${mimi}`)
    message.value = `ВЫ ВЫИГРАЛИ! Число: ${mimi}`
    gold.value += Number(balsalm.value)
    change1.value = goTown
    btn1.value = "Уйти"
    chet1 = false
    nechet1 = false
  } else if ((mimi % 2) !== 0 & nechet1) {
    console.log(`Нечётное - ${mimi}`)
    message.value = `ВЫ ВЫИГРАЛИ! Число: ${mimi}`
    gold.value += Number(balsalm.value)
    change1.value = goTown
    btn1.value = "Уйти"
    chet1 = false
    nechet1 = false
  } else {
  message.value = `ВЫ ПРОИГРАЛИ! Число: ${mimi}`
    if (balsalm.value > gold.value) {
      message.value += ". Поскольку денег для расплаты у вас недостаточно, платить ввы будете здоровьем!"
      health.value -= balsalm.value
      if (health.value <= 0) {
        (`Нечётное: ${nechet1} Чётное: ${chet1}  -- ${mimi}`)
        lose()
        message.value += ' Казино всегда выигрывает.'
      }
    } else {
      console.log(`Нечётное: ${nechet1} Чётное: ${chet1} -- ${mimi}`)
      gold.value -= balsalm.value
      change1.value = goTown
      btn1.value = "Уйти"
      chet1 = false
      nechet1 = false
    }
  }
}
////////////////////
let pillcount = 0
let revolve = false
function jevilPill() {
  revolve=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    for (let field of xyzlift.value) {
      if (field.fig !== "C3") {
        field.fun = ''
      }
    }
    usedList.value.length = 0
    for (let field of xyzlift.value) {
      if (field.fig !== "С3") {
        field.fun = randonify()
      }
    }
    if (pillcount >= 15) {
      message.value = "Действие той ужасной пилюли закочилось, но кажется подземелье теперь выглядит иначе..."
      pillcount = 0
      jk0()
      revolve = false
    }
  })
  goTown()
}
let seer = false
function seeingPill() {
  seer=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    inspection()
    console.log(pillcount)
    if (pillcount > 10) {
      message.value = "Действие пилюли кончилось"
      pillcount = 0
      jk0()
      seer = false
    }
  })
  goTown()
}
let illp = false
function rottingPill() {
  illp=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    health.value -= 25
    sanity.value -= 25
    console.log(pillcount)
    if (pillcount > 10) {
      message.value = "Действие пилюли кончилось. Тело содрогают болезненные судороги, но вы вздыхаете с облегчением."
      pillcount = 0
      jk0()
      illp = false
    }
    else if (curet) {
      jk0()
      countinf = 0
    }
  })
  goTown()
}
let bloo = false
function bloomingPill() {
  bloo=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    health.value += 25
    sanity.value += 25
    console.log(pillcount)
    if (pillcount > 10) {
      message.value = "Действие пилюли кончилось. Тело и разум ликуют."
      pillcount = 0
      jk0()
      bloo = false
    }
  })
  goTown()
}
let weak = false
function weaknessPill() {
  weak=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    xp.value -= 1
    console.log(pillcount)
    if (pillcount > 15) {
      message.value = "Действие пилюли кончилось. К несчастью, сила не возвращается обратно."
      pillcount = 0
      jk0()
      weak = false
    } else if (curet) {
      jk0()
      countinf = 0
    }
  })
  goTown()
}
let stren = false
function strengthPill()  {
  stren=true
  console.log(pillcount)
  const jk0 = watch(roomLIndex, (newX) => {
    pillcount++
    xp.value += 1
    console.log(pillcount)
    if (pillcount > 15) {
      message.value = "Действие пилюли кончилось. К несчастью, сила не возвращается обратно."
      pillcount = 0
      jk0()
      stren = false
    } else if (curet) {
      jk0()
      countinf = 0
    }
  })
  goTown()
}
function mixPill() {
  gold.value = Math.floor(Math.random() * ((gold.value + 100) - 1 + 1)) + 1
  sanity.value = Math.floor(Math.random() * ((sanity.value + 100) - 1 + 1)) + 1
  xp.value = Math.floor(Math.random() * ((xp.value + 100) - 1 + 1)) + 1
  health.value = Math.floor(Math.random() * ((health.value + 100)  - 1 + 1)) + 1
  goTown()
  message.value = "Значения всех ваших ресурсов перемешались! Как эта пилюля смогла перемешать золото?"
}

let pillspack = ref([
  {name: 'Пилюля Джевила', //0
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: jevilPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала странная пилюля фиолетого-синего цвета. Примите ёё?'
  }, {name: 'Пилюля Ясновидения', //1
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: seeingPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля светло-зелёного цвета. Примите ёё?'
  }, {name: 'Пилюля Гниения', //2
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: rottingPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля тёмно-зелёного цвета. Примите ёё?'
  }, {name: 'Пилюля Цветения', //3
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: bloomingPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля розового цвета. Примите ёё?'
  }, {name: 'Пилюля Слабости', //4
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: weaknessPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля тёмно-синего цвета. Примите ёё?'
  }, {name: 'Пилюля Силы', //5
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: strengthPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля оранжевого цвета. Примите ёё?'
  }, {name: 'Пилюля Изменения', //6
    btn1: 'Принять', btn2: 'Уйти', btn3: 'N/A', btn4: 'N/A',
    fun1: mixPill, fun2: goTown, fun3: '', fun4: '',
    msgtext: 'Из автомата выпала пилюля оранжево-фиолетового цвета. Примите ёё?'
  }
])
function updatepills(bubble) {
  message.value = pillspack.value[bubble].msgtext
  btn1.value=pillspack.value[bubble].btn1
  btn2.value=pillspack.value[bubble].btn2
  btn3.value=pillspack.value[bubble].btn3
  btn4.value=pillspack.value[bubble].btn4
  change1.value=pillspack.value[bubble].fun1
  if (pillspack.value[bubble].fun2 !== '') {
    absent2.value = true;
    change2.value=pillspack.value[bubble].fun2
  } else {
    absent2.value = false;
  }
  if (pillspack.value[bubble].fun3 !== '') {
    absent3.value = true;
    change3.value=pillspack.value[bubble].fun3
  } else {
    absent3.value = false;
  }
  if (pillspack.value[bubble].fun4 !== '') {
    absent4.value = true;
    change4.value=pillspack.value[bubble].fun4
  } else {
    absent4.value = false;
  }
}
function buypill() {
  if(gold.value < 250) {
    message.value = "У вас недостаточно денег"
  } else {
    gold.value -= 250
    let pill = Math.floor(Math.random() * (6 - 0 + 1)) + 0
    updatepills(pill)
  }
}

function goEchoTown() {
  pillcount -= 30
  countc -= 30
  countinf -= 30
  firecount -= 30
  goTown()
  message.value = "Похоже, продолжительность действия эффектов на вас увеличилось (Даже если у вас их пока нет)."
}
////////////////////

let construc = ref('')
let construc2 = ref('')
let oksi = ref(false)

function construction() {
  if (gold.value >= 1000) {
    gold.value -= 1000
    oksi.value = true
  } else {
    message.value='У вас недостаточно средств'
  }
}

function construction1() {
  oksi.value = false
  let shitindex = getIDByFig(construc2.value)
  let roomgarba = getRoomByName(construc.value)
  xyzlift.value[shitindex].fun = roomgarba
  xyzlift.value[shitindex].vall = emoji[roomgarba]
}
////////////
let destruc = ref('')
let destruc2 = ref('')
let miron = ref(false)
let countd = 0
function destruction() {
  if (countd > 1) {
    message.value='Произошло нечто ужасное... вы больше не собираетесь нажимать на кнопку'
  } else {
    miron.value = true
  }
  countd++
}
function dispection(copro) {
  console.log(copro)
  let shitindexu = getIDByFig(copro) - 6
  let shitindexd = getIDByFig(copro) + 6
  let shitindexl = getIDByFig(copro) - 1
  let shitindexr = getIDByFig(copro) + 1
  let deadshige = getIDByFig(copro)
  console.log(roomLIndex.value)
  console.log(shitindexu)
  console.log(shitindexd)
  console.log(shitindexl)
  console.log(shitindexr)
  console.log(deadshige)
  console.log(roomLIndex.value)
  if (shitindexu !== null) {
    if (typeof xyzlift.value[shitindexu] !== 'undefined') {
      if (getIDByFun(roomLIndex.value) == shitindexu) {
      lose()
      }
      xyzlift.value[shitindexu].vall = "❌"
      xyzlift.value[shitindexu].fun = 41
    }
  }
  if (shitindexd !== null) {
    if (typeof xyzlift.value[shitindexd] !== 'undefined') {
      if (getIDByFun(roomLIndex.value) == shitindexd) {
        lose()
      }
      xyzlift.value[shitindexd].vall = "❌"
      xyzlift.value[shitindexd].fun = 41
    }
  }
  if (shitindexl !== null) {
    if (typeof xyzlift.value[shitindexl] !== 'undefined') {
      if (getIDByFun(roomLIndex.value) == shitindexl) {
        lose()
      }
      xyzlift.value[shitindexl].vall = "❌"
      xyzlift.value[shitindexl].fun = 41
    }
  }
  if ( shitindexr !== null) {
    if (typeof xyzlift.value[shitindexr] !== 'undefined') {
      if (getIDByFun(roomLIndex.value) === shitindexr) {
        lose()
      }
      xyzlift.value[shitindexr].vall = "❌"
      xyzlift.value[shitindexr].fun = 41
    }
  }
  xyzlift.value[deadshige].vall = "❌"
  if (deadshige !== null) {
    if (typeof xyzlift.value[deadshige] !== 'undefined') {
      if (getIDByFun(roomLIndex.value) == deadshige) {
        lose()
      }
      xyzlift.value[deadshige].vall = "❌"
      xyzlift.value[deadshige].fun = 41
    }
  }
}


function destruction1() {
  miron.value = false
  dispection(destruc.value)
}
////////////

function skipl() {
  goTown()
  if (gold.value >= 0) {
    gold.value -= 150
    message.value = "Деньги были потеряны"
  } else {
    health.value -= 150
    if (health.value <= 0) {
      lose()
    }
    message.value = "Зелёный гастер были потеряны"
  }
}

function zemlya() {
  goTown()
  if (health.value >= 0) {
    health.value -= 150
    message.value = "Ой, ой! Похоже в комнате была установлена ловушка! стрела больно ранила вас. В следующий раз будьте аккуратнее!"
  } else {
    lose()
  }
}
let scarpoji
let herko
function theft0(){
  if (gold.value >= 1500) {
    gold.value -= 1500
    impArts.push(herko)
    roomList.value[4].btn4 = ``
    roomList.value[4].fun4 = ``
    roomList.value[4].btn3 = `Уйти`
    roomList.value[4].fun3 = goTown
    update(roomLIndex.value)
  } else {
    message.value = 'Недостаточно денег'
  }
}
function theft() {
  goTown()
  if (impArts.length >= 1 && !(roomList.value[4].btn4 === `Уйти`)) {
    herko = impArts.pop()
    scarpoji = roomList.value[4]
    roomList.value[4].btn3 = `Купить ${herko}`
    roomList.value[4].fun3 = theft0
    roomList.value[4].btn4 = `Уйти`
    roomList.value[4].fun4 = goTown

  } else {
    message.value = "Хмммм, какая подозрительная коробка..."
  }
}

let hayloft = ref('')
let pickup = ref(false)
function daddygun1() {
  pickup.value = true
}

function daddygun() {
  pickup.value = false
  roomLIndex.value = getFunByFig(hayloft.value)
  let shitindexq = getIDByFig(hayloft.value)
  xyzlift.value[shitindexq].vall = emoji[roomLIndex.value]
  let pivo = hayloft.value.split('')
  console.log(pivo)
  rli.value = roomLetter.value.indexOf(pivo[0])
  rii.value = roomIndex.value.indexOf(Number(pivo[1]))
  update(roomList.value[roomLIndex.value]);
  //goTown()
}

function vip() {
  if (xp.value >= 300) {
    message.value = '"Вы действительно набрали достаточно опыта."'
    health.value += 500
    sanity.value += 500
    gold.value += 1500
    if (!(impArts.includes('Потёртая медаль'))) {
    impArts.push('Потёртая медаль')}
  } else {
    message.value = '"У вас недостаточно опыта"'
  }
}
let meif = false
let countinf = 0
let curet = false

function infection1() {
  infection()
  goTown()
}

function infection() {
  meif = true
  const jk0 = watch(roomLIndex, (newX) => {
      if (curet) {
        jk0()
        countinf = 0
      } else if (countinf === 25) {
        health.value -= 1000
        countinf = 0
      }
      countinf++
      console.log(countinf)
  })}

function cure() {
  curet = true
  meif = false
  health.value += 200
  sanity.value += 200
  message.value = "Вы были исцелены!"
  let rory = getIDByFun(22)
  xyzlift.value[rory].fun = 0
  xyzlift.value[rory].vall = ''
  curet = false
}


let roomL2Index = ref(0)
function update2() {
  message.value = utilityRooms.value[roomL2Index.value].msgtext
  btn1.value=utilityRooms.value[roomL2Index.value].btn1
  btn2.value=utilityRooms.value[roomL2Index.value].btn2
  btn3.value=utilityRooms.value[roomL2Index.value].btn3
  btn4.value=utilityRooms.value[roomL2Index.value].btn4
  change1.value=utilityRooms.value[roomL2Index.value].fun1
  if (utilityRooms.value[roomL2Index.value].fun2 !== '') {
    absent2.value = true;
    change2.value=utilityRooms.value[roomL2Index.value].fun2
  } else {
    absent2.value = false;
  }
  if (utilityRooms.value[roomL2Index.value].fun3 !== '') {
    absent3.value = true;
    change3.value=utilityRooms.value[roomL2Index.value].fun3
  } else {
    absent3.value = false;
  }
  if (utilityRooms.value[roomL2Index.value].fun4 !== '') {
    absent4.value = true;
    change4.value=utilityRooms.value[roomL2Index.value].fun4
  } else {
    absent4.value = false;
  }
}
let meh = false
let countc = 0
let del = false
function credit() {
    roomList.value[19].btn1 = "Отдать заём (3500)"
    roomList.value[19].fun1 = closecred
    update(roomList.value[19])
    message.value = 'На вашей руке появилась странная метка, что-то вроде клейма, в виде часов. Вы боитесь думать о том, что может произойти если вы не сможете управиться в срок.'
    gold.value += 3000
    meh = true
    const jk0 = watch(roomLIndex, (newX) => {
    countc++
      console.log(countc)
    if (countc === 35) {
      roomList.value[19].btn1 = "Взять заём"
      roomList.value[19].fun1 = credit
      update(roomList.value[19])
      meh = false
      message.value = "Вдруг вы чувствуете, как внутри что-то очень сильно болит. Похоже, что-то не так с вашими внутренними органами. В любом случае, сейчас у вас нет озсожности обратиться к врачу, так что вы продолжаете путь. Вы знали на что шли, когда брали тот кредит..."
      health.value -= 3000
      if (health.value <= 0) {
        lose()
      }
      countc = 0
      jk0()
    }
    if (del) {
      jk0()
      }
  })}
function closecred() {
 if (gold.value >= 3500) {
   del = true
   meh = false
   gold.value -= 3500
   message.value = '"Отлично. Займ закрыт. Приятно иметь с тобой дело."'
   roomList.value[19].btn1 = "Взять заём"
   roomList.value[19].fun1 = credit
 } else {
   message.value = '"Ты меня что, за дурака держишь? Этого недостаточно. Тебе нужно вернуть 2500!"'
 }
}


function archive1() {
 // coolimage.value = 'https://ia.svg.png'
  roomL2Index.value = 0
  update2(utilityRooms.value[roomL2Index.value])
}
function archive2() {
 // coolimage.value = 'htjpg'
  roomL2Index.value = 1
  update2(utilityRooms.value[roomL2Index.value])
}
function archive3() {
 // coolimage.value = 'httg'
  roomL2Index.value = 2
  update2(utilityRooms.value[roomL2Index.value])
}
function archive4() {
 // coolimage.value = 'ht3'
  roomL2Index.value = 3
  update2(utilityRooms.value[roomL2Index.value])
}
function archive5() {
//  coolimage.value = 'httpg'
  roomL2Index.value = 4
  update2(utilityRooms.value[roomL2Index.value])
}
function archive6() {
//  coolimage.value = 'h783.jpg'
  roomL2Index.value = 5
  update2(utilityRooms.value[roomL2Index.value])
}
function plsback() {
  roomLIndex.value = 10
  //coolimage.value = ''
  update(roomList.value[roomLIndex.value])
}
function plsback2() {
  roomLIndex.value = 11
  //coolimage.value = ''
  update(roomList.value[roomLIndex.value])
}

function exchange() {
  health.value -= 10;
  gold.value += 10;
  if (health.value <= 0) {
    roomLIndex.value = 2
    update(roomList.value[roomLIndex.value]);
  }
}
function exchangeXp() {
  xp.value += 1
  gold.value -= 10;
}
function doorw() {
  if (impArts.includes("placehold")) {

  }
}

function exchangeSan() {
  sanity.value -= 30;
  gold.value += 30;
  message.value = 'Ваше отражение искажается и вы видите нечто настолько ужасное, что чувствуете, как что-то скребётся внутри вашей головы. Тем не менее, из трубки в чашу выпало 30 монет. Продолжите ли вы смотреть в зеркало?'
  if (sanity.value <= 0) {
    roomLIndex.value = 2
    update(roomList.value[roomLIndex.value]);
  }
}
let psecount = 0
function pseudoTown() {
   psecount++
  if (psecount > 1) {
    message.value = "ТЫ НЕ МОЖЕШЬ УЙТИ"
   // greyPrank = true;
  }
}

function lose() {
  roomLIndex.value = 2
  update(roomList.value[roomLIndex.value])
}
function exchange1() {
  health.value -= 50;
  sanity.value += 50;
  if (health.value <= 0) {
    lose()
  }
  goTown()
}
function exchange2() {
  gold.value -= 50;
  health.value += 50;
  goTown()
}
function exchange3() {
  sanity.value -= 50;
  gold.value += 50;
  if (sanity.value <= 0) {
    lose()
  }
  goTown()
}
function exchange4() {
  health.value -= 50
  sanity.value += 50
  if (health.value <= 0) {
    lose()
  }
  goTown()
}
let change1 = ref(goUp)
let change2 = ref(goDown)
let change3 = ref(goLeft)
let change4 = ref(goRight)

</script>
<template>
  <div id="bogdan" :class="{ greyT: greyPrank, insaneP: insanePrank, jevilP: revolve}">
    <div id="app" :class="{jevilP: revolve}">
      <h1>Пронстранство No. 7</h1>
     <!-- <img v-bind:src="coolimage"> -->

      <div class="effect" v-if="meh"> <!--  -->
        <img src="https://avatars.mds.yandex.net/i?id=3ffc9d79d496ba49359500ff6d869a38_l-9098231-images-thumbs&n=13">
        <p>Часики тикают</p>
        <p>Тебе не удасться избежать уплаты. Ходов осталось: {{35 - countc}}</p>
      </div>
      <div id="inf" v-if="meif">
        <img src="https://i.pinimg.com/736x/69/82/83/698283241a50c84bcbf423f7102a5b93.jpg">
        <p>Инфекция</p>
        <p>Лучше что-нибудь сделать с этой инфекцией. Ходов до симптомов: {{25 - countinf}}</p>
      </div>
      <div id="jevPill" v-if="revolve">
        <img src="https://images.steamusercontent.com/ugc/811121990145819023/3B4FC3EC0A6DD1A6C94FA7585A9E54CEAC925274/?imw=512&amp;&amp;ima=fit&amp;impolicy=Letterbox&amp;imcolor=%23000000&amp;letterbox=false">
        <p>МИР ВРАЩАЕТСЯ!!!</p>
        <p>ХАОС, ХАОС! Ходов осталось: {{'♾/11'}}</p>
      </div>
      <div id="seePill" v-if="seer">
        <img src="https://avatars.mds.yandex.net/i?id=9ba2721d117b72bf68004ce80693b1474958ee58-12471321-images-thumbs&n=13">
        <p>Ясновидение</p>
        <p>Видите ли вы то, что вижу я? Ходов осталось: {{10 - pillcount}}</p>
      </div>
      <div id="illPill" v-if="illp">
        <img src="https://avatars.mds.yandex.net/i?id=0a10d6e00ef03aa6fb5f32d32d2bd41a9835b015-9181183-images-thumbs&n=13">
        <p>Гниение</p>
        <p>Боль разложения содрогает тело. Ходов осталось: {{10 - pillcount}}</p>
      </div>
      <div id="blooPill" v-if="bloo">
        <img src="https://avatars.mds.yandex.net/i?id=f7a68da3a0661cc95e661f6ba61a98e3_l-4576871-images-thumbs&n=13">
        <p>Цветение</p>
        <p>Тело и разум преображает весна. Ходов осталось: {{10 - pillcount}}</p>
      </div>
      <div id="weakPill" v-if="weak">
        <img src="https://avatars.mds.yandex.net/i?id=2085dccb4b0f938925631961c0851d132bb93a1e-12528722-images-thumbs&n=13">
        <p>Слабость</p>
        <p>Кажется, ваша сила куда-то уходит. Ходов осталось: {{15 - pillcount}}</p>
      </div>
      <div id="strenPill" v-if="stren">
        <img src="https://i.pinimg.com/736x/ad/c8/2e/adc82efe01a03b112c13add483cf784b.jpg">
        <p>Доппинг</p>
        <p>Кажется, сила прибывает!. Ходов осталось: {{15 - pillcount}}</p>
      </div>
      <div id="pain" v-if="firebool">
        <img src="https://avatars.mds.yandex.net/get-mpic/5218923/2a00000191dc984db8ac9f8ecd6095821434/9hq">
        <p>Горение</p>
        <p>Это будет больно. Ходов осталось: {{5 - firecount}}</p>
      </div>

      <div class="mstats" v-if="fightdisp">
        <p><span>{{ nameM }}</span> <strong>атакует!</strong></p>
        <p><strong>Здоровье противника:</strong> <span id="healthText">{{ healthM }}</span></p>
      </div>

      <div class="controls">
        <button @click="change1">{{btn1}}</button>
        <button @click="change2" v-if="absent2">{{btn2}}</button>
        <button @click="change3" v-if="absent3">{{btn3}}</button>
        <button @click="change4" v-if="absent4">{{btn4}}</button>
      </div>

      <!-- Сообщение -->
      <p id="text">{{ message }}</p>

      <!-- Статусы -->
      <div class="stats" :class="{ greyT: greyPrank, jevilP: revolve}">
        <p><strong>Рассудок:</strong> <span id="sanityText">{{ sanity }}</span></p>
        <p><strong>Здоровье:</strong> <span id="healthText">{{ health }}</span></p>
        <p><strong>Золото:</strong> <span id="goldText">{{ gold }}</span></p>
        <p><strong>Индекс комнаты:</strong> <span id="roomIndexText">{{ roomIndex[rii] }}{{ roomLetter[rli] }}</span></p>
        <p><strong>Комната:</strong> <span id="roomIndexText">{{ roomList[roomLIndex].name }}</span></p>
        <p><strong>Опыт:</strong> <span>{{ xp }}</span></p>
        <p><strong>Инвентарь:</strong> <span>Оружие: {{inventory}}; Ключи: {{impArts}}</span></p>
      </div>

      <div v-if="rori">
        <input v-model="balsalm" max="3000">
      </div>

      <legend for="popka" v-if="pickup">Индекс комнаты<input v-if="pickup" type="text" v-model="hayloft" id="popka" value="Индекс комнаты"></legend>
      <button v-if="pickup" v-on:click="daddygun">Сесть на поезд!</button>

      <legend for="om" v-if="oksi">Название комнаты:<input v-if="oksi" type="text" v-model="construc" id="om"></legend>
      <legend for="mo" v-if="oksi">Индекс комнаты:<input v-if="oksi" type="text" v-model="construc2" id="mo"></legend>
      <button v-if="oksi" v-on:click="construction1">Создать</button>

      <legend for="omo" v-if="miron">Индекс комнаты:<input v-if="miron" type="text" v-model="destruc" id="omo"></legend>
      <button v-if="miron" v-on:click="destruction1">Нажать</button>

      <table align="center">
        <thead>
        <tr>
          <th>A</th>
          <th>B</th>
          <th>C</th>
          <th>D</th>
          <th>E</th>
          <th>F</th>
          <th></th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A1")}}</th>
          <th>{{getVallByFig("B1")}}</th>
          <th>{{getVallByFig("C1")}}</th>
          <th>{{getVallByFig("D1")}}</th>
          <th>{{getVallByFig("E1")}}</th>
          <th>{{getVallByFig("F1")}}</th>
          <th>1</th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A2")}}</th>
          <th>{{getVallByFig("B2")}}</th>
          <th>{{getVallByFig("C2")}}</th>
          <th>{{getVallByFig("D2")}}</th>
          <th>{{getVallByFig("E2")}}</th>
          <th>{{getVallByFig("F2")}}</th>
          <th>2</th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A3")}}</th>
          <th>{{getVallByFig("B3")}}</th>
          <th>🚪</th>
          <th>{{getVallByFig("D3")}}</th>
          <th>{{getVallByFig("E3")}}</th>
          <th>{{getVallByFig("F3")}}</th>
          <th>3</th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A4")}}</th>
          <th>{{getVallByFig("B4")}}</th>
          <th>{{getVallByFig("C4")}}</th>
          <th>{{getVallByFig("D4")}}</th>
          <th>{{getVallByFig("E4")}}</th>
          <th>{{getVallByFig("F4")}}</th>
          <th>4</th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A5")}}</th>
          <th>{{getVallByFig("B5")}}</th>
          <th>{{getVallByFig("C5")}}</th>
          <th>{{getVallByFig("D5")}}</th>
          <th>{{getVallByFig("E5")}}</th>
          <th>{{getVallByFig("F5")}}</th>
          <th>5</th>
        </tr>
        </thead>
        <thead>
        <tr>
          <th>{{getVallByFig("A6")}}</th>
          <th>{{getVallByFig("B6")}}</th>
          <th>{{getVallByFig("C6")}}</th>
          <th>{{getVallByFig("D6")}}</th>
          <th>{{getVallByFig("E6")}}</th>
          <th>{{getVallByFig("F6")}}</th>
          <th>6</th>
        </tr>
        </thead>
      </table>
    </div>
  </div>
</template>
<style scoped>
img {
  width: 35%;
}
* {
  outline: 1px solid transparent; /* временно — чтобы увидеть, что вылезает */
}
html {
  width: 100%;
}
body {
  width: 100%;
  height: auto;
}

#bogdan {
  width: 100vw;
  background-color: #8a0000;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  box-sizing: border-box;
  position: relative;
}

h1 {
  font-family: "23 seconds to midnight";
  font-size: 3.5em;
}
.mstats {
  background-color: #270000;
  border: white 3px ridge;
}

#app {
  width: 100vw;
  background-color: #3c0000;
  color: #ffffff;
  text-align: center;
  justify-content: center;
  display: block;
  box-sizing: border-box;
  position: relative;
}

.controls,
.stats {
  border: 1px solid #0a0a23;
  color: #ffffff;
  background-color: #0a0a23;
  position: relative
}
.stats {
  border: 3px solid #ffffff;
}

#monsterStats {
  display: none;
  border: 1px solid #0a0a23;
  padding: 5px;
  color: #ffffff;
  background-color: #c70d0d;
}

#text {
  color: #ffffff;
}

.stats {
}

button {
  background-color: #bf0000;
}
@keyframes greyP {
  0% {
    background-color: black;
    transform: translateX(0);
  }
  50% {
    background-color: #500000;
    transform: translateX(-50px);
  }
  100% {
    background-color: #270000;
    transform: translateX(75px);
  }
}
@keyframes jevilP {
  0% {
    background-color: #26009c;
    transform: translateX(0);
  }
  25% {
    background-color: #400cef;
  }
  50% {
    background-color: #4e00ae;
    transform: translateX(-10px);
  }
  75% {
    background-color: #512dc8;
  }
  100% {
    background-color: #26009c;
    transform: translateX(10px);
  }
}
.jevilP {
  animation: jevilP 1s linear infinite;
}
@keyframes insaneP {
  0% {
    background-color: #500000;
    transform: translateX(0);
    font-family: "Arial Narrow";
  }
  25% {
    font-family: "Bookman Old Style";
  }
  50% {
    background-color: black;
    transform: translateX(-50px);
    font-family: "Arial Black";
  }
}
.insaneP {
  animation: insaneP 3s linear infinite;
}

.greyT {
  font-family: Phorssa;
  color: white;
  background-color: black;
  animation: greyP 3s linear infinite;
}
th {
  border: 2px solid white;

}
table {
  border: 2px solid white;
}
</style>