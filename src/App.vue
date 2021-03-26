<template>
  <div id="app">
    <div class="game_wrapper">
      <div v-if="!gameOver" class="game_title title_title">
        Simon The Game
      </div>

      <div v-else class="game_title title_game-over">
        Game Over :-((
      </div>

      <div class="game_field">
        <div
          class="game_field-item item_1"
          @click.prevent="itemPressed(elements.blue)"
          :class="{ activeClass: elements.blue.isActive }"
        ></div>
        <div
          class="game_field-item item_2"
          @click.prevent="itemPressed(elements.red)"
          :class="{ activeClass: elements.red.isActive }"
        ></div>
        <div
          class="game_field-item item_3"
          @click.prevent="itemPressed(elements.yellow)"
          :class="{ activeClass: elements.yellow.isActive }"
        ></div>
        <div
          class="game_field-item item_4"
          @click.prevent="itemPressed(elements.green)"
          :class="{ activeClass: elements.green.isActive }"
        ></div>
      </div>
      <div class="game_info">
        <div class="game_info_item info_item-title">
          Round: <span>{{ roundCounter }}</span>
        </div>
        <div class="game_info_item " @click="startGame">
          <button class="info_item-btn">Start</button>
        </div>
        <div class="game_info_item">
          <div class="game_info_item-level">
            <input type="radio" value="easy" v-model="difficultyLevel" />
            <label>Easy</label>
          </div>
          <div class="game_info_item-level">
            <input type="radio" value="medium" v-model="difficultyLevel" />
            <label>Medium</label>
          </div>
          <div class="game_info_item-level">
            <input type="radio" value="hard" v-model="difficultyLevel" />
            <label>Hard</label>
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
          sound: "1",
          isActive: false,
        },
        red: {
          id: 2,
          sound: "2",
          isActive: false,
        },
        yellow: {
          id: 3,
          sound: "3",
          isActive: false,
        },
        green: {
          id: 4,
          sound: "4",
          isActive: false,
        },
      },
      difficultyLevel: "easy",
      lavelInterval: 1500,
      randomSelected: [], // выбранные рандомно элементы
      itemsSelected: [], // выбранные пользователем элементы
      roundCounter: 1, // счетчик раундов
      gameOver: false,
    };
  },
  methods: {
    // подсветка элемента и озвучивание элемента
    elementHighlight(e) {
      e.isActive = true;
      this.sounding(e.sound); // озвучивание
      setTimeout(function() {
        e.isActive = false;
      }, 300);
    },

    // подсвечивает выбранный пользователем элемент по клику и добавляет его id в масиив элементов выбранных пользователем
    itemPressed(elem) {
      this.elementHighlight(elem);
      this.itemsSelected.push(elem.id);
      // console.log(this.itemsSelected);
    },
    // рекурсивный запуск ф-ию game с интервалом времени в зависимости от сложности
    recGame(interval) {
      setTimeout(() => {
        this.game();
        return;
      }, interval);
    },
    // озучивает клик
    sounding(src) {
      let audio = new Audio(require(`../src/assets/sounds/sound_${src}.mp3`));
      audio.play();
    },

    // формирует массив рандомных значений и сравнивает со значениями введенными пользователем
    game() {
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
        this.recGame(this.lavelInterval);
      } else {
        // если достаточно, то дает пользлвателю 3сек + по 0.5сек закажды раунд для повтора
        setTimeout(() => {
          // выводит оба масссива в консоль
          console.log(JSON.stringify(this.randomSelected));
          console.log(JSON.stringify(this.itemsSelected));
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
            this.recGame(this.lavelInterval);
          } else {
            // если пользователь не успел или ошибся (массивы не идентичны)
            console.log("ошибка"); // то ошибка
            this.randomSelected = [];
            this.itemsSelected = [];
            this.roundCounter = 1; // и обновленеи счетчика
            this.gameOver = true;
            return;
          }
        }, 3000 + 500 * this.roundCounter);
      }
    },

    // запускает игру по клику
    startGame() {
      this.gameOver = false;
      // устанавливает уроверь сложности
      switch (this.difficultyLevel) {
        case "easy":
          this.lavelInterval = 1500;
          break;
        case "medium":
          this.lavelInterval = 1000;
          break;
        case "hard":
          this.lavelInterval = 400;
          break;
      }
      console.log(this.lavelInterval);

      this.game();
    },
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}
.game_title {
  text-align: center;
  margin-bottom: 15px;

  font-size: 25px;
  font-weight: 700;

  width: 250px;
  margin-top: 10px;
  border: 1px solid;
  border-radius: 8px;
}
.title_title {
  background-color: rgba(197, 228, 87, 0.836);
}
.title_game-over {
  background-color: rgb(228, 95, 122);
}

.game_wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.game_field {
  width: 300px;
  height: 300px;
  border-radius: 50%;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
}
.game_field-item {
  cursor: pointer;
}

.item_1 {
  background-color: rgb(71, 71, 216);
  border-top-left-radius: 100%;
  opacity: 0.6;
}
.item_2 {
  background-color: #f01c1c;
  border-top-right-radius: 100%;
  opacity: 0.6;
}
.item_3 {
  background-color: #f3f313;
  border-bottom-left-radius: 100%;
  opacity: 0.6;
}
.item_4 {
  background-color: #41e70f;
  border-bottom-right-radius: 100%;
  opacity: 0.6;
}
.activeClass {
  border: 2px solid #0d0e0d;
  opacity: 1;
}
.game_info {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.game_info_item {
  padding-top: 8px;
}
.info_item-title {
  font-size: 18px;
  font-weight: 700;
}
.info_item-title span {
  color: rgb(238, 54, 91);
  font-size: 25px;
}
.info_item-btn {
  background-color: rgb(228, 95, 122);
  height: 30px;
  width: 80px;
  border: 1px solid;
  border-radius: 8px;
  font-weight: 700;
  color: beige;
}
.info_item-btn:hover {
  cursor: pointer;
  background-color: rgb(238, 54, 91);
}
.info_item-btn:focus {
  outline: none;
}
.game_info_item-level {
  padding-top: 3px;
  font-weight: 500;
}
.game_info_item-level label {
  padding-left: 9px;
}
</style>
