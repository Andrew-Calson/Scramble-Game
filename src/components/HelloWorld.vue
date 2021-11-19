<template>
  <div class="hello">
    <h1>Welcome to Scramble</h1>
    <div class="row">
      <h1>{{ game.points }}</h1>
      <h1>{{ game.strikes }}</h1>
    </div>
    <div class="row">
      <h6>POINTS</h6>
      <h6>STRIKES</h6>
    </div>
    <div
      :style="{
        color: status.color,
        backgroundColor: status.bgColor,
        border: status.borderColor,
      }"
      class="msg-box"
    >
      {{ status.message }}
    </div>
    <h2>{{ game.suffledWord }}</h2>
    <input
      :disabled="!this.game.active"
      type="text"
      id="text_box"
      @keypress.enter="checkWord"
    />
    <div>
      <button :disabled="!this.game.active" @click="passWord" id="pass_btn">
        <span
          ><b>{{ passLimit }}</b></span
        >
        Passes Remaining
      </button>
      <br />
      <button id="replay_btn" v-if="!this.game.active" @click="playAgain">
        Play, Again?
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      words: [
        "penguin",
        "magazine",
        "gun",
        "paper",
        "picture",
        "friend",
        "work",
        "money",
        "call",
        "ball",
        "taco",
        "family",
        "flip",
        "wooden",
        "pinch",
        "speed",
        "original",
        "security",
        "hand",
        "explode",
      ],
      passLimit: 3,
      strikeLimit: 3,
      status: {
        message: "",
        color: "",
        bgColor: "",
        borderColor: "",
      },
      guessedWord: "",
      game: {
        active: true,
        points: 0,
        strikes: 0,
        index: 0,
        suffledWord: "",
        currentList: [],
        originWord: "",
      },
    };
  },
  mounted() {
    this.initWords();
  },
  methods: {
    initWords() {
      const newWords = [];
      const duplicateWords = [...this.words];

      for (let i = 0; i < this.words.length; i++) {
        const randomIndex = Math.floor(Math.random() * duplicateWords.length);
        newWords.push(duplicateWords[randomIndex]);
        duplicateWords.splice(randomIndex, 1);
      }

      this.words = newWords;
      this.game.suffledWord = this.suffle(this.words[0]);
      this.game.originWord = this.words[0];
    },
    suffle(src) {
      const copy = [...src];

      const length = copy.length;
      for (let i = 0; i < length; i++) {
        const x = copy[i];
        const y = Math.floor(Math.random() * length);
        const z = copy[y];
        copy[i] = z;
        copy[y] = x;
      }

      if (typeof src === "string") {
        return copy.join("").toUpperCase();
      }

      return copy;
    },
    checkWord(e) {
      const inputVal = e.target.value;
      if (inputVal === "") {
        this.status.message = "Please guess what this word is!";
        this.status.color = "#a94442";
        this.status.bgColor = "#f2dede";
        this.status.borderColor = "#ebccd1";
        return;
      }

      for (let i = 0; i < this.words.length; i++) {
        if (this.game.currentList.length === this.words.length) {
          this.status.message = "You win.";
          this.status.color = "#3c763d";
          this.status.bgColor = "#dff0d8";
          this.status.borderColor = "#d6e9c6";
          this.game.active = false;
          return;
        }
        if (inputVal.toLowerCase() === this.game.originWord) {
          this.status.message = "Correct. Next word.";
          this.status.color = "#3c763d";
          this.status.bgColor = "#dff0d8";
          this.status.borderColor = "#d6e9c6";
          this.game.points++;
          this.game.index++;
          if (this.game.index > this.words.length - 1) {
            this.status.message = "You win.";
            this.status.color = "#3c763d";
            this.status.bgColor = "#dff0d8";
            this.status.borderColor = "#d6e9c6";
            this.game.active = false;
            return;
          } else {
            this.game.suffledWord = this.suffle(this.words[this.game.index]);
            this.game.originWord = this.words[this.game.index];
            this.game.currentList.push(this.game.originWord);
            document.getElementById("text_box").value = "";
            return;
          }
        } else {
          document.getElementById("text_box").value = "";
          this.status.message = "Wrong. Try again.";
          this.status.color = "#a94442";
          this.status.bgColor = "#f2dede";
          this.status.borderColor = "#ebccd1";
          this.game.strikes++;

          if (this.game.strikes >= this.strikeLimit) {
            this.status.message = "You lost.";
            this.status.color = "#a94442";
            this.status.bgColor = "#f2dede";
            this.status.borderColor = "#ebccd1";
            this.game.active = false;
            return;
          }
          return;
        }
      }
    },
    playAgain() {
      this.status.message = "";
      this.status.color = "";
      this.status.bgColor = "";
      this.status.borderColor = "";
      this.guessedWord = "";
      this.passLimit = 3;
      this.game.active = true;
      this.game.points = 0;
      this.game.strikes = 0;
      this.game.index = 0;
      this.game.suffledWord = "";
      this.game.currentList = [];
      this.game.originWord = "";
      this.initWords();
    },

    passWord() {
      if (this.passLimit === 0) return;
      this.passLimit--;
      this.game.index++;
      this.status.message = "You passed. Next word.";
      this.status.color = "#31708f";
      this.status.bgColor = "#d9edf7";
      this.status.borderColor = "#bce8f1";
      this.game.suffledWord = this.suffle(this.words[this.game.index]);
      this.game.originWord = this.words[this.game.index];
      this.game.currentList.push(this.words[this.game.index - 1]);
    },
  },
};
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#pass_btn {
  background-color: #c9302c;
  color: white;
  height: 25px;
  border-radius: 4px;
  margin-bottom: 10px;
  border-color: #ac2925;
}
#replay_btn {
  background-color: #286090;
  color: white;
  height: 25px;
  border-radius: 4px;
  border-color: #204d74;
}
input {
  width: 50%;
  height: 30px;
  margin-bottom: 10px;
}
span {
  background-color: white;
  margin: 2px;
  font-size: 10px;
  padding: 3px;
  color: black;
  border-radius: 2px;
}
.hello {
  margin: 10% 25%;
  width: 50%;
}
.row {
  display: flex;
}
.row > h1 {
  margin: 0 24%;
}
.row > h6 {
  font-size: 8px;
  margin: 0.5% 23%;
}
.msg-box {
  margin: 2% 25%;
  width: 50%;
  height: 28px;
  padding: 3px;
  vertical-align: middle;
}
</style>
