<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps<{
  isOpen: boolean
}>()

const emit = defineEmits<{
  (e: 'close'): void
}>()

const titleInput = ref<HTMLInputElement | null>(null)

watch(
  () => props.isOpen,
  () => {
    setTimeout(() => {
      if (props.isOpen) {
        titleInput.value?.focus()
      }
    }, 0)
  },
)
</script>

<template>
  <Teleport to="body" v-if="isOpen">
    <div
      class="fixed inset-0 z-10 bg-black bg-opacity-50 flex items-center justify-center"
      role="dialog"
      aria-modal="true"
      @keydown.esc="emit('close')"
    >
      <div class="bg-white p-5 rounded max-w-md w-full">
        <h2 class="text-xl font-bold mb-4">Add new card</h2>
        <input
          type="text"
          placeholder="Card Title"
          aria-label="Card Title"
          class="w-full p-2 mb-4 border rounded"
          ref="titleInput"
        />

        <textarea
          class="w-full p-2 mb-4 border rounded"
          placeholder="Description"
          aria-label="Card Description"
        ></textarea>

        <div class="flex justify-end gap-2">
          <button
            class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded"
            @click="emit('close')"
          >
            Cancel
          </button>
          <button
            class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded"
            @click="emit('close')"
          >
            Save
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped></style>
