<template>
  <section class="section">
    <div class="container">
      <div class="fixed header">
        <i class="material-icons">account_circle</i>
        <span>PINDER</span>
        <i class="material-icons">chat_bubble_outline</i>
      </div>
      <div
          v-if="current"
          class="fixed fixed--center"
          style="z-index: 3"
          :class="{ 'transition': isVisible }">
        <Vue2InteractDraggable
            v-if="isVisible"
            :interact-out-of-sight-x-coordinate="500"
            :interact-max-rotation="15"
            :interact-x-threshold="200"
            :interact-y-threshold="200"
            :interact-event-bus-events="interactEventBus"
            interact-block-drag-down
            @draggedRight="emitAndNext('match')"
            @draggedLeft="emitAndNext('reject')"
            @draggedUp="emitAndNext('skip')"
            class="rounded-borders card card--one">
          <div style="height: 100%">
            <img
                :src="current.src"
                class="rounded-borders"/>
            <div class="text">
              <h2>{{ current.name }}, <span>{{ current.age }}</span></h2>
            </div>
          </div>
        </Vue2InteractDraggable>
      </div>
      <div
          v-if="next"
          class="rounded-borders card card--two fixed fixed--center"
          style="z-index: 2">
        <div style="height: 100%">
          <img
              :src="next.src"
              class="rounded-borders"/>
          <div class="text">
            <h2>{{ next.name }}, <span>{{ next.age }}</span></h2>
          </div>
        </div>
      </div>
      <div
          v-if="index + 2 < cards.length"
          class="rounded-borders card card--four fixed fixed--center"
          style="z-index: 1">
        <div style="height: 100%">
        </div>
      </div>
      <div class="footer fixed">
        <v-btn
            class="mx-2"
            fab
            large
            @click="index = 0"
        >
          <v-icon color="amber darken-1" large>
            mdi-reload
          </v-icon>
        </v-btn>
        <v-btn
            class="mx-2"
            fab
            large
            color="white"
            @click="reject"
        >
          <v-icon color="pink lighten-1" large>
            mdi-close
          </v-icon>
        </v-btn>
        <v-btn
            class="mx-2"
            fab
            large
            color="white"
            @click="skip"
        >
          <v-icon color="purple lighten-2" large>
            mdi-star
          </v-icon>
        </v-btn>
        <v-btn
            class="mx-2"
            fab
            large
            @click="match"
        >
          <v-icon color="pink lighten-2" large>
            mdi-heart
          </v-icon>
        </v-btn>
        <v-btn
            class="mx-2"
            fab
            large
        >
          <v-icon color="purple darken-2" large>
            mdi-flash
          </v-icon>
        </v-btn>
      </div>
    </div>
  </section>
</template>
<script>
import {Vue2InteractDraggable, InteractEventBus} from 'vue2-interact'
import axios from 'axios';

const EVENTS = {
  MATCH: 'match',
  SKIP: 'skip',
  REJECT: 'reject'
}
export default {
  name: 'swipeCards',
  components: {Vue2InteractDraggable},
  data() {
    return {
      isVisible: true,
      index: 0,
      interactEventBus: {
        draggedRight: EVENTS.MATCH,
        draggedLeft: EVENTS.REJECT,
        draggedUp: EVENTS.SKIP
      },
      cards: []
    }
  },
  computed: {
    current() {
      return this.cards[this.index]
    },
    next() {
      return this.cards[this.index + 1]
    }
  },
  created() {
    this.getPeople()
  },
  methods: {
    match() {
      InteractEventBus.$emit(EVENTS.MATCH)
    },
    reject() {
      InteractEventBus.$emit(EVENTS.REJECT)
    },
    skip() {
      InteractEventBus.$emit(EVENTS.SKIP)
    },
    emitAndNext(event) {
      this.$emit(event, this.index)
      setTimeout(() => this.isVisible = false, 200)
      setTimeout(() => {
        this.index++
        this.isVisible = true
      }, 200)
    },
    getPeople() {
      axios.get(`https://randomuser.me/api/?results=50`)
          .then(response => {
            let peoples = response.data.results;
            peoples.forEach(people => {
              let obj = {};
              obj.name = people.name.first +" "+ people.name.last;
              obj.src = people.picture.large;
              obj.age = people.dob.age;
              this.cards.push(obj);
            })

          })
          .catch(e => {
            this.errors.push(e)
          })
    },
  }
}
</script>

<style lang="scss" scoped>
.header {
  width: 100%;
  height: 60vh;
  z-index: 0;
  top: 0;
  left: 0;
  color: #f953c6;
  text-align: center;
  font-style: italic;
  font-family: 'Engagement', cursive;
  display: flex;
  justify-content: space-between;

  span {
    display: block;
    font-size: 4rem;
    padding-top: 2rem;
    text-shadow: 1px 1px 1px red;
  }

  i {
    padding: 24px;
  }
}

.footer {
  width: 77vw;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  padding-bottom: 30px;
  justify-content: space-around;
  align-items: center;
}

.btn {
  position: relative;
  width: 50px;
  height: 50px;
  padding: .2rem;
  border-radius: 50%;
  background-color: white;
  box-shadow: 0 6px 6px -3px rgba(0, 0, 0, 0.02), 0 10px 14px 1px rgba(0, 0, 0, 0.02), 0 4px 18px 3px rgba(0, 0, 0, 0.02);
  cursor: pointer;
  transition: all .3s ease;
  user-select: none;
  -webkit-tap-highlight-color: transparent;

  &:active {
    transform: translateY(4px);
  }

  i {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    &::before {
      content: '';
    }
  }
}

.flex {
  display: flex;

  &--center {
    align-items: center;
    justify-content: center;
  }
}

.fixed {
  position: fixed;

  &--center {
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
}

.rounded-borders {
  border-radius: 12px;
}

.card {
  width: 80vw;
  height: 60vh;
  color: white;

  img {
    object-fit: cover;
    display: block;
    width: 100%;
    height: 100%;
  }

  &--one {
    box-shadow: 0 1px 3px rgba(0, 0, 0, .2), 0 1px 1px rgba(0, 0, 0, .14), 0 2px 1px -1px rgba(0, 0, 0, .12);
  }

  &--two {
    transform: translate(-48%, -48%);
    box-shadow: 0 6px 6px -3px rgba(0, 0, 0, .2), 0 10px 14px 1px rgba(0, 0, 0, .14), 0 4px 18px 3px rgba(0, 0, 0, .12);
  }

  &--three {
    background: rgba(black, .8);
    transform: translate(-46%, -46%);
    box-shadow: 0 10px 13px -6px rgba(0, 0, 0, .2), 0 20px 31px 3px rgba(0, 0, 0, .14), 0 8px 38px 7px rgba(0, 0, 0, .12);
  }

  .text {
    position: absolute;
    bottom: 0;
    width: 100%;
    background: black;
    background: rgba(0, 0, 0, 0.5);
    border-bottom-right-radius: 12px;
    border-bottom-left-radius: 12px;
    text-indent: 20px;

    span {
      font-weight: normal;
    }
  }
}

.transition {
  animation: appear 200ms ease-in;
}

@keyframes appear {
  from {
    transform: translate(-48%, -48%);
  }
  to {
    transform: translate(-50%, -50%);
  }
}
</style>
