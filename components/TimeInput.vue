<template>
    <div>
        <label :for="name">{{ title }}</label>
        <input type="time" class="input" :id="name" :name="name" :value="value" @input="$emit('input', $event.target.value)" :disabled="disabled">
    </div>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            required: true
        },
        name: {
            type: String,
            required: true
        },
        value: {
            type: String,
        },
        disabled: {
            type: Boolean,
        }
    }
}
</script>