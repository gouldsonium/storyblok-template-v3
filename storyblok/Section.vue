<script setup>
  const props = defineProps({ blok: Object });
  const resolvedRichText = computed(() => renderRichText(props.blok.text));

    // For the background style object
  const hasBackgroundImage = computed(() => {return !!props.blok.background_image?.filename});  
  const hasBackgroundColor = computed(() => {return !!props.blok.background_color?.color});

  const backgroundStyle = computed(() => {
    if (hasBackgroundImage.value) {
      return {
        backgroundImage: `linear-gradient(to right, #22283180, #22283180), url(${props.blok.background_image.filename})`,
        backgroundPosition: 'center center',
        backgroundSize: 'cover',
        backgroundRepeat: 'no-repeat',
      };
    }

    if(hasBackgroundColor.value){
      return {backgroundColor: props.blok.background_color?.color}
    }

    return null;
  });

</script>

<template>
  <section v-editable="blok" :style="backgroundStyle" class="w-full bg-primary px-6 lg:px-8 py-24 sm:py-32">
    <div :class="blok?.layout">
      <h2 v-if="!!blok?.title" class="font-bold text-secondary mb-10 flex items-center" :style="{color: blok.title_color?.color}"
      :class="{'text-center justify-center' : blok?.text_center}">
        <span>
          <img v-if="blok?.text_center" src="~/assets/img/dash.png" class="my-5 h-2" alt="dash.png">
        </span>
        <span :class="{'mx-3' : blok?.text_center}">
          {{ blok?.title }}
        </span>
        <span>
          <img v-if="blok?.text_center" src="~/assets/img/dash.png" class="my-5 h-2 transform rotate-180" alt="dash.png">
        </span>
      </h2>
      <div v-if="!!blok?.text" v-html="resolvedRichText" class="max-w-none prose mb-10"
        :class="{ 'prose-invert': blok.invert, 'text-center' : blok?.text_center}"></div>
        <StoryblokComponent v-for="blok in blok.content" :key="blok._uid" :blok="blok" />
    </div>
  </section>
</template>