<template>
  <div>
    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount"/>
    <h1 
      v-html="this.question">
    </h1>
    <template v-if="this.question">
      <template v-for="(answer, index) in this.answers" v-bind:key="index">
        <input 
          :disabled="this.asnwerSunmitted"
          type="radio" 
          name="options" 
          :value="answer"
          v-model="this.chosenAnswer"
        >
        <label v-html="answer"></label><br>
      </template>
      <button v-if="!this.asnwerSunmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>

      <section v-if="this.asnwerSunmitted" class="result">
        <h4 v-if="this.chosenAnswer == this.correctAnswer"
          v-html="'&#9989; Parabéns, Resposta Correta.'">
        </h4>
        <h4 v-else
         v-html="'&#10060; Desculpa, Resposta Errada. Resposta Correta: ' + this.correctAnswer">
         
        </h4>
        <button class="send" @click="this.getNewQuestion()" type="button">Next Question</button>
      </section>
    </template>
  </div>
</template>


<script>
import ScoreBoard from '@/components/ScoreBoard.vue'


export default {
  name: 'App',
  components:{
    ScoreBoard,
  },
  data(){  
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      asnwerSunmitted: false,
      winCount: 0,
      loseCount: 0,
    }
  },

  computed:{
    answers(){
      let answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;  
    }
  },

  methods:{
    submitAnswer(){
      if(!this.chosenAnswer){
        alert('Escolha uma das opções')
      } else {
        this.asnwerSunmitted = true
        if (this.chosenAnswer === this.correctAnswer){
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    
    getNewQuestion() {
      this.asnwerSunmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;
      
      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
        
      })
    }
  },

  created(){
    this.getNewQuestion()
  }
}

//https://opentdb.com/api.php?amount=1&category=18&difficulty=easy

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio]{
    margin: 12px 4px;
  }

  button.send{
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: #1867c0;
    cursor: pointer;
  }
}
</style>
