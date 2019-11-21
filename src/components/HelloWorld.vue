<template>
  <div class="hello">
    <section class="container">
      <div class="questionBox">
        <transition
          :duration="{ enter: 500, leave: 300 }"
          enter-active-class="animated zoomIn"
          leave-active-class="animated zoomOut"
          mode="out-in"
        >
          <div
            class="questionContainer"
            v-if="questionIndex<quiz.questions.length"
            v-bind:key="questionIndex"
          >
            <header>
              <h1 class="title is-6">Encuesta</h1>
              <div class="progressContainer">
                <progress
                  class="progress is-info is-small"
                  :value="(questionIndex/quiz.questions.length)*100"
                  max="100"
                >{{(questionIndex/quiz.questions.length)*100}}%</progress>
                <p>{{((questionIndex/quiz.questions.length)*100).toFixed(2)}}% progreso</p>
              </div>
            </header>

            <h2 class="titleContainer title">{{ quiz.questions[questionIndex].text }}</h2>
            <div class="optionContainer">
              <div
                class="option"
                v-for="(response, index) in quiz.questions[questionIndex].responses"
                @click="selectOption(index)"
                :class="{ 'is-selected': userResponses[questionIndex] == index}"
                :key="index"
              ><span class="badge badge-info pull-left">{{ index | charIndex }}.</span> {{ response.text }} <i class="fas fa-check"></i></div>
            </div>
            <footer class="questionFooter">
              <nav class="pagination" role="navigation" aria-label="pagination">
                <div class="container col-12">
                  <div class="row">
                    <div class="col-4"><b-button @click="prev()" :disabled="questionIndex < 1">Anterior</b-button></div>
                    <div class="col-4"><b-button size="sm" @click="toggle">{{ show ? 'Cerrar' : '¿Por qué la pregunta?' }}</b-button></div>
                    <div class="col-4">
                      <b-button :class="(userResponses[questionIndex]!==null)?'is-active':''"
                              :disabled="questionIndex>=quiz.questions.length"
                              @click="next()">{{ (userResponses[questionIndex]!==null)?'Siguiente':'Omitir' }}</b-button>
                    </div>
                  </div>
                  <div class="row">
                    <b-alert
                      v-model="show"
                      class="mt-4 col-12"
                      dismissible
                      @dismissed="dismissed"
                    >
                      Aquí la descripción de la pregunta si es que hay alguna...!
                    </b-alert>
                  </div>
                </div>
              </nav>
            </footer>
          </div>
          <div
            v-if="questionIndex >= quiz.questions.length"
            v-bind:key="questionIndex"
            class="quizCompleted has-text-centered">
            <span class="icon">
              <!-- <i class="fa" :class="score()>0?'fa-check-circle-o is-active':'fa-times-circle'"></i> -->
            </span>
            <h2 class="title">Se redirige al landing: {{finalLandingPage}}</h2>
            <br>
            <a class="button" @click="restart()">
              restart
              <i class="fa fa-refresh"></i>
            </a>
          </div>
        </transition>
      </div>
    </section>
  </div>
</template>

<script>
import Vue from 'vue'
var quiz = {
    user: "Norberto",
    questions: [
      {
        text: "Eres hombre o mujer?",
        responses: [
          { text: "Soy Hombre" },
          { text: "Soy Mujer" },
          { text: "Prefiero no decir" }
        ]
      },
      {
        text:"¿Qué edad tienes?",
        responses:[
          {text:"15 - 20 años"},
          {text:"21 - 25 años"},
          {text:"26 - 30 años"},
          {text:"31 - 35 años"},
          {text:"36 - 40 años"},
          {text:"41 - 45 años"},
        ]
      },
      {
        text:"¿Estas casado(a)?",
        responses:[
          {text:"Sí, estoy casada(o)"},
          {text:"Estoy soltera(o)"},
        ]
      },
      {
        text:"¿Vives en Apodaca?",
        responses:[
          {text:"Sí"},
          {text:"No"},
        ]
      },
      {
        text: "¿Sabes que significa HTTP?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿un documento HTML empieza y cierra con la etiqueta HTML?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿El cuerpo de un HTML usa con la etiqueta body?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿sabes como usar OutLook?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿Te sientes familiarizado con el termino Search Engine Opt.?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿El domain \".com\" se refiere a comida?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿Usas linux a diario? ",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿Tu mobile es Android?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿Sabes lo que significa HTML?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      },
      {
        text: "¿Te sientes comoda(o) con el buscador Yahoo?",
        responses: [
          { text: "si", value:1 },
          { text: "No", value: 2 },
          { text: "A veces", value:0 },
        ]
      }
    ]
  },
  userResponseSkelaton = Array(quiz.questions.length).fill(null);
export default {
  name: 'Encuesta',
  props: {
    msg: String
  },
  data: function() {
    return {
      quiz: quiz,
      questionIndex: 0,
      userResponses: userResponseSkelaton,
      isActive: false,
      show: false,
      finalLandingPage:null
    };
  },
  filters: {
    charIndex: function(i) {
      return String.fromCharCode(97 + i);
    }
  },
  methods: {
    toggle() {
      console.log('Toggle button clicked')
      this.show = !this.show
    },
    dismissed() {
      console.log('Alert dismissed')
    },
    restart: function() {
      this.questionIndex = 0;
      this.userResponses = Array(this.quiz.questions.length).fill(null);
    },
    selectOption: function(index) {
      Vue.set(this.userResponses, this.questionIndex, index);
    },
    next: function() {
      console.log(this.quiz.questions.length);
      if (this.questionIndex < this.quiz.questions.length) this.questionIndex++;
    },
    prev: function() {
      if (this.quiz.questions.length > 0) this.questionIndex--;
    },
    score: function() {
      var resultados = new Array;
      for(let i in this.userResponses){
        resultados.push((undefined !== this.quiz.questions[i].responses[this.userResponses[i]].value)?this.quiz.questions[i].responses[this.userResponses[i]].value:'');
      }
      this.finalLandingPage = (resultados.includes( 1 ))?1:(resultados.includes( 2 ))? 2 : 0;
    }
  },
  watch: {
    show(newVal) {
      console.log('Alert is now ' + (newVal ? 'visible' : 'hidden'))
    }
  },
  created: function(){

  }
}
</script>
<style lang="scss" scoped>
$trans_duration: 0.3s;
$primary_color: #4281f8;

@import url("https://fonts.googleapis.com/css?family=Montserrat:400,400i,700");
@import url("https://fonts.googleapis.com/css?family=Open+Sans:400,400i,700");

body {
  font-family: "Open Sans", sans-serif;
  font-size: 14px;

  height: 100vh;

  background: #CFD8DC;

  /* mocking native UI */
  cursor: default !important; /* remove text selection cursor */
  user-select: none; /* remove text selection */
  user-drag: none; /* disbale element dragging */

  display: flex;
  align-items: center;
  justify-content: center;
}

.button {
  transition: $trans_duration;
}
.title,
.subtitle {
  font-family: Montserrat, sans-serif;
  font-weight: normal;
  color: gray;
}
.animated {
  transition-duration: $trans_duration/2;
}

.container {
  margin: 0 auto;
}

.questionBox {
  max-width: 30rem;
  width: 30rem;
  min-height: 30rem;

  background: #FAFAFA;
  position: relative;
  display: flex;

  border-radius: 0.5rem;
  overflow: hidden;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.19), 0 6px 6px rgba(0, 0, 0, 0.23);
  margin: 20px auto;

  header {
    background: rgba(0, 0, 0, 0.025);
    padding: 1.5rem;
    text-align: center;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);

    h1 {
      font-weight: bold;
      margin-bottom: 1rem !important;
    }
    .progressContainer {
      width: 60%;
      margin: 0 auto;
      > progress {
        width: 100%;
        margin: 0;
        border-radius: 5rem;
        overflow: hidden;
        border: none;

        color: $primary_color;
        &::-moz-progress-bar {
          background: $primary_color;
        }
        &::-webkit-progress-value {
          background: $primary_color;
        }
      }
      > p {
        margin: 0;
        margin-top: 0.5rem;
      }
    }
  }
  .titleContainer {
    text-align: center;
    margin: 0 auto;
    padding: 1.5rem;
  }

  .quizForm {
    display: block;
    white-space: normal;

    height: 100%;
    width: 100%;

    .quizFormContainer {
      height: 100%;
      margin: 15px 18px;

      .field-label {
        text-align: left;
        margin-bottom: 0.5rem;
      }
    }
  }
  .quizCompleted {
    width: 100%;
    padding: 1rem;
    text-align: center;

    > .icon {
      color: #FF5252;
      font-size: 5rem;

      .is-active {
        color: #00E676;
      }
    }
  }
  .questionContainer {
    white-space: normal;

    height: 100%;
    width: 100%;

    .optionContainer {
      margin-top: 12px;
      flex-grow: 1;
      .option {
        border-radius: 290486px;
        padding: 9px 18px;
        margin: 0 18px;
        margin-bottom: 12px;
        transition: $trans_duration;
        cursor: pointer;
        background-color: rgba(0, 0, 0, 0.05);
        color: rgba(0, 0, 0, 0.85);
        border: transparent 1px solid;

        svg{
          display: none;
        }

        &.is-selected {
          border-color: rgba(black, 0.25);
          background-color: white;
          position: relative;
          svg{
            display: block;
            position: absolute;
            right: 12px;
            top: 13px;
          }
        }
        &:hover {
          background-color: rgba(0, 0, 0, 0.1);
        }
        &:active {
          transform: scaleX(0.9);
        }

        span{
          position: relative;
          float: left;
          top: 3px;
        }
      }
    }
    footer{
      background: rgba(0, 0, 0, 0.025);
      border-top: 1px solid rgba(0, 0, 0, 0.1);

      padding: 15px 25px;
      width: 100%;
      align-self: flex-end;

      .questionFooter {
        background: rgba(0, 0, 0, 0.025);
        border-top: 1px solid rgba(0, 0, 0, 0.1);
        width: 100%;
        align-self: flex-end;
      }
    }
  }
}
.pagination {
  display: flex;
  justify-content: space-between;
}
.button {
  padding: 0.5rem 1rem;
  border: 1px solid rgba(0, 0, 0, 0.25);
  border-radius: 5rem;
  margin: 0 0.25rem;

  transition: 0.3s;

  &:hover {
    cursor: pointer;
    background: #ECEFF1;
    border-color: rgba(0, 0, 0, 0.25);
  }
  &.is-active {
    background: $primary_color;
    color: white;
    border-color: transparent;

    &:hover {
      background: darken($primary_color, 10%);
    }
  }
}

@media screen and (min-width: 769px) {
  .questionBox {
    align-items: center;
    justify-content: center;

    .questionContainer {
      display: flex;
      flex-direction: column;
    }
  }
}

@media screen and (max-width: 768px) {
  .sidebar {
    height: auto !important;
    border-radius: 6px 6px 0px 0px;
  }
}
</style>
