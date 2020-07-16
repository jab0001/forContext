<template>
    <div class="todos-filter">
        <div class="todos-filter__chk">
            <label>
                <input v-model="value.status"
                    type="checkbox"
                    :true-value='1'
                    :false-value='0'>
                <span>done todos</span>
            </label>
        </div>
        <div class="todos-filter__text">
            <input v-model="value.name"
                type="text"
                class="todos-filter__input"
                placeholder="Search todos">
        </div>
    </div>
</template>

<script>
export default {
    name: 'TodoFilter',
    props: {
        value: {
            type: Object,
            default: () => {}
        },
    },
    watch: {
        value(val) {
            this.$emit('input', val)
        },
    },
}
</script>

<style lang="scss">
.todos-filter {
    width: 320px;
    margin: 0 auto;

    &__chk {
        margin-bottom: 10px;
    }
}
</style>
