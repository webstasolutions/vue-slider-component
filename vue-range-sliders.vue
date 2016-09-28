<template>
	<div v-el:wrap :class="['vue-range-wrap', { 'vue-range-disabled': disabled }]" v-show="show" :style="{ width: rangeWidth }">
		<span class="vue-range-min">
			<slot name="left">{{ min }}</slot>
		</span>
		<div v-el:elem class="vue-range" :style="{ height: `${height}px`, margin: `${dotSize / 2}px` }">
			<div v-el:dot class="vue-range-dot" :style="{ width: `${dotSize}px`, height: `${dotSize}px`, top: `${(-(dotSize - height) / 2)}px` }"></div>
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
			w: 0
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
		value: {
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
			return this.$els.elem.getBoundingClientRect().left
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
			this.$els.dot.addEventListener('mousedown', this.moveStart)
			this.$els.wrap.addEventListener('click', this.wrapClick)
			this.$els.wrap.addEventListener('mousemove', this.moveing)
			this.$els.wrap.addEventListener('mouseup', this.moveEnd)
			this.$els.wrap.addEventListener('mouseleave', this.moveEnd)
		},
		removeEvent() {
			this.$els.dot.removeEventListener('mousedown', this.moveStart)
			this.$els.wrap.removeEventListener('click', this.wrapClick)
			this.$els.wrap.removeEventListener('mousemove', this.moveing)
			this.$els.wrap.removeEventListener('mouseup', this.moveEnd)
			this.$els.wrap.removeEventListener('mouseleave', this.moveEnd)
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
			this.$els.dot.style.transform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
			this.$els.dot.style.WebkitTransform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
		},
		setTransitionTime(time) {
			time || this.$els.dot.offsetWidth
			this.$els.dot.style.transitionDuration = time + 's'
			this.$els.dot.style.webkitTransitionDuration = time + 's'
		}
	},
	ready() {
		this.w = this.$els.elem.offsetWidth
		this.bindEvent()
		this.setValue(this.value)
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

