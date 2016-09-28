<template>
	<span ref="wrap" class="vue-range-wrap" v-show="show">
		<div ref="elem" class="vue-range" :style="{ width: rangeWidth, height: `${height}px`, margin: `${dotSize / 2}px` }">
			<div ref="dot" class="vue-range-dot" :style="{ width: `${dotSize}px`, height: `${dotSize}px`, top: `${(-(dotSize - height) / 2)}px` }"></div>
		</div>
	</span>
</template>

<script>
export default {
	data() {
		return {
			flag: false,
			value: 0
		}
	},
	props: {
		width: {
			type: [Number, String],
			default: 150
		},
		height: {
			type: Number,
			default: 4
		},
		dotSize: {
			type: Number,
			default: 15
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
		val: {
			type: Number,
			default: 1
		}
	},
	computed: {
		total: function() {
			return (this.max - this.min) / this.interval
		},
		gap: function() {
			return this.width / this.total
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
			document.addEventListener('mousemove', this.moveing)
			document.addEventListener('mouseup', this.moveEnd)
			document.addEventListener('mouseleave', this.moveEnd)
		},
		removeEvent() {
			this.$refs.dot.removeEventListener('mousedown', this.moveStart)
			this.$refs.wrap.removeEventListener('click', this.wrapClick)
			document.removeEventListener('mousemove', this.moveing)
			document.removeEventListener('mouseup', this.moveEnd)
			document.removeEventListener('mouseleave', this.moveEnd)
		},
		wrapClick(e) {
			let x = e.clientX - this.left;
			this.setTransform(x);
			let v = Math.round(x / this.gap) * this.interval + this.min;
			this.setValue(v);
		},
		moveStart() {
			this.flag = true
		},
		moveing(e) {
			if (!this.flag) return false;
			let x = e.clientX - this.left;
			if (x >= 0 && x <= this.width) {
				this.setTransform(x);
				let v = Math.round(x / this.gap) * this.interval + this.min;
				this.setValue(v);
			}
			else if (x < 0) {
				this.setTransform(0);
				this.setValue(this.min);
			}
			else {
				this.setTransform(this.width);
				this.setValue(this.max);
			}
		},
		moveEnd() {
			this.flag = false;
			this.setPosition();
		},
		setValue(val) {
			this.value !== val && this.$emit('callback', val)
			this.value = val
			this.setPosition()
		},
		setPosition() {
			this.setTransform(this.position)
		},
		setTransform(val) {
			this.$refs.dot.style.transform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
			this.$refs.dot.style.WebkitTransform = 'translateX(' + (val - (this.dotSize / 2)) + 'px)'
		}
	},
	mounted() {
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
    display: inline-block;
}
.vue-range-wrap .vue-range {
    position: relative;
    display: inline-block;
    vertical-align: middle;

    -webkit-user-select: none;
            user-select: none;

    border-radius: 15px;
    background-color: rgba(0, 0, 0, .36);
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
</style>
