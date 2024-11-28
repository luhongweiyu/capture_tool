<template>

        <input type="checkbox" v-model="localValue" > <span :style="props.style">{{ props.showtext }}</span></input>
    <!-- <p> -->
    <!-- 组件内:{{value1}} -->
    <!-- </p> -->
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';
// 声明接收父组件传递的属性值：自定义属性
const props = defineProps({
    up_value: Boolean,
    default_value: Boolean,
    showtext: String,
    style: String|Object,
});
const localValue = ref(props.up_value)
const emit = defineEmits(['update:up_value']);


// const value1 = defineModel();

// console.log(props.style)
watch(() => props.up_value, (newValue) => {
    localValue.value = newValue;
});

watch(localValue, (newValue) => {
    emit('update:up_value', newValue);
});

if (typeof props.default_value === "undefined") {
    props.default_value = false;
}
if (typeof props.up_value === "undefined") {
    console.log("undefined")
    localValue.value = props.default_value
}
// console.log(props.up_value)
// onMounted(() => {

// });
</script>

<style></style>