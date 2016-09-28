<template>
	<div ref="wrap" :class="['vue-range-wrap', { 'vue-range-disabled': disabled }]" v-show="show" :style="{ width: rangeWidth }">
		<span class="vue-range-min">
			<slot name="left">{{ min }}</slot>
		</span>
		<div ref="elem" class="vue-range" :style="{ height: `${height}px`, margin: `${dotSize / 2}px` }">
			<div ref="dot" class="vue-range-dot" :style="{ width: `${dotSize}px`, height: `${dotSize}px`, top: `${(-(dotSize - height) / 2)}px` }"></div>
			<template v-if="piecewise">
				<ul class="vue-range-piecewise">
					<li v-for="(value, index) of total - 1" :style="{ width: `${height}px`, height: `${height}px`, top: `0px`, left: `${gap*(index + 1) - height / 2}px` }">
					</li>
				</ul>
			</template>
		</div>
		<span class="vue-range-max">
			<slot name="right">{{ max }}</slot>
		</span>
	</div>
</template>

<script>
export default {
	data() {
		return {
			flag: false,
			w: 0,
			value: 0
		}
	},
	props: {
		width: {
			type: [Number, String],
			default: 'auto'
		},
		height: {
			type: Number,
			default: 4
		},
		dotSize: {
			type: Number,
			default: 16
		},
		min: {
			type: Number,
			default: 0
		},
		max: {
			type: Number,
			default: 100
		},
		interval: {
			type: Number,
			default: 1
		},
		show: {
			type: Boolean,
			default: true
		},
		disabled: {
			type: Boolean,
			default: false
		},
		piecewise: {
			type: Boolean,
			default: false
		},
		val: {
			type: Number,
			default: 0
		}
	},
	computed: {
		total: function() {
			if ((this.max - this.min) % this.interval !== 0) {
				throw new Error('Prop[interval] is illegal, Please enter a valid interval')
			}
			return (this.max - this.min) / this.interval
		},
		gap: function() {
			return this.w / this.total
		},
		left: function() {
			return this.$refs.elem.getBoundingClientRect().left
		},
		position: function() {
			return (this.value - this.min) / this.interval * this.gap
		},
		rangeWidth: function() {
			return typeof this.width === 'number' ? (this.width + 'px') : this.width
		}
	},
	methods: {
		bindEvent() {
			this.$refs.dot.addEventListener('mousedown', this.moveStart)
			this.$refs.wrap.addEventListener('click', this.wrapClick)
			this.$refs.wrap.addEventListener('mousemove', this.moveing)
			this.$refs.wrap.addEventListener('mouseup', this.moveEnd)
			this.$refs.wrap.addEventListener('mouseleave', this.moveEnd)
		},
		removeEvent() {
			this.$refs.dot.removeEventListener('mousedown', this.moveStart)
			this.$refs.wrap.removeEventListener('click', this.wrapClick)
			this.$refs.wrap.removeEventListener('mousemove', this.moveing)
			this.$refs.wrap.removeEventListener('mouseup', this.moveEnd)
			this.$refs.wrap.removeEventListener('mouseleave', this.moveEnd)
		},
		wrapClick(e) {
			if (this.disabled) return false
			let x = e.clientX - this.left
			this.setValueOnPos(x)
		},
		moveStart() {
			if (this.disabled) return false
			this.flag = true
		},
		moveing(e) {
			if (!this.flag) return false
			let x = e.clientX - this.left
			this.setValueOnPos(x, true)
		},
		moveEnd() {
			this.flag = false
			this.setPosition(0.2)
		},
		setValueOnPos(x, bool) {
			if (x >= 0 && x <= this.w) {
				this.setTransform(x)
				let v = Math.round(x / this.gap) * this.interval + this.min
				this.setValue(v, bool)
			}
			else if (x < 0) {
				this.setTransform(0)
				this.setValue(this.min)
			}
			else {
				this.setTransform(this.w)
				this.setValue(this.max);
			}
		},
		setValue(val, bool) {
			if (val < this.min || val > this.max) return false
			val !== this.value && this.$emit('callback', val)
			this.value = val
			bool || this.setPosition()
		},
		setPosition(time = 0.5) {
			this.flag || this.setTransitionTime(time)
			this.setTransform(this.position)
			this.flag || this.setTransitionTime(0)
		},
		setTransform(val) {
			this.$refs.dot.style.transform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
			this.$refs.dot.style.WebkitTransform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
		},
		setTransitionTime(time) {
			time || this.$refs.dot.offsetWidth
			this.$refs.dot.style.transitionDuration = time + 's'
			this.$refs.dot.style.webkitTransitionDuration = time + 's'
		}
	},
	mounted() {
		this.w = this.$refs.elem.offsetWidth
		this.bindEvent()
		this.setValue(this.val)
	},
	destroyed() {
		this.removeEvent()
	}
}
</script>

<style scoped>
.vue-range-wrap {
	position: relative;
	display: -webkit-box;
	display: -webkit-flex;
	display: -moz-box;
	display: -ms-flexbox;
	display: flex;
    align-items: center;
	-webkit-box-align: center;
	-webkit-align-items: center;
	-moz-box-align: center;
	-ms-flex-align: center;
	-ms-grid-row-align: center;
    justify-content: center;
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    -moz-box-pack: center;
    -ms-flex-pack: center;
}
.vue-range-wrap.vue-range-disabled {
	opacity: .5;
	cursor: not-allowed;
}
.vue-range-wrap .vue-range {
    position: relative;
    display: inline-block;
    vertical-align: middle;
    flex: 1;

    -webkit-user-select: none;
            user-select: none;

    border-radius: 15px;
    background-color: #3498db;
}
.vue-range-wrap .vue-range .vue-range-dot {
    position: absolute;
    z-index: 9;
    left: 0;

    -webkit-user-select: none;
            user-select: none;

    border-radius: 50%;
    background-color: #f1c40f;

    transition: all 0s;
}
.vue-range-min, .vue-range-max {
	font-size: 14px;
	color: #3498db;
	vertical-align: middle;
}
.vue-range-min {
	margin-right: 5px;
}
.vue-range-max {
	margin-left: 5px;
}
.vue-range-piecewise {
	list-style: none;
}
.vue-range-piecewise li {
	position: absolute;
	background-color: rgba(0, 0, 0, 0.16);
	border-radius: 50%;
}
</style>
