<template>
    <div class="ranges">
        <div class="range_inputs" v-if="autoApply===false">
            <button class="applyBtn btn btn-sm btn-success" :disabled="canSelect" type="button" @click="$emit('clickApply')">{{locale.applyLabel}}</button>
            <button class="cancelBtn btn btn-sm btn-default" type="button" @click="$emit('clickCancel')">{{locale.cancelLabel}}</button>
        </div>

        <ul v-if="ranges">
            <li v-for="(value, key) in ranges" @click="$emit('clickRange', value)">{{key}}</li>
        </ul>
    </div>
</template>

<script>
    export default {
        props: ['canSelect', 'ranges', 'locale', 'autoApply']
    }
</script>
