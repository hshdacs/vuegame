<template>
  <h1>Number Guessing</h1>
  <div>
    Difficulty
    &nbsp;
    <input v-model="difficulty" />
    &nbsp;
    <button @click="newGame">New Game</button>
    <hr>
    <div v-for="tip in tipps">
      <h3>{{ tip.number }}
        &nbsp;
      <Rating :rate="tip" />
      </h3>
    </div>
    <div>
      <input v-show="isGuessing"
        v-model="inputNumber"
        placeholder="Your tipp" 
        @keyup.enter="checkNumber()"
        />
      <h2 v-show="isWinning">Congratulation, you got it!</h2>
      <div v-show="isLoosing">
        <h2 >Well, bad luck, more next time.</h2>
        <h3>{{ secretNumber }}</h3>
      </div>
    </div>
  </div>
</template>

<script>
import Rating from "./components/Rating";

function createSecretNumber(schwierigkeitsgrad) {
  let geheimeZahl;
  const wertebereich = Math.pow(10, schwierigkeitsgrad);
  const startbereich = Math.pow(10, schwierigkeitsgrad-1);
  do {
    geheimeZahl = Math.floor(Math.random() * (wertebereich-startbereich) + startbereich);
  } while ( zahlEnthältDoppelteZiffern(geheimeZahl, schwierigkeitsgrad) );
  return geheimeZahl;
}

function zahlEnthältDoppelteZiffern(zahl, schwierigkeitsgrad) {
  let anzahlGefundenerZiffern = 0;
  for (let ziffer = 0; ziffer <= 9; ziffer++) {
    if (istZifferInZahl(ziffer,zahl))
        anzahlGefundenerZiffern++;
  }
  return anzahlGefundenerZiffern != schwierigkeitsgrad;
}

function getCorrectNumber(tipp, geheimeZahl) {
  let anzahlKorrekteZiffern = 0;
  while ( tipp > 0 ) {
      let zifferGeheimeZahl = geheimeZahl % 10;
      let zifferTipp = tipp % 10;
      if (zifferGeheimeZahl == zifferTipp) {
          anzahlKorrekteZiffern++;
      }
      geheimeZahl = Math.floor(geheimeZahl / 10);
      tipp = Math.floor(tipp / 10);
  }
  return anzahlKorrekteZiffern;
}

function getPresentNumber(tipp, geheimeZahl) {
  let vorhandeneZiffern = 0;
  while ( tipp > 0 ) {
      let ziffer = tipp % 10;
      if ( istZifferInZahl(ziffer, geheimeZahl) ) {
          vorhandeneZiffern++;
      }
      tipp = Math.floor(tipp / 10);
  }
  return vorhandeneZiffern;
}

function istZifferInZahl(ziffer, zahl) {
  while (zahl > 0) {
      let zifferDerZahl = zahl % 10;
      if (zifferDerZahl == ziffer) return true;
      zahl = Math.floor(zahl/10);
  }
  return false;
}

export default {
  name: 'App',
  components: {
    Rating
  },
  data() {
    return {
      isGuessing: true,
      isWinning: false,
      isLoosing: false,
      difficulty: 4,
      secretNumber: 0,
      inputNumber: "",
      tipps: []
    }
  },
  created() {
    this.secretNumber = createSecretNumber(this.difficulty);
  },
  methods: {
    newGame() {
      this.tipps = [];
      this.secretNumber = createSecretNumber(this.difficulty);
      this.isGuessing = true;
      this.isWinning = this.isLoosing = false;
    },
    checkNumber() {
        const number = Number(this.inputNumber);
        if (number === 0) {
          this.isLoosing = true;
          this.isGuessing = false;
          return;
        }
        const correct = getCorrectNumber(number, this.secretNumber);
        const present = getPresentNumber(number, this.secretNumber) - correct;
        const newtip = { number, correct, present };
        this.tipps = [...this.tipps, newtip];
        this.isWinning = (correct === this.difficulty);
        this.isGuessing = !this.isWinning;
    }
  }
}
</script>

<style>
</style>
