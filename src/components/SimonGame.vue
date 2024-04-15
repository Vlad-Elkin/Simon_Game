<template>
  <div class="simon-game">
    <button @click="startGame">Начать</button>
    <br>
    <label for="difficulty">Выберите сложность:</label>
    <select id="difficulty" v-model="selectedInterval">
      <option v-for="option in difficultyOptions" :value="option.value" :key="option.index">
        {{ option.text }}
      </option>
    </select>
    <div class="board">
      <div v-for="(color,index) in colors" :key="index"
      :class="['color',color,{'active':activeColor===index}]"
      @click="handleColorClick(index)"></div>
    </div>
    <p v-if="gameOver">Game Over! Попробуйте снова.</p>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'HelloWorld',
  data(){ 
    return{
      gameOver:false,
      sequence: [],
      playerSequence: [],

      selectedInterval:1500,
      difficultyOptions:ref([
        {index:0,text:'Легкий',value: 1500},
        {index:1,text:'Средний',value: 1000},
        {index:2,text:'Сложный  ',value: 400}
      ]),

      activeColor:null,
      colors: ['green', 'red', 'yellow', 'blue'],
    }
  },
  methods:{
    startGame(){
        this.gameOver = false;
        this.sequence = [];
        this.addStep();
      },
      addStep(){
        this.sequence.push(Math.floor(Math.random()*4));
        this.showSequence();
      },
      showSequence(){
        let index = 0;
        const flash = setInterval(()=>{
          this.activeButton(this.sequence[index]);
          index++;
          if (index>=this.sequence.length){
            clearInterval(flash);
            this.playerSequence = [];
          }
        },this.selectedInterval)
      },
      activeButton(index){
        this.activeColor = index;
        const audio = new Audio(require('../assets/audio/'+this.colors[index]+'.mp3'))
        audio.play();
        setTimeout(()=>{
          this.activeColor = null
        },this.selectedInterval/2)
      },
      handleColorClick(index){
        if (!this.gameOver){
          this.playerSequence.push(index);
          this.activeButton(index);
          if (this.playerSequence[this.playerSequence.length-1] !== this.sequence[this.playerSequence.length-1]){
            this.gameOver = true;
          } else if (this.playerSequence.length === this.sequence.length){
            this.addStep();
          }
        }
      }
  }
}
</script>

<style>
.board{
  display: grid;
  justify-content: center;
  grid-template-areas: "a b" "c d";
}
.color{
  width: 100px;
  height: 100px;
  margin: 10px;}
.color:not(.active){
  filter: brightness(50%);
}
.red{background-color: red;}
.blue{background-color: blue;}
.green{background-color: green;}
.yellow{background-color: yellow;}
</style>