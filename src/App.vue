<template>
  <div id="app">
    <h1 class="game_title">Simon The Game</h1>
    <div class="container">
      <div class="game__content">
        <div
          class="game__item game__item_1"
          @click.prevent="itemPressed(elements.blue)"
          :class="{ activeClass: elements.blue.isActive }"
        ></div>
        <div
          class="game__item game__item_2"
          @click.prevent="itemPressed(elements.red)"
          :class="{ activeClass: elements.red.isActive }"
        ></div>
        <div
          class="game__item game__item_3"
          @click.prevent="itemPressed(elements.yellow)"
          :class="{ activeClass: elements.yellow.isActive }"
        ></div>
        <div
          class="game__item game__item_4"
          @click.prevent="itemPressed(elements.green)"
          :class="{ activeClass: elements.green.isActive }"
        ></div>
      </div>
      <div>
        <div>Раунд: {{ roundCounter }}</div>
        <div @click="startGame"><button>Start</button></div>
        <div>
          <div>
            <input type="radio" value="easy" />
            <label>Легкий</label>
          </div>

          <div>
            <input type="radio" value="normal" />
            <label>Нормальный</label>
          </div>
          <div>
            <input type="radio" value="hard" />
            <label>Сложный</label>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: "App",
  components: {},
  data() {
    return {
      // привязать элементы к переменным в которых будет содержаться идентификатор элемента и звук
      elements: {
        blue: {
          id: 1,
          sound: "",
          isActive: false,
        },
        red: {
          id: 2,
          sound: "",
          isActive: false,
        },
        yellow: {
          id: 3,
          sound: "",
          isActive: false,
        },
        green: {
          id: 4,
          sound: "",
          isActive: false,
        },
      },
      randomSelected: [], // выбранные рандомно элементы
      itemsSelected: [], // выбранные пользователем элементы
      roundCounter: 1, // счетчик раундов
    };
  },
  methods: {
    // подсветка элемента
    elementHighlight(e) {
      e.isActive = true;
      setTimeout(function() {
        e.isActive = false;
      }, 300);
    },

    // подсвечивает выбранный элемент по клику и добавляет его id в масиив элементов выбранных пользователем
    itemPressed(elem) {
      this.elementHighlight(elem);
      this.itemsSelected.push(elem.id);
      // console.log(this.itemsSelected);
    },
    // рекурсивный запуск ф-и randomElems с интервалом времени в зависимости от сложности
    recRandomElems(interval) {
      setTimeout(() => {
        this.randomElems();
        return;
      }, interval);
    },

    // формирует массив рандомных значений и сравнивает со значениями введенными пользователем
    randomElems() {
      let randomElem;
      // генерирует рандомный элемент
      let randomNum = Math.floor(
        Math.random() * (Math.floor(5) - Math.ceil(1)) + Math.ceil(1)
      );
      // console.log(randomNum);

      switch (randomNum) {
        case 1:
          randomElem = this.elements.blue;
          break;
        case 2:
          randomElem = this.elements.red;
          break;
        case 3:
          randomElem = this.elements.yellow;
          break;
        case 4:
          randomElem = this.elements.green;
          break;
      }
      // console.log(randomElem);
      // подсвечивает рандомный элемент
      this.elementHighlight(randomElem);
      //пушит его в массив рандомных элементов
      this.randomSelected.push(randomElem.id);
      // console.log(this.randomSelected);

      // проверяет достаточно ли элементов в массиве исходя из раунда
      if (this.randomSelected.length < this.roundCounter) {
        // если нет, то запускает ф-ю рекурсивно
        this.recRandomElems(1500);
      } else {
        // если достаточно, то дает пользлвателю 3сек + по 0.5сек закажды раунд для повтора
        setTimeout(() => {
          // выводит оба масссива в консоль
          console.log(this.randomSelected);
          console.log(this.itemsSelected);
          if (
            // если введенные пользоватлем значения совпадают с рандомными
            JSON.stringify(this.randomSelected) ===
            JSON.stringify(this.itemsSelected)
          ) {
            // то массивы очищаются
            this.randomSelected = [];
            this.itemsSelected = [];
            // раунд увеличивается
            this.roundCounter++;
            // ф-я запускается рекурсивно
            this.recRandomElems(1500);
          } else {
            // если пользователь не успел или ошибся (массивы не идентичны)
            console.log("ошибка"); // то ошибка
            this.randomSelected = [];
            this.itemsSelected = [];
            this.roundCounter = 1; // и обновленеи счетчика
            return;
          }
        }, 3000 + 500 * this.roundCounter);
      }
    },

    // запускает игру по клику
    startGame() {
      this.randomElems();
    },
  },
};
</script>

<style>
.game_title {
  text-align: center;
}
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.game__content {
  width: 300px;
  height: 300px;
  border-radius: 50%;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
}
.game__item {
  cursor: pointer;
}

.game__item_1 {
  background-color: rgb(71, 71, 216);
  border-top-left-radius: 100%;
  opacity: 0.6;
}
.game__item_2 {
  background-color: #f01c1c;
  border-top-right-radius: 100%;
  opacity: 0.6;
}
.game__item_3 {
  background-color: #f3f313;
  border-bottom-left-radius: 100%;
  opacity: 0.6;
}
.game__item_4 {
  background-color: #41e70f;
  border-bottom-right-radius: 100%;
  opacity: 0.6;
}
.activeClass {
  border: 2px solid #0d0e0d;
  opacity: 1;
}
</style>
