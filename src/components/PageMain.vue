<template>
  <div class="container jumbotron p-4" style="background-color: #BDD8DA">
    <h1 class="display-4">Speed Typing Attempt</h1>
    <p class="lead mt-4">Test how fast you can use the keyboard</p>

    <hr class="my-4" />
    <div v-if="isFinished" class="alert alert-primary">
    <h3>Time is Over</h3>
    <p class="display-4">{{ dkn }} DKN</p>
    <span>Accuracy Percentage : %{{ percentCorrect }}</span><br>
    <span>Correct Word : {{ trueCount }}</span><br>
    <span>Wrong Word : {{ falseCount }}</span><br>
    <button @click="newGame" class="btn btn-success btn-lg btn-block mt-5" >Try Again</button>
</div>
     <div v-else>
    <div  class="card">
      <div class="card-body">
        <span
          v-for="(word, key) in words.filter((data,index)=>index<20)"
          :key="key"
          v-bind:class="key != 0 || activeWordControl"
          class="words ml-2"
          >{{ word }}</span
        >
      </div>
    </div>
    <div class="card bg-#DFEFF0">
      <div class="card-body bg-secondary">
        <div class="input-group input-group-lg">
          <input type="text" class="form-control" v-model="writingWord" />
          <div class="input-group-append input-group-lg" id="button-addon4">
            <button class="btn btn-light" disabled type="button">{{ time }} sec</button>
            <button :disabled="isStarted" class="btn btn-light" type="button"  @click="getWords">Refresh</button>
          </div>
        </div>
      </div>
    </div>
</div>
  </div>
</template>
<script>

import wordList from '@/assets/words.json'
export default {
  data () {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      time: 30,
      interval: false,
      isStarted: false,
      isFinished: false,
      wordList: wordList
    }
  },
  watch: {
    writingWord (value) {
      if (!value || value === ' ') {
        this.writingWord = ''
        return
      }

      if (!this.isStarted) this.timer()
      const word = this.words[0].slice(0, value.length).toUpperCase()
      const userWord = value.replace(' ', '').toUpperCase()
      this.isTrue = word === userWord

      if (value.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writingWord = ''
        this.isTrue = true
      }
    }
  },
  computed: {
    activeWordControl () {
      return this.isTrue ? 'active-Word' : 'active-Word bg-danger'
    },
    dkn () {
      return 400 - this.words.length
    },
    percentCorrect () {
      const percent = (100 / this.dkn)
      const value = (percent * this.trueCount)
      return isNaN(value) ? 0 : value
    }
  },
  mounted () {
    this.getWords()
  },

  methods: {
    newGame () {
      this.getWords()
      this.isFinished = false
      this.time = 30
      this.isTrue = true
      this.isStarted = false
    },

    getWords () {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 400)
    },

    timer () {
      this.isStarted = true
      this.interval = setInterval(this.timeProcess, 1000)
    },
    timeProcess () {
      if (this.time === 0) {
        clearInterval(this.interval)
        this.isFinished = true
        return
      }
      this.time--
    }
  }
}
</script>
<style>
.words {
  font-size: 25px;
  font-weight: 400;
  margin-left: 10px;
}
.active-Word {
  background-color: lavender;
  color: gray;
  padding: 5px;
  border-radius: 6px;
}
</style>
