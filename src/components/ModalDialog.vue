<script setup lang="ts">
import { ref, watch, useTemplateRef, nextTick } from 'vue'
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'

import type { Card } from '@/types'

const props = defineProps<{
  isOpen: boolean
  card: Card | null
  mode: 'add' | 'edit'
}>()

const emit = defineEmits<{
  (e: 'close'): void
  (e: 'save', card: Card): void
}>()

const titleInput = ref<HTMLInputElement | null>(null)
const modalEl = useTemplateRef<HTMLDivElement>('modalEl')
const { activate, deactivate } = useFocusTrap(modalEl)

const localCard = ref<Card>({
  id: 0,
  title: '',
  description: '',
})

watch(
  () => props.isOpen,
  async () => {
    if (props.isOpen) {
      await nextTick()
      activate()
      titleInput.value?.focus()
    } else {
      deactivate()
    }
  },
)

watch(
  () => props.card,
  (newCard) => {
    if (newCard) {
      localCard.value = { ...newCard }
    } else {
      localCard.value = { id: 0, title: '', description: '' }
    }
  },
  { immediate: true },
)
</script>

<template>
  <Teleport to="body" v-if="isOpen">
    <div
      class="fixed inset-0 z-10 bg-black bg-opacity-50 flex items-center justify-center"
      role="dialog"
      aria-modal="true"
      @keydown.esc="emit('close')"
      @click.self="emit('close')"
      ref="modalEl"
    >
      <div class="bg-white p-5 rounded max-w-md w-full">
        <h2 class="text-xl font-bold mb-4">{{ mode === 'add' ? 'Add Card' : 'Edit Card' }}</h2>
        <input
          type="text"
          placeholder="Card Title"
          aria-label="Card Title"
          class="w-full p-2 mb-4 border rounded"
          ref="titleInput"
          v-model="localCard.title"
        />

        <textarea
          class="w-full p-2 mb-4 border rounded"
          placeholder="Description"
          aria-label="Card Description"
          v-model="localCard.description"
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
            @click="emit('save', localCard)"
          >
            {{ mode === 'add' ? 'Add' : 'Save' }}
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style scoped></style>
