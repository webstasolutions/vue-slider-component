<template>
	<div id="app">
		<vue2-range ref="range" :min="1" :max="100" :val="val" @callback="getVal"></vue2-range>
		<h1><small>Value: </small>{{ val }}</h1>
		<div class="btn-group">
			<button @click="setVal(20)">setVal20</button>
			<button @click="setVal(100)">setVal100</button>
		</div>
	</div>
</template>

<script>
import vue2Range from './vue2-range.vue';

export default {
	components: {
		vue2Range
	},
	data () {
		return {
			msg: 'Hello Vue!',
			val: 1
		}
	},
	methods: {
		getVal(val) {
			this.val = val
		},
		setVal(num) {
			this.val = num
			this.$refs.range.setValue(num)
		}
	}
}
</script>

<style>
body {
	font-family: Helvetica, sans-serif;
}

#app {
	padding: 30px;
}
</style>
