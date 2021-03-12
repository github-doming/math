<template>
	<div>
		<a-form layout="inline">
			<a-row type="flex" justify="center">
				<a-col :span="4">
					<a-form-item :label="`计算范围`">
						<a-input-number class="short-input" v-model="rangeStart" placeholder="起点" :min="1"/>
						<span :style="{ display: 'inline-block', width: '20px', textAlign: 'center' }"> - </span>
						<a-input-number class="short-input" v-model="rangeEnd" placeholder="终点" :min="1"/>
					</a-form-item>
				</a-col>
				<a-col :span="3">
					<a-form-item :label="`元素个数`">
						<a-input-number class="short-input" v-model="elements" placeholder="个数" :min="2"/>

					</a-form-item>
				</a-col>
				<a-col :span="3">
					<a-form-item :label="`生成数量`">
						<a-input-number class="short-input" v-model="len" placeholder="数量" :min="0"/>
					</a-form-item>
				</a-col>
				<a-col :span="3">
					<a-form-item :label="`括号位置`">
						<a-input-number class="short-input" v-model="attrs.rank" placeholder="位置" :min="0" :max="elements+1"/>
					</a-form-item>
				</a-col>
				<a-col :span="6">
					<a-form-item :label="`选择运算类型`">
						<a-checkbox-group v-model="computingType">
							<a-checkbox value="add">
								加法
							</a-checkbox>
							<a-checkbox value="sub">
								减法
							</a-checkbox>
						</a-checkbox-group>
					</a-form-item>
				</a-col>
				<a-col :span="3">
					<a-form-item>
						<a-button type="primary" html-type="submit" @click="generate">
							生成
						</a-button>
						<a-button type="primary" @click="attrs.answer = !attrs.answer" style="margin-left: 10px">
							答案
						</a-button>
					</a-form-item>
				</a-col>
				<a-col :span="2">
					<a-form-item>
						<a-button shape="circle" @click="attrs.size++" icon="plus"/>
						<a-button shape="circle" @click="attrs.size--" icon="minus" style="margin-left: 10px"/>
					</a-form-item>
				</a-col>

			</a-row>
		</a-form>
		<div class="ComputingPanel">
			<a-card>
				<a-card-grid
								v-for="(item) in data" :key="item[0]"
								:style="[{width:(attrs.size*elements*6*order + 100)+'px'},{textAlign:'center'}]">
					<CalculationFormula :items="item" :attrs="attrs"/>
				</a-card-grid>
			</a-card>
		</div>
	</div>
</template>

<script>
  import CalculationFormula from "@/components/CalculationFormula";

  export default {
    name: "ComputingPanel",
    components: {CalculationFormula},
    computed: {
      order() {
        return Math.round((this.rangeStart + this.rangeEnd) / 2).toString().length;
      }
    },
    data() {
      return {
        rangeStart: 5,
        rangeEnd: 10,
        elements: 2,
        computingType: ['add'],
        data: [],
        len: 30,
        attrs: {size: 6, answer: false, rank: null},
      }
    },
    methods: {
      generate() {
        if (!this.standard()) {
          return;
        }
        this.attrs.answer = false;
        const type = this.computingType.length > 1 ? 2 : this.computingType[0] === 'add' ? 1 : 0;
        this.computing(type);
      },
      computing(type) {
        this.data = [];
        const range = this.rangeEnd - this.rangeStart;
        for (let i = 0; i < this.len; i++) {
          let max = Math.floor(Math.random() * range) + this.rangeStart;
          this.data[i] = [];
          this.data[i][0] = this.getKey();
          this.data[i][1] = type !== 2 ? type : Math.round(Math.random());
          this.data[i][2] = max;
          let tmp = max;
          for (let j = 1; j < this.elements; j++) {
            let range = Math.ceil(Math.random() * (tmp));
            this.data[i][j + 2] = range;
            tmp = tmp - range;
          }
          this.data[i][this.elements + 2] = tmp;
        }
      },
      notification(message) {
        this.$notification.open({
          message: '错误',
          description: message,
          placement: 'bottomRight',
        });
      },
      standard() {
        if (this.rangeStart == null) {
          this.rangeStart = 5;
        }
        if (this.rangeEnd == null) {
          this.rangeEnd = 10;
        }
        if (this.elements == null) {
          this.elements = 2;
        }
        if (this.elements < 2) {
          this.notification('元素个数必须大于等于2');
          return false;
        }
        if (this.computingType.length === 0) {
          this.notification('请选择运算类型');
          return false;
        }
        if (this.rangeStart > this.rangeEnd) {
          const temp = this.rangeStart;
          this.rangeStart = this.rangeEnd;
          this.rangeEnd = temp;
        }
        return true;
      },
      getKey() {
        return (((1 + Math.random()) * new Date().getTime()) | 0).toString(16);
      },
    }
  }
</script>

<style scoped>
	.short-input {
		display: inline-block;
		width: 60px;
	}
</style>