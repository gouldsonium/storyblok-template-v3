<script setup>
  import { ref, onMounted, onUnmounted } from 'vue';
  import { Dialog, DialogPanel } from '@headlessui/vue';
  import { Bars3Icon, XMarkIcon } from '@heroicons/vue/24/outline';
  const runtimeConfig = useRuntimeConfig();

  // fetch header details from Config object
  const storyblokApi = useStoryblokApi();
  const { data } = await storyblokApi.get('cdn/stories/config', {
    version: runtimeConfig.public.VERSION,
    resolve_links: 'url',
  });

  // Fetch Navbar Logo
  const logo = data.story.content.logo?.filename || null;
  
  // Define header menu as a ref
  const headerMenu = ref(null);
  headerMenu.value = data.story.content.header_menu;

  // CTA stands for Call To Action button
  const CTA = data.story.content?.cta;

  // Open or close mobile menu
  const mobileMenuOpen = ref(false);

  // Handle scroll
  const isScrolled = ref(false);
  const isMobile = ref(null);

  // Add scroll event listener
  onMounted(() => {
    checkMobile();
    window.addEventListener('scroll', handleScroll)
    window.addEventListener('resize', checkMobile);
  })

  // Remove scroll event listener on component unmount
  onUnmounted(() => {
    window.removeEventListener('scroll', handleScroll);
    window.removeEventListener('resize', checkMobile);
  })

  // media query for mobile devices
  const checkMobile = () => {
    isMobile.value = window.matchMedia("(max-width: 768px)").matches;
  };

  // Handle scroll event
  const handleScroll = () => {
    const scrollPosition = window.scrollY
    isScrolled.value = scrollPosition > 0
  };

  // Logo and header options
  const changeOnScroll = data.story.content?.change_on_scroll;
  const darkmode = data.story.content?.darkmode;
  const whitenLogo = data.story.content?.whiten;
  const logoHeight = data.story.content?.height;

  // Sets the favicon for the site
  const favicon = data.story.content.favicon?.filename || null;
  useHead({
    link: [
      {
        rel: 'icon',
        type: 'image/png',
        href: favicon
      }
    ]  
  })

  // If there is no logo, links or call to action btn, hide the navbar
  const showHeader = !!logo || headerMenu.value?.length > 0 || !!CTA?.length > 0;
  const navClasses = () => {
    if(darkmode){
      return 'bg-white text-gray-800 dark:bg-gray-900 dark:text-white'
    } else {
      return 'bg-white text-gray-800'
    }
  }
</script>

<template>
  <header class="fixed inset-x-0 top-0 z-50 transition duration-500" v-if="showHeader" 
    :class="!isScrolled && changeOnScroll ? 'bg-transparent text-white': navClasses()">
    <nav class="flex items-center justify-between px-6 lg:px-8" aria-label="Global">
      <div class="flex-1">
        <NuxtLink to="/" class="-m-1.5 p-1.5" style="max-width: 90%;">
          <span class="sr-only">Logo</span>
          <NuxtImg v-if="logo" placeholder provider="storyblok"
            class="w-auto transition duration-300"
            :style="{ height: isMobile ? '50px' : logoHeight + 'px' }"
            :class="{'dark-brighten' : !!whitenLogo && !isScrolled}"
            :src="logo" alt="Nav Logo" 
          />
        </NuxtLink>
      </div>
      <div class="flex lg:hidden">
        <button type="button" class="-m-2.5 inline-flex items-center justify-center rounded-md p-2.5 " 
          @click="mobileMenuOpen = true" :class="[isScrolled ? 'text-gray-700' : 'text-white', darkmode ? 'dark:text-white' : '']">
          <span class="sr-only">Open main menu</span>
          <Bars3Icon class="h-6 w-6" aria-hidden="true" />
        </button>
      </div>
      <div class="hidden lg:flex items-center lg:gap-x-12">
        <AppLink v-for="blok in headerMenu" :key="blok.name" :to="blok.url" 
          class="font-semibold leading-6 hover:text-secondary font-heading"
        >
          {{ blok.text }}
        </AppLink>
        <StoryblokComponent
          id="callToAction"
          v-for="blok in CTA"
          :key="blok._uid"
          :blok="blok"
        />
      </div>
    </nav>
    <Dialog as="div" class="lg:hidden" @close="mobileMenuOpen = false" :open="mobileMenuOpen">
      <div class="fixed inset-0 z-50" />
      <DialogPanel 
        class="fixed inset-y-0 right-0 z-50 w-full overflow-y-auto bg-white text-gray-900 px-6 py-6 sm:max-w-sm sm:ring-1 sm:ring-gray-900/10"
        :class="{'dark:bg-gray-900 dark:text-white' : darkmode}"
      >
        <div class="flex items-center justify-between">
          <NuxtLink to="/" class="-m-1.5 p-1.5" @click="mobileMenuOpen = false">
            <span class="sr-only">Logo</span>
            <NuxtImg 
              class="h-24 sm:h-16 w-auto" 
              :src="logo" 
              alt="Mobile Nav Logo" 
              style="height: 50px"
              placeholder provider="storyblok"
            />
          </NuxtLink>
          <button type="button" class="-m-2.5 rounded-md p-2.5 text-gray-700" @click="mobileMenuOpen = false" :class="{'dark:text-white' : darkmode}">
            <span class="sr-only">Close menu</span>
            <XMarkIcon class="h-6 w-6" aria-hidden="true" />
          </button>
        </div>
        <div class="mt-6 flow-root">
          <div class="-my-6 divide-y divide-gray-500/10">
            <div class="space-y-2 py-6">
              <AppLink v-for="link in headerMenu" :key="link.name" :to="link.url" @click="mobileMenuOpen = false" 
                class="-mx-3 rounded-lg px-4 py-2 text-base font-semibold leading-7 block hover:opacity-50 duration-500 font-heading"
                >
                {{ link.text }}
              </AppLink>
              <StoryblokComponent
                class="text-base w-auto"
                v-for="blok in CTA"
                :key="blok._uid"
                :blok="blok"
                @click="mobileMenuOpen = false"
              />
            </div>
          </div>
        </div>
      </DialogPanel>
    </Dialog>
  </header>
</template>

<style scoped>
/* removes all color from logo and sets it to be pure white at top of the screen */
.dark-brighten{
  filter: brightness(0) invert(1);
}
</style>