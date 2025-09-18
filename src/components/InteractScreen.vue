<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-memmory
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="(el) => setCardRef(el, index)"
        :cardsContext="cardsContext"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, value: card }"
        :rules="rules"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>

<script>
import Card from "./Card.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardMemmory: Card,
  },
  data() {
    return {
      rules: [],
      cardRefs: {},
    };
  },
  methods: {
    setCardRef(el, index) {
      if (el) {
        this.cardRefs[`card-${index}`] = el;
      }
    },
    checkRule(card) {
      console.log(card);
      if (this.rules.length === 2) return false;
      // if (this.rules.length === 1 && this.rules[0].index != card.index) {
      //   this.rules.push(card);
      // } else return false;
      this.rules.push(card);
      if (this.rules.length === 1) {
        this.cardRefs[`card-${this.rules[0].index}`].onEnabledDisabledMode();
      }
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("Right...");
        // this.cardRefs[`card-${this.rules[0].index}`].onEnabledDisabledMode();
        console.log(this.cardRefs[`card-${this.rules[0].index}`]);
        this.cardRefs[`card-${this.rules[1].index}`].onEnabledDisabledMode();
        this.rules = [];

        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 1
        )
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        console.log("wrong!");
        console.log(
          this.cardRefs[`card-${this.rules[0].index}`].onFlipBackCard
        );
        setTimeout(() => {
          this.cardRefs[`card-${this.rules[0].index}`].onFlipBackCard();
          this.cardRefs[`card-${this.rules[1].index}`].onFlipBackCard();
          this.cardRefs[`card-${this.rules[0].index}`].onEnabledDisabledMode();
          this.rules = [];
        }, 800);
      } else return false;
    },
  },
};
</script>

<style scoped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.screen__inner {
  width: calc(424px);
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
