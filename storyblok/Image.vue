<script setup>
  const props = defineProps({ blok: Object });
  // Sets a max height on the image in pixels if there is one set and it is above 0
  const imageMaxHeight = computed(() => {
    return !!props.blok?.max_height && props.blok?.max_height > 0 ? { maxHeight: props.blok?.max_height + 'px' } : null
  })
</script>

<template>
  <div v-editable="blok" class="flex w-full" :class="blok?.position">
    <NuxtImg 
      v-if="!!blok.image?.filename" 
      :src="blok.image.filename" 
      :alt="blok.image.alt" 
      :style="imageMaxHeight" 
      class="h-full" 
      :class="{'w-full' : !!blok?.full_width}"
      placeholder provider="storyblok"
    />
  </div>
</template>