<template>
  <section class="hero is-fullheight">
    <div class="sticky-top">
      <div class="my-flex">
        <b-input
          v-model="inputPlayer"
          class="mb-1"
          placeholder="Nome..."
          size="is-large"
          icon="account"
          expanded
        >
        </b-input>

        <b-button
          type="is-info is-large"
          @click="addNewPlayer"
          @keyup.enter="addNewPlayer"
          >Novo jogador</b-button
        >
      </div>

      <div class="mt-2">
        <p class="has-text-weight-medium is-italic ml-4 my-2">Buy-in preço</p>

        <b-numberinput
          v-model="price"
          min="1"
          max="1000"
          size="is-large"
          type="is-success"
          rounded
          controls-alignment="right"
          controls-position="compact"
          controls-rounded
          expanded
        ></b-numberinput>
      </div>
    </div>

    <div class="hero-body is-flex is-justify-content-center is-flex-wrap-wrap ">
      <div
        v-for="(player, index) in currentPlayers"
        :key="index"
        class="is-flex is-flex-direction-column has-background-light p-4 m-1"
      >
        <div class="is-flex is-flex-direction-column">
          <div class="mr-1 is-flex">
            <p class="has-text-weight-semibold mr-1">Nome:</p>
            <span>{{ player.name }}</span>
          </div>

          <div class="mr-1 is-flex">
            <p class="has-text-weight-semibold mr-1">Quantidade de Buy-in:</p>
            <span> {{ player.buy_in }}</span>
          </div>

          <div class="mr-1 is-flex">
            <p class="has-text-weight-semibold mr-1">Saldo devedor:</p>
            <span> R${{ player.buy_in * price }},00</span>
          </div>
        </div>
        <div class="is-flex is-justify-content-space-between mt-1">
          <b-button
            size="is-medium is-success mr-1"
            expanded
            @click="addBuyIn(player.id)"
          >
            +
          </b-button>
          <b-button
            size="is-medium is-danger "
            expanded
            @click="removeBuyIn(player.id)"
          >
            -
          </b-button>
        </div>

        <!-- <b-button
            size="is-small is-danger my-4 "
            expanded
            @click="removeBuyIn(player.id)"
          >
           Remover jogador
          </b-button> -->
      </div>
    </div>

    <div class="sticky-bot">
      <span class="is-italic is-size-7"
        >Atenção! Isso apagará todos os dados da mesa.</span
      >
      <b-button type="is-danger" @click="newGame">Apagar mesa</b-button>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      inputPlayer: '',
      currentPlayers: [],
      price: 10,
    }
  },

  computed: {},

  watch: {
    price() {
      localStorage.setItem('poker_price', this.price)
    },
  },

  mounted() {
    const pokerParty = localStorage.getItem('poker_party')
    const pokerPrice = localStorage.getItem('poker_price')

    if (!pokerParty) {
      localStorage.setItem('poker_party', '')
    } else {
      this.currentPlayers = JSON.parse(localStorage.getItem('poker_party'))
    }

    if (!pokerPrice) {
      localStorage.setItem('poker_price', 10)
    } else {
      this.price = JSON.parse(localStorage.getItem('poker_price'))
    }
  },

  methods: {
    addNewPlayer() {
      if (!this.inputPlayer) {
        return
      }

      const newPlayer = {
        id: this.currentPlayers.length + 1,
        name: this.inputPlayer,
        buy_in: 1,
      }

      this.currentPlayers.push(newPlayer)

      this.inputPlayer = ''

      // Local storage
      localStorage.setItem('poker_party', JSON.stringify(this.currentPlayers))

      this.$buefy.toast.open({
        message: `Jogador ${newPlayer.name} adicionado!`,
        type: 'is-success',
      })
    },

    addBuyIn(playerId) {
      const id = this.currentPlayers.findIndex(
        (player) => player.id === playerId
      )
      this.currentPlayers[id].buy_in += 1

      // Local storage
      localStorage.setItem('poker_party', JSON.stringify(this.currentPlayers))
    },

    removeBuyIn(playerId) {
      const id = this.currentPlayers.findIndex(
        (player) => player.id === playerId
      )
      if (this.currentPlayers[id].buy_in > 0) {
        this.currentPlayers[id].buy_in -= 1
      }

      // Local storage
      localStorage.setItem('poker_party', JSON.stringify(this.currentPlayers))
    },

    newGame() {
      localStorage.setItem('poker_party', '')
      this.price = 10
      this.currentPlayers = []

      this.$buefy.toast.open({
        message: 'Mesa apagada.',
        type: 'is-danger',
      })
    },
  },
}
</script>


<style scoped>
.sticky-bot {
  position: sticky;
  width: 100%;
  left: 0px;
  bottom: 0px;
  display: flex;
  flex-direction: row;
  place-items: center;
  justify-content: space-around;
  padding: 1rem;
  height: 80px;
  gap: 10px;
  background: hsl(0, 0%, 96%);
}

.sticky-top {
  z-index: 10;
  position: sticky;
  width: 100%;
  left: 0px;
  top: 0px;
  height: auto;
  background: hsl(0, 0%, 96%);
  padding: 1rem;
}

.my-flex {
  display: flex;
  flex-direction: row;
  place-items: center;
  justify-content: space-around;
  gap: 10px;
}
</style>
