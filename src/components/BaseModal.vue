<template>
    <Teleport to="body">
        <Transition name="modal-outer">
            <div v-show="modalActive" class="absolute w-full bg-black bg-opacity-40 h-screen top-0 left-0 flex justify-center px-8">
                <Transition name="modal-inner">
                    <div class="p-4 bg-white self-start mt-32 max-w-screen-sm rounded-md flex flex-col justify-center">
                        <slot />
                        <button @click="$emit('close-modal')" class="text-white mt-8 px-6 py-2 bg-weather-primary rounded-md">Close</button>
                    </div>
                </Transition>
            </div>
        </Transition>
    </Teleport>
</template>

<script setup>
defineEmits(['close-modal']);
defineProps({
    modalActive: {
        type: Boolean,
        default: false,
    },
})
</script>

<style scoped>
.modal-outer-enter-active,
.modal-outer-leave-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.52, 0.19, 1.02);
}

.modal-outer-enter-from,
.modal-outer-leave-to {
    opacity: 0;
}

.modal-inner-enter-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.52, 0.19, 1.02) 0.15s;
}

.modal-inner-leave-active {
    transition: opacity 0.3s cubic-bezier(0.52, 0.52, 0.19, 1.02);
}

.modal-inner-enter-from{
    opacity: 0;
    transform: scale(0.8);
}

.modal-inner-leave-to {
    opacity: 0;
    transform: scale(0.8);
}
</style>