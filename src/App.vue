<template>
  <div id="app">
      <!-- Modal -->
      
      <div class="modal-wrapper" v-if="isModalActive">
          <div class="modal">
              <div class="cark-picker-row">
                  <div v-for="item in deck.cards.slice(0,4)" v-bind:key="item" v-bind:class="{ cardActive: isCardSelected(item) }" class="card-picker-item" @click="selectNumber(item)">{{item}}</div>
              </div>
              <div class="cark-picker-row">
                  <div v-for="item in deck.cards.slice(4,8)" v-bind:key="item" v-bind:class="{ cardActive: isCardSelected(item) }" class="card-picker-item" @click="selectNumber(item)">{{item}}</div>
              </div>
              <div class="cark-picker-row">
                  <div v-for="item in deck.cards.slice(8,12)" v-bind:key="item" v-bind:class="{ cardActive: isCardSelected(item) }" class="card-picker-item" @click="selectNumber(item)">{{item | filterTen}}</div>
              </div>
              <div class="cark-picker-row mt-2">
                  <div v-for="item in deck.signs" class="card-picker-item sign-item" v-bind:key="item" v-bind:class="{ redText: isRed(item), cardActive: isSignSelected(item) }" @click="selectSign(item)">{{item | getCardSymbol}}</div>
              </div>

              <div class="cark-picker-row mt-2 btn" v-bind:class="{ activateButton : !isSelectActive }" @click="setCard()">
                  <span>Select</span>
              </div>
              <div class="cark-picker-row btn btn-cancel" @click="closeModal()">
                  <span>Cancel</span>
              </div>
          </div>
      </div>

      <!-- First row of cards -->
      <div class="rowContainer top">

          <div v-for="item in data.players.slice(0,5)" v-bind:key="item.index" class="singleHand">

              <!-- If Player is active -->
              <div v-if="item.isActive" class="handContainer">
                  <div v-if="item.Card_1 != null" class="singleCard whiteBg noselect" v-bind:class="{ redText: isRed(item.Card_1[1]) }" @click="selectCard(item.index, 1)">
                      <span class="top_left_card">{{ item.Card_1[1] | getCardSymbol }}</span>
                      <span class="main_card_symbol">{{ item.Card_1[0] }}</span>
                      <span class="bottom_right_card">{{ item.Card_1[1] | getCardSymbol }}</span>
                  </div>

                  <div v-else class="singleCard noselect" @click="selectCard(item.index, 1)">
                      <div class="card-circle">+</div>
                  </div>

                  <div v-if="item.Card_2 != null" class="singleCard whiteBg noselect" v-bind:class="{ redText: isRed(item.Card_2[1]) }" @click="selectCard(item.index, 2)">
                      <span class="top_left_card">{{ item.Card_2[1] | getCardSymbol }}</span>
                      <span class="main_card_symbol">{{ item.Card_2[0] | filterTen }}</span>
                      <span class="bottom_right_card">{{ item.Card_2[1] | getCardSymbol }}</span>
                  </div>

                  <div v-else class="singleCard noselect" @click="selectCard(item.index, 2)">
                      <span class="card-circle">+</span>
                  </div>

              </div>
              <p class="wins" v-if="item.isActive && item.odds != null">Wins: {{item.odds}} <br>Tie: 0%</p>
              <!-- Inactive card -->
              <div v-if="!item.isActive" @click="addPlayer(item.index)" class="handContainer inactive">
                  <div class="circle">+</div>
              </div>
              <p v-if="!item.isActive" class="inactive_player">Add player</p>
          </div>

      </div>
      <!-- Board -->
      <div class="board">
          <div class="board-card-container">
              <!-- If card is active -->
              <div v-for="item in data.table.cards" v-bind:key="item.index" @click="selectTableCard(item.index)">
                  <div v-if="item.card != null" class="singleCard whiteBg noselect" v-bind:class="{ redText: isRed(item.card[1]) }">
                      <span class="top_left_card">{{ item.card[1] | getCardSymbol }}</span>
                      <span class="main_card_symbol">{{ item.card[0] | filterTen }}</span>
                      <span class="bottom_right_card">{{ item.card[1] | getCardSymbol }}</span>
                  </div>

                  <div v-else class="singleCard noselect" @click="selectTableCard(item.index)">
                      <div class="card-circle">+</div>
                  </div>
              </div>
          </div>
      </div>

      <!-- Second row of cards -->
      <div class="rowContainer">

          <div v-for="item in data.players.slice(5,10)" v-bind:key="item.index" class="singleHand">

              <!-- If Player is active -->
              <div v-if="item.isActive" class="handContainer">
                  <div v-if="item.Card_1 != null" class="singleCard whiteBg noselect" v-bind:class="{ redText: isRed(item.Card_1[1]) }" @click="selectCard(item.index, 1)">
                      <span class="top_left_card">{{ item.Card_1[1] | getCardSymbol }}</span>
                      <span class="main_card_symbol">{{ item.Card_1[0] | filterTen }}</span>
                      <span class="bottom_right_card">{{ item.Card_1[1] | getCardSymbol }}</span>
                  </div>

                  <div v-else class="singleCard noselect" @click="selectCard(item.index, 1)">
                      <div class="card-circle">+</div>
                  </div>

                  <div v-if="item.Card_2 != null" class="singleCard whiteBg noselect" v-bind:class="{ redText: isRed(item.Card_2[1]) }" @click="selectCard(item.index, 2)">
                      <span class="top_left_card">{{ item.Card_2[1] | getCardSymbol }}</span>
                      <span class="main_card_symbol">{{ item.Card_2[0] | filterTen }}</span>
                      <span class="bottom_right_card">{{ item.Card_2[1] | getCardSymbol }}</span>
                  </div>

                  <div v-else class="singleCard noselect" @click="selectCard(item.index, 2)">
                      <span class="card-circle">+</span>
                  </div>

              </div>
              <p class="wins" v-if="item.isActive && item.odds != null">Wins: {{item.odds}} <br>Tie: {{item.tie}}</p>
              <span v-if="item.isActive" class="remove-player noselect" @click="removePlayer(item.index)">-</span>


              <!-- Inactive card -->
              <div class="d-new-player">
                <div v-if="!item.isActive" @click="addPlayer(item.index)" class="handContainer inactive">
                    <div class="circle">+</div>
                </div>
                <p v-if="!item.isActive" class="inactive_player">Add player</p>
              </div>
              <div class="m-new-player">
                <span v-if="!item.isActive" class="add-label">Add new hand</span>
                <span v-if="!item.isActive" class="add-player noselect" @click="addPlayer(item.index)">+</span>
              </div>
          </div>
      </div>
  </div>
</template>

<script>
import {CardGroup, OddsCalculator} from 'poker-odds-calculator';

export default {
  name: 'App',
  filters: {
      getCardSymbol(value) {
          switch(value) {
              case 'h': {
                  return '♥'
              }
              case 's': {
                  return '♠'
              }
              case 'c': {
                  return '♣'
              }
              case 'd': {
                  return '♦'
              }
              default: {
                  return value
              }
          }
      },
      filterTen(value) {
          if(value == "T" || value == "t") {
              return '10'
          }
          return value
      }
  },
  computed: {
      isSelectActive() {
          if(this.selectedData.card == '' || this.selectedData.sign == '') {
              return false
          }
          return true
      }
  },
  methods: {
      isRed(card) {
          if (card.toLowerCase() == 'h' || card.toLowerCase() == 'd') {
              return true
          }
          return false
      },
      isCardSelected(item) {
          if(item == this.selectedData.card) {
              return true
          }
          return false
      },
      isSignSelected(sign) {
          if(sign == this.selectedData.sign) {
              return true
          }
          return false
      },
      selectCard(index, position) {
          //Open modal
          this.isModalActive = true
          //Set selectedCard index and position
          this.selectedCard.index = index
          this.selectedCard.position = position
      },
      selectTableCard(index) {
          //Open modal
          this.isModalActive = true
          this.isBoardModal = true
          //Set selectedCard index and position
          this.selectedBoardCardIndex = index
      },
      selectNumber(number) {
          this.selectedData.card = number
      },
      selectSign(sign) {
          this.selectedData.sign = sign
      },
      closeModal() {
          this.selectedData.card = ''
          this.selectedData.sign = ''
          this.isModalActive = false
      },
      calculateOdds () {
        let boardCardString = ''
        let boardCardCnt = 0;
        this.data.table.cards.forEach(card =>
        {
          if (card.card != null)
          {
            boardCardString += card.card.toLowerCase()
            boardCardCnt ++
          }
        })
        if (boardCardCnt == 0|| boardCardCnt == 3 || boardCardCnt == 4 || boardCardCnt == 5)
        {
          console.log(boardCardString)
          const board = CardGroup.fromString(boardCardString)
          const playerCards = []
          const oddPlayerIndexes = []
          let i;
          for (i = 0; i < this.data.players.length; i ++)
          {
            if (this.data.players[i].Card_1 == null || this.data.players[i].Card_2 == null)
            {
              this.data.players[i].odds = null
              continue
            }
            let playerCardString = this.data.players[i].Card_1.toLowerCase() + this.data.players[i].Card_2.toLowerCase()
            console.log(playerCardString)
            playerCards.push(CardGroup.fromString(playerCardString))
            oddPlayerIndexes.push(i)
          }
          
          const result = OddsCalculator.calculate(playerCards, board)
          console.log(result);
          for (i = 0; i < result.equities.length; i ++)
          {
            this.data.players[oddPlayerIndexes[i]].odds = result.equities[i].getEquity() + '%'
            this.data.players[oddPlayerIndexes[i]].tie = result.equities[i].getTiePercentage() + '%'
          }
        }
      }, 
      setCard() {
        //Check if card is already selected
        let temp_data = this.data
        for (let n in this.data.players) {
            //Check if there is a card
            if(this.data.players[n].isActive) {
                if(this.data.players[n].Card_1 == ((this.selectedData.card + this.selectedData.sign))) {
                    temp_data.players[this.data.players[n].index].Card_1 = null
                }
                if(this.data.players[n].Card_2 == ((this.selectedData.card + this.selectedData.sign))) {
                    temp_data.players[this.data.players[n].index].Card_2 = null
                }
            }
        }

        for (let n in this.data.table.cards) {
            //Check if there is a card
            //if(true) {
                if(this.data.table.cards[n].card == ((this.selectedData.card + this.selectedData.sign))) {
                    this.data.table.cards[n].card = null
                }
            //}
        }

        if(this.isBoardModal) {
            temp_data.table.cards[this.selectedBoardCardIndex].card = (this.selectedData.card + this.selectedData.sign)
            this.data = temp_data

            this.isModalActive = false
            this.isBoardModal = false

            this.selectedData.card = ''
            this.selectedData.sign = ''
        }
        else
        {
          //Set the selectedCard to the specified card
          if(this.selectedCard.position == 1) {
              temp_data.players[this.selectedCard.index].Card_1 = (this.selectedData.card + this.selectedData.sign)
          }

          if(this.selectedCard.position == 2) {
              temp_data.players[this.selectedCard.index].Card_2 = (this.selectedData.card + this.selectedData.sign)
          }

          this.data = temp_data
        }
        
        this.selectedData.card = ''
        this.selectedData.sign = ''

        this.isModalActive = false
        
        this.calculateOdds()
      },
      addPlayer(index) {
          //Set isActive of the index to true
          this.data.players[index].isActive = true
      },
      removePlayer(index) {
        this.data.players[index].Card_1 = null
        this.data.players[index].Card_2 = null
        this.data.players[index].odds = null
        this.data.players[index].tie = null
        this.data.players[index].isActive = false
      },
  },
  mounted() {
      document.addEventListener('keyup', (evt) => {
          if (evt.keyCode === 27) {
              this.closeModal()
          }
      });
  },
  beforeDestroy() {
      document.removeEventListener('keyup', (evt) => {
          if (evt.keyCode === 27) {
              this.closeModal()
          }
      });
  },
  data: function () {
      return {
          selectedData: {
              card: '',
              sign: ''
          },
          selectedCard: {
              index: 0,
              position: 1
          },
          selectedBoardCardIndex: 0,
          isModalActive: false,
          isBoardModal: false,
          deck: {
              cards: ["2", "3", "4", "5", "6", "7", "8", "9", "T", "A", "J", "Q", "K"],
              signs: ["h", "s", "d", "c"]
          },
          data: {
              "table": {
                  "hasCards": true,
                  "cards": [
                      {card: null, index: 0},
                      {card: null, index: 1},
                      {card: null, index: 2},
                      {card: null, index: 3},
                      {card: null, index: 4}
                  ]
              },
              /* 
                how to fill players data manually
                desktop: index 0-9
                mobile: index 5-9 
                *Note
                In mobile, calculate data from index 5 to 9(players[5]~players[9]), so have to fill null data from index 0 to 4(players[0]~players[4])
              */
              "players": [  
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "tie": null,
                      "isActive": false,
                      "index": 0
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 1
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 2
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 3
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 4
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 5
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 6
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 7
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 8
                  },
                  {
                      "Card_1": null,
                      "Card_2": null,
                      "odds": null,
                      "isActive": false,
                      "index": 9
                  }
              ]
          }
      }
  }
}
</script>

<style>
    #app {
        width: 100%;
        max-width: 1000px;
        margin-top: 20px;
        margin-left: auto;
        margin-right: auto;
        background: #FDD119;
        font-family: 'News Cycle', sans-serif;
        display: flex;
        flex-direction: column;
    }

    .rowContainer {
        padding: 20px 80px;
        display: flex;
        align-items: top;
        justify-content: space-between;
    }

    .singleHand {
        width: 140px;
        text-align: center;
    }

    .handContainer {
        height: 85px;
        display: flex;
        justify-content: space-between;
    }

    .inactive {
        background: rgba(0,0,0,0.2);
    }

    .singleCard {
        text-align: center;
        background: #004E7F;
        width: 63px;
        height: 85px;
        position: relative;
        cursor: pointer;
        border: 1px solid rgba(64, 122, 157, 0.37);
    }

    .top_left_card, .bottom_right_card {
        position: absolute;
        font-family: 'Segoe UI';
        font-size: 25px;
        line-height: 28px;
    }

    .top_left_card {
        left: 5px;
        top: 2px;
    }

    .bottom_right_card {
        bottom: 2px;
        right: 5px;
    }

    .main_card_symbol {
        position: absolute;
        left: 0;
        right: 0;
        top: 17px;
        font-size: 30px;
        font-weight: bold;
    }

    .whiteBg {
        background: white;
    }

    .redText {
        color : red;
    }

    .circle {
        background: #fff;
        width: 46px;
        height: 46px;
        border-radius: 100px;
        text-align: center;
        line-height: 42px;
        font-size: 40px;
        margin: auto;
        color: #707070;
    }

    .card-circle {
        background: #fff;
        width: 20px;
        height: 20px;
        border-radius: 100px;
        display: inline-block;
        line-height: 19px;
        margin-top: 32px;
    }

    .wins {
        text-align: left;
        font-weight: bold;
        font-size: 17px;
        line-height: 19px;
        margin-top: 10px;
        color: #005B1B;
    }

    .inactive_player {
        font-family: 'News Cycle';
        font-size: 15px;
        font-weight: bold;
        line-height: 18px;
    }

    .board {
        width: 509px;
        height:243px;
        border-radius: 122px;
        margin: 0 auto;
        background: rgb(0,127,38);
        background: -moz-radial-gradient(circle, rgba(0,127,38,1) 0%, rgba(0,64,19,1) 100%);
        background: -webkit-radial-gradient(circle, rgba(0,127,38,1) 0%, rgba(0,64,19,1) 100%);
        background: radial-gradient(circle, rgba(0,127,38,1) 0%, rgba(0,64,19,1) 100%);
        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#007f26",endColorstr="#004013",GradientType=1); 
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .board-card-container {
        width: 85%;
        display: flex;
        justify-content: space-between;
    }

    .noselect {
        -webkit-touch-callout: none; /* iOS Safari */
            -webkit-user-select: none; /* Safari */
            -khtml-user-select: none; /* Konqueror HTML */
            -moz-user-select: none; /* Old versions of Firefox */
                -ms-user-select: none; /* Internet Explorer/Edge */
                    user-select: none; /* Non-prefixed version, currently
                                        supported by Chrome, Opera and Firefox */
    }

    .modal-wrapper {
        position: absolute;
        top:0;
        left:0;
        width: 100vw;
        height: 100vh;
        background: rgba(0,0,0,0.5);
        z-index: 100;
        text-align: center;
        -webkit-backdrop-filter: blur(10px);
        backdrop-filter: blur(10px);
    }

    .mt-2 {
        margin-top: 10px;
    }

    .modal {
        width: 90%;
        max-width: 381px;
        height: auto;
        margin: 0 auto;
        margin-top: 40px;
    }

    .cark-picker-row {
        width: 100%;
        height: auto;
        display: flex;
    }

    .card-picker-item {
        height: 72px;
        background: white;
        line-height: 72px;
        flex-grow: 1;
        flex-basis: 0;
        font-weight: bold;
    }

    .card-picker-item:hover {
        background: #FFE576;
        cursor: pointer;
    }

    .cark-picker-row span {
        margin: 0 auto;
    }

    .cardActive {
        background: #FDD119;
    }

    .btn {
        text-align: center;
        background: #FDD119;
        padding: 10px 0;
        cursor: pointer;
    }

    .sign-item {
        font-size: 40px;
    }

    .handContainer.inactive {
        cursor: pointer;
    }

    .activateButton {
        pointer-events: none;
        background: #BFBFBF;
        color: white;
        cursor: not-allowed !important;
    }

    .remove-player {
      display: None;
    }

    .m-new-player {
      display: None;
    }
    
    @media only screen and (max-width: 768px) {
      .rowContainer {
        display: flex;
        flex-direction: column;
        padding-left: 20px;
        padding-right: 20px;
      }

      .top {
        display: None;
      }

      .board {
        margin-top: 50px;
        width: 357px;
        height: 157px;
      }

      .singleCard {
        width: 54px;
        height: 70px;
      }

      .handContainer {
        width: 120px;
      }

      .top_left_card, .bottom_right_card {
          position: absolute;
          font-family: 'Segoe UI';
          font-size: 20px;
          line-height: 23px;
      }

      .main_card_symbol {
          position: absolute;
          left: 0;
          right: 0;
          top: 15px;
          font-size: 24px;
          font-weight: bold;
      }

      .card-circle {
        margin-top: 25px;
      }

      .singleHand {
        display: flex;
        width: 100%;
      }

      .inactive {
        display: None;
      }

      .inactive_player {
        display: None;
      }

      .wins {
        margin-top: 18px;
        margin-left: 10px;
      }

      .remove-player {
          background: #ff0000;
          opacity: 46%;
          width: 32px;
          height: 32px;
          border-radius: 100px;
          text-align: center;
          line-height: 22px;
          font-size: 40px;
          float: right;
          color: #FFFFFF;
          margin-top: 19px;
          position: absolute;
          right: 35px;
          display: block;
      }

      .m-new-player {
        display: flex;
        height: 50px;
      }

      .d-new-player {
        display: None;
      }

      .add-label {
        text-align: left;
        font-weight: bold;
        font-size: 17px;
        line-height: 25px;
        margin-top: 10px;
        color: #005B1B;
      }

      .add-player {
          background: #00ff33;
          opacity: 46%;
          width: 32px;
          height: 32px;
          border-radius: 100px;
          text-align: center;
          line-height: 28px;
          font-size: 40px;
          float: right;
          color: #FFFFFF;
          position: absolute;
          right: 35px;
          display: block;
          margin-top: 10px;
      }
    }
</style>
