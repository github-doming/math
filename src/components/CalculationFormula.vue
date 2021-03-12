<template>
	<div :style="fontSize()" @dblclick="cardShow()" class="can-not-select">
		<div v-if="type === 'add'">
			<span v-for="(item,index) in data" :key="index">
				<span v-if="index !== unknown">{{item}}</span>
				<span v-else style="color: red">{{question()}}</span>
				<span v-if="index !== data.length - 1">+</span>
				<span v-else>=</span>
			</span>
			<span v-if="data.length !== unknown">{{max}}</span>
			<span v-else style="color: red">{{question()}}</span>
		</div>
		<div v-else>
			<span v-if="data.length !== unknown">{{max}}</span>
			<span v-else style="color: red">{{question()}}</span>
			<span v-for="(item,index) in data" :key="index">
				<span v-if="index !== data.length - 1">-</span>
				<span v-else>=</span>
				<span v-if="index !== unknown">{{item}}</span>
				<span v-else style="color: red">{{question()}}</span>
			</span>
		</div>

	</div>
</template>

<script>
  export default {
    name: "CalculationFormula",
    props: {
      items: {
        type: Array,
        required: true,
      },
      attrs: {
        type: Object,
        required: true,
      },
    },
    watch: {
      attrs: {
        handler(newValue) {
          this.tempAnswer = newValue.answer;
        },
        deep: true
      },
    },
    created() {
      let zero = 0;
      this.data = this.items.slice(3).filter(ele => {
        if (ele === 0) {
          if (zero === 0) {
            zero++;
            return true;
          } else {
            return false;
          }
        }
        return true;
      });
    },
    computed: {
      unknown() {
        if (this.attrs.rank < 1 || this.attrs.rank > this.data.length + 1) {
          return Math.floor(Math.random() * (this.data.length + 1));
        }
        if (this.type === 'add') {
          return this.attrs.rank - 1;
        } else {
          if (this.attrs.rank === 1) {
            return this.data.length;
          }
          return this.attrs.rank - 2;
        }
      }
    },
    data() {
      return {
        type: this.items[1] === 1 ? 'add' : 'sub',
        max: this.items[2],
        data: [],
        tempAnswer: this.attrs.answer,
      }
    },
    methods: {
      fontSize() {
        return {fontSize: (2 * this.attrs.size + 22) + 'px'};
      },
      question() {
        if (this.tempAnswer) {
          if (this.unknown === this.data.length) {
            return this.max;
          } else {
            return this.data[this.unknown];
          }
        } else {
          return '( )';
        }
      },
      cardShow() {
        setTimeout(this.hidden, 1000);
        this.tempAnswer = true;
      },
      hidden() {
        this.tempAnswer = this.attrs.answer;
      },
    }
  }
</script>

<style scoped>
	.can-not-select {
		-webkit-touch-callout: none;
		-webkit-user-select: none;
		-moz-user-select: none;
		-ms-user-select: none;
		user-select: none;
	}

</style>