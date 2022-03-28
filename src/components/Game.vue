<template>
    <div class="main-block">
        <div class="submain-block" v-if="isLose===false">
            <button id="0" :class="{black: isSelected[0]}" class="button-complete green"
                    v-on:click="onColorButtonClick(0)"></button>
            <button id="1" :class="{black: isSelected[1]}" class="button-complete yellow"
                    v-on:click="onColorButtonClick(1)"></button>
            <br>
            <button id="2" :class="{black: isSelected[2]}" class="button-complete orange"
                    v-on:click="onColorButtonClick(2)"></button>
            <button id="3" :class="{black: isSelected[3]}" class="button-complete red"
                    v-on:click="onColorButtonClick(3)"></button>
        </div>
        <div>
            <div class="round">Раунд: {{round}}</div>
            <button class="button-complete button-start" v-on:click="newGame()">Начать</button>
            <br><br>
            <div>Уровень сложности</div>
            <select v-model="timer">
                <option value=1500>Легкий</option>
                <option value=1000>Средний</option>
                <option value=400>Сложный</option>
            </select>
            <div v-if="isLose">Вы проиграли!</div>
        </div>
    </div>
</template>

<script>
    import sound0 from '../assets/sounds/1.mp3';
    import sound1 from '../assets/sounds/2.mp3';
    import sound2 from '../assets/sounds/3.mp3';
    import sound3 from '../assets/sounds/4.mp3';

    export default {
        name: "Game",
        data() {
            return {
                round: 0,
                sequenceOfClicksRule: [],
                sequenceOfClicksPlayer: [],
                isSelected: [false, false, false, false],
                isLose: null,
                timer: 1500,
                audio: [new Audio(), new Audio(), new Audio(), new Audio()],
                updateHook: 0,
            }
        },
        methods: {
            newGame() {
                this.round = 0;
                this.isLose = false;
                this.audio.forEach(item => item.pause());
                this.sequenceOfClicksRule = [];
                this.sequenceOfClicksPlayer = [];
                this.audio[0].src = sound0;
                this.audio[1].src = sound1;
                this.audio[2].src = sound2;
                this.audio[3].src = sound3;
                for (let i=0; i<this.isSelected.length; i++) {
                    this.isSelected[i] = false;
                }
                this.newRound();
            },
            async newRound() {
                this.round++;
                this.sequenceOfClicksPlayer = [];
                this.sequenceOfClicksRule.push(Math.floor((Math.random() * 4)));

                for (let i = 0; i < this.sequenceOfClicksRule.length; i++) {
                    this.isSelected[this.sequenceOfClicksRule[i]] = true;
                    this.$forceUpdate();
                    this.audio[this.sequenceOfClicksRule[i]].play();
                    setTimeout(() => {
                        this.isSelected[this.sequenceOfClicksRule[i]] = false;
                        this.$forceUpdate();
                    }, 200);
                    //await new Promise((resolve) => setTimeout(resolve, 200));
                    //console.log('isSelected: ' + this.isSelected);
                    await new Promise((resolve) => setTimeout(resolve, this.timer));
                    //console.log('isSelected: ' + this.isSelected);
                }
                //console.log('sequenceOfClicks: ' + this.sequenceOfClicksRule);
            },
            async onColorButtonClick(id) {
                this.audio[id].play();
                this.sequenceOfClicksPlayer.push(id);
                //console.log(this.sequenceOfClicksPlayer);
                for (let i = 0; i < this.sequenceOfClicksPlayer.length;) {
                    if (this.sequenceOfClicksRule[i] !== this.sequenceOfClicksPlayer[i]) {
                        this.isLose = true;
                        this.audio.forEach(item => item.pause());
                    } else {
                        if (this.sequenceOfClicksRule.length === this.sequenceOfClicksPlayer.length) {
                            await new Promise((resolve) => setTimeout(resolve, 500));
                            this.newRound();
                        }
                    }
                    i++;
                }
            },
        }
    }
</script>

<style lang="scss" scoped>
    .main-block {
        display: inline-flex;
    }

    .submain-block {
        margin-left: auto;
        margin-right: auto;
        width: 500px;
    }

    .button-complete {
        position: relative;
        border: none;
        font-size: 28px;
        color: #FFFFFF;
        padding: 20px;
        width: 200px;
        height: 200px;
        border-radius: 5px;
        text-align: center;
        transition-duration: 0.4s;
        text-decoration: none;
        overflow: hidden;
        cursor: pointer;
    }

    .button-complete:after {
        content: "";
        background: #f1f1f1;
        display: block;
        position: absolute;
        padding-top: 300%;
        padding-left: 350%;
        margin-left: -20px !important;
        margin-top: -120%;
        opacity: 0;
        transition: all 0.8s;
    }

    .button-complete:active:after {
        padding: 0;
        margin: 0;
        opacity: 1;
        transition: 0s
    }

    .green {
        background-color: green;
    }

    .yellow {
        background-color: yellow;
    }

    .orange {
        background-color: orange;
    }

    .red {
        background-color: red;
    }

    .black {
        background-color: black !important;
    }



    .round {
        font-size: 40px;
        font-weight: bold;
    }

    .button-start {
        background-color: blue;
        margin-top: 30px;
        width: 170px;
        height: 70px;
    }
</style>