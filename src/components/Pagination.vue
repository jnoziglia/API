<script setup>
    import { ref } from 'vue';
    const props =  defineProps({
        total: {
            type: Number,
            required: true
        },
        resultsPerPage: {
            type: Number,
            required: true
        }
    });
    const emit = defineEmits(['updateCurrent']);
    const current = ref(1);

    const prev = () => {
        if (current.value > 1) {
            current.value--;
            emit('updateCurrent', current.value);
        }
    }

    const next = () => {
        if (current.value < props.total) {
            current.value++;
            emit('updateCurrent', current.value);
        }
    }
</script>

<template>
    <div class="d-flex align-center justify-end">
        <v-btn text="Prev" @click="prev"></v-btn>
        <div class="mx-2">{{ current }} / {{ total }}</div>
        <v-btn text="Next" @click="next"></v-btn>
    </div>
</template>