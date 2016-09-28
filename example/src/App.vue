<template>
	<div id="app">
		<section data-title="Default">
			<div class="pull-left">
				<vue2-range ref="range1" :val="val1" @callback="getVal1"></vue2-range>
				<h1><small>Value: </small>{{ val1 }}</h1>
				<div class="btn-group">
					<button @click="setVal(1, 20)">set value => 20</button>
					<button @click="setVal(1, 100)">set value => 100</button>
				</div>
			</div>
			<div class="pull-right">
				<code>{
<span class="green">width</span>: <span class="yellow">'auto'</span><small class="gray">// 组件宽度</small>,
<span class="green">height</span>: <span class="yellow">4</span><small class="gray">// 组件高度</small>,
<span class="green">dotSize</span>: <span class="yellow">16</span><small class="gray">// 滑块大小</small>,
<span class="green">min</span>: <span class="yellow">0</span><small class="gray">// 最小值</small>,
<span class="green">max</span>: <span class="yellow">100</span><small class="gray">// 最大值</small>,
<span class="green">interval</span>: <span class="yellow">1</span><small class="gray">// 分段间隔</small>,
<span class="green">disabled</span>: <span class="yellow">false</span><small class="gray">// 是否不可用</small>,
<span class="green">show</span>: <span class="yellow">true</span><small class="gray">// 是否显示组件</small>,
<span class="green">piecewise</span>: <span class="yellow">false</span><small class="gray">// 是否显示分段样式</small>,
<span class="green">val</span>: <span class="yellow">1</span><small class="gray">// 初始值</small>
}</code>
			</div>
		</section>
		<section data-title="Demo1">
			<div class="pull-left">
				<vue2-range ref="range2" :width="300" :height="10" :dot-size="30" :min="1" :max="25" :interval="4" :piecewise="true" :disabled="disabled" :val="val2" @callback="getVal2">
					<h3 slot="left">1</h3>
					<h3 slot="right">25</h3>
				</vue2-range>
				<h1><small>Value: </small>{{ val2 }}</h1>
				<div class="btn-group">
					<button @click="setVal(2, 5)">set value => 5</button>
					<button @click="setVal(2, 25)">set value => 25</button>
					<button @click="setDisabled">set disabled</button>
				</div>
			</div>
			<div class="pull-right">
				<code>{
<span class="green">width</span>: <span class="yellow">300</span>,
<span class="green">height</span>: <span class="yellow">10</span>,
<span class="green">dotSize</span>: <span class="yellow">30</span>,
<span class="green">min</span>: <span class="yellow">1</span>,
<span class="green">max</span>: <span class="yellow">25</span>,
<span class="green">interval</span>: <span class="yellow">4</span>,
<span class="green">piecewise</span>: <span class="yellow">true</span>,
<span class="green">val</span>: <span class="yellow">25</span>
}</code>
			</div>
		</section>
	</div>
</template>

<script>
import vue2Range from './vue2-range-sliders.vue';

export default {
	components: {
		vue2Range
	},
	data () {
		return {
			msg: 'Hello Vue!',
			val1: 0,
			val2: 25,
			disabled: false
		}
	},
	methods: {
		getVal1(val) {
			this.val1 = val
		},
		getVal2(val) {
			this.val2 = val
		},
		setDisabled() {
			this.disabled = !this.disabled
		},
		setVal(kind, num) {
			this[`val${kind}`] = num
			this.$refs[`range${kind}`].setValue(num)
		}
	}
}
</script>

<style>
body {
	font-family: Helvetica, sans-serif;
}

#app {
}

section {
	padding: 40px 30px 20px 30px;
	margin: 20px;
	box-shadow: 0 0 5px 3px rgba(0, 0, 0, .36);
	max-width: 800px;
	overflow: hidden;
	position: relative;
}

section::after {
	content: attr(data-title);
	position: absolute;
	left: 5px;
	top: 5px;
	font-size: 14px;
	font-weight: bold;
	color: #333;
}

.pull-left {
	float: left;
}

.pull-right {
	float: right;
}


button {
    padding: 9px 15px;
    display: inline-block;
    border: none;
    background-color: #ccc;
    border-radius: 3px;
    transition: background-color .2s;
    margin-right: 10px;
}

.green {
	color: #2ecc71;
}

.yellow {
	color: #ffd400;
}

.gray {
	color: #D3DCE6;
}

code {
    padding: 10px 20px;
    min-width: 280px;
    display: inline-block;
    background-color: #333;
    color: #fff;
    white-space: pre-wrap;
    font-family: Consolas, Monaco, Droid, Sans, Mono, Source, Code, Pro, Menlo, Lucida, Sans, Type, Writer, Ubuntu, Mono;
    border-radius: 5px;
}
</style>
