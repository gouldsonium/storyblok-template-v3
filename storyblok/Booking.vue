<script setup>
import { Switch, SwitchGroup, SwitchLabel } from '@headlessui/vue';
import { useRouter } from 'nuxt/app';

const router = useRouter();

const props = defineProps({ blok: Object });
const resolvedRichText = computed(() => renderRichText(props.blok.text));
// Agreed to privacy policy
const agreed = ref(false);
// Fields
const first_name = ref('');
const last_name = ref('');
const email = ref('');
const phone = ref('');
const message = ref('');

const handleSubmit = async () => {
  try {
    // Ensure user agrees to the privacy policy
    if(!agreed.value){
      alert('Please agree to our privacy policy');
      return;
    }
    // Built for Netlify forms
    const response = await fetch('/', {
      method: 'POST',
      body: new FormData(event.target),
      netlify: true // Important for Netlify Forms
    });
    router.push('/success'); // Redirect to the success page
  } catch (error) {
    console.error('Form submission error:', error);
    alert('Oops, something went wrong. Please get in touch another way');
  }
};

// Used for switch on agree
const computedStyle = computed(() => {
  if (agreed.value && props.blok?.privacy_policy?.color) {
    return { backgroundColor: props.blok.privacy_policy.color };
  }
  return {};
});
</script>

<template>
  <section :style="{ backgroundColor: blok.background_color?.color }" class="px-6 lg:px-8 py-24 sm:py-32 relative">
    <div :id="blok?.section_id" style="position: relative; visibility: hidden;" class="md:top-[-100px]"></div>
    <div class="text-center">
      <h2 class="font-semibold font-heading text-gray-800 dark:text-gray-100" :style="{color: blok?.title_color?.color}">{{ blok?.title || 'Get in touch' }}</h2>
      <div v-if="!!blok?.text" v-html="resolvedRichText" class="max-w-none prose my-5 dark:text-gray-100"
        :class="{ 'prose-invert text-white': blok.invert, 'text-center': blok?.text_center }"></div>
    </div>
    <div>
      <form @submit.prevent="handleSubmit" method="POST" action="/success" class="mx-auto max-w-xl" :name="blok.name" netlify>
        <input type="hidden" name="form-name" :value="blok.name" />
        <div class="grid grid-cols-1 gap-x-8 gap-y-6 sm:grid-cols-2">
          <div>
            <label for="first-name" class="block text-sm font-semibold leading-6 text-gray-800 dark:text-gray-100" :style="{color: blok?.text_color?.color}">First name</label>
            <div class="mt-2.5">
              <input required type="text" name="first-name" id="first-name" autocomplete="given-name" v-model="first_name"
                class="form-input" />
            </div>
          </div>
          <div>
            <label for="last-name" class="block text-sm font-semibold leading-6 text-gray-800 dark:text-gray-100" :style="{color: blok?.text_color?.color}">Last name</label>
            <div class="mt-2.5">
              <input required type="text" name="last-name" id="last-name" autocomplete="family-name" v-model="last_name"
                class="form-input" />
            </div>
          </div>
          <div class="sm:col-span-2">
            <label for="email" class="block text-sm font-semibold leading-6 text-gray-800 dark:text-gray-100" :style="{color: blok?.text_color?.color}">Email</label>
            <div class="mt-2.5">
              <input required type="email" name="email" id="email" autocomplete="email" v-model="email"
                class="form-input" />
            </div>
          </div>
          <div class="sm:col-span-2">
            <label for="phone-number" class="block text-sm font-semibold leading-6 text-gray-800 dark:text-gray-100" :style="{color: blok?.text_color?.color}">Phone number</label>
            <div class="mt-2.5">
              <input required type="text" name="phone-number" id="phone-number" autocomplete="tel" v-model="phone"
                class="form-input" />
            </div>
          </div>
          <div class="sm:col-span-2">
            <label for="message" class="block text-sm font-semibold leading-6 text-gray-800 dark:text-gray-100" :style="{color: blok?.text_color?.color}">Message</label>
            <div class="mt-2.5">
              <textarea name="message" id="message" rows="4" v-model="message"
                class="form-input" />
            </div>
          </div>
          <SwitchGroup as="div" class="flex gap-x-4 sm:col-span-2">
            <div class="flex h-6 items-center">
              <Switch v-model="agreed"
                :class="[agreed ? 'bg-secondary' : 'bg-gray-200', 'flex w-8 flex-none cursor-pointer rounded-full p-px ring-1 ring-inset ring-gray-900/5 transition-colors duration-200 ease-in-out focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600']"
                :style="computedStyle"
                >
                <span class="sr-only">Agree to policies</span>
                <span aria-hidden="true"
                  :class="[agreed ? 'translate-x-3.5' : 'translate-x-0', 'h-4 w-4 transform rounded-full bg-white shadow-sm ring-1 ring-gray-900/5 transition duration-200 ease-in-out']" />
              </Switch>
            </div>
            <SwitchLabel class="text-sm leading-6 text-gray-600 dark:text-gray-200" :style="{color: blok?.text_color?.color}">
              By selecting this, you agree to our
              {{ ' ' }}
              <NuxtLink 
                to="/privacy-policy" 
                class="font-semibold text-secondary" 
                :style="{color: blok?.privacy_policy?.color}"
              >
                  privacy&nbsp;policy
              </NuxtLink>.
            </SwitchLabel>
          </SwitchGroup>
        </div>
        <div class="mt-10 w-full">
          <button 
            type="submit" 
            class="w-full btn py-2 border-none hover:opacity-75 bg-secondary" 
            :style="{backgroundColor: blok?.btn_color?.color, color:blok?.btn_text_color?.color, borderRadius: blok.style}"
          >
            Get in touch
          </button>
        </div>
      </form>
    </div>
  </section>
</template>