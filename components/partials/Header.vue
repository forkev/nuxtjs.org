<template>
  <div class="absolute">
    <!-- Common Header -->
    <header class="header bg-light-elevatedSurface dark:bg-dark-elevatedSurface border-b border-light-border dark:border-dark-border lg:border-0 h-16 lg:h-24 transition-colors duration-300 ease-linear">
      <template v-if="action === ''">
        <div class="container mx-auto px-4 flex items-center lg:py-6 h-full">
          <!-- Left Title -->
          <a class="inline-block text-nuxt-gray h-7 lg:h-10 z-10 mr-auto" href="/" @click.prevent="$router.push('/')" @click.right.stop.prevent="$router.push('/design')">
            <h1 class="m-0 h-0 w-0 overflow-hidden">NUXTJS</h1>
            <nui-logo class="h-6 lg:h-8 text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary transition-colors duration-300 ease-linear" />
          </a>
          <!-- Center Navigation -->
          <ul class="hidden lg:flex lg:pt-1 xl:pt-0 text-center mx-auto">
            <li v-for="link in links" :key="link" class="header_nav_link xl:px-4 lg:py-0 lg:px-2 py-2">
              <nuxt-link class="block p-2 font-medium uppercase hover:no-underline hover:text-nuxt-lightgreen text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary transition-colors duration-300 ease-linear" :to="{ name: 'section-slug', params: { section: link } }">
                {{ $store.state.lang.links[link] || link }}
              </nuxt-link>
            </li>
            <li class="header_nav_link xl:px-4 lg:py-0 lg:px-2 py-2">
              <nuxt-link class="block p-2 font-medium uppercase hover:no-underline hover:text-nuxt-lightgreen text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary transition-colors duration-300 ease-linear" :to="{ name: 'resources' }">
                {{ $store.state.lang.links['resources'] || 'resources' }}
              </nuxt-link>
            </li>
          </ul>
          <!-- Right Action -->
          <button class="block md:hidden flex p-2 -m-2 mr-4 items-center justify-center text-light-onSurfaceSecondary dark:text-dark-onSurfaceSecondary hover:text-nuxt-lightgreen z-10 lg:hidden transition-colors duration-300 ease-linear" @click.prevent="$emit('change', 'search')">
            <nui-search-icon class="block h-5 fill-current" />
          </button>
          <nui-search class="hidden md:inline-block align-middle mr-2"/>
          <nui-select v-model="currentLang" mode="slim" :options="locales" class="inline-block align-middle">
            <template v-slot:icon>
              <nui-globe />
            </template>
          </nui-select>
        </div>
      </template>
      <nui-container v-else class="flex justify-between lg:hidden h-full items-center">
        <!-- Left Title -->
        <nui-search v-if="action === 'search'" class="w-full pr-4"/>
        <div v-else class="flex items-end">
          <component :is="'nui-' + action + '-icon'" class="block h-6 text-nuxt-lightgreen fill-current" />
          <span class="block text-lg font-medium uppercase text-nuxt-gray pl-4 h-6">{{ $store.state.lang.links[action] || action }}</span>
        </div>
        <!-- Right Action -->
        <button class="block flex p-2 -m-2 items-center justify-center text-light-onSurfaceSecondary dark:text-dark-onSurfaceSecondary hover:text-nuxt-lightgreen z-10 lg:hidden transition-colors duration-300 ease-linear" :class="action === 'search' ? 'pt-3' : ''" @click.prevent="$emit('change', '')">
          <nui-times-icon class="block h-5 fill-current"/>
        </button>
      </nui-container>
    </header>
    <!-- Mobile Main Navigation -->
    <nav class="header_mobile_nav fixed h-16 bg-light-elevatedSurface dark:bg-dark-elevatedSurface border-t left-0 bottom-0 right-0 z-30 border-light-border dark:border-dark-border block lg:hidden transition-colors duration-300 ease-linear">
      <div class="flex justify-between items-center h-full">
        <nuxt-link v-for="link in links" :key="link" class="block md:flex md:justify-center w-full p-2 md:p-4 text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary hover:no-underline hover:text-primary-base dark:hover:text-primary-base text-center visited:text-nuxt-gray transition-colors duration-300 ease-linear" :to="{ name: 'section-slug', params: { section: link } }" @click.prevent.native="$emit('change', action === link ? '' : ($route.params.section !== link ? '' : link))">
          <component :is="'nui-' + link + '-icon'" class="inline-block h-5 fill-current mb-1" :class="{'text-nuxt-lightgreen': action === link}"/>
          <span class="block text-xs md:text-base md:pl-3 font-medium text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary transition-colors duration-300 ease-linear">{{ $store.state.lang.links[link] || link }}</span>
        </nuxt-link>
        <nuxt-link class="block md:flex md:justify-center w-full p-2 md:p-4 text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary hover:no-underline hover:text-primary-base dark:hover:text-primary-base text-center visited:text-nuxt-gray transition-colors duration-300 ease-linear" :to="{ name: 'resources' }">
          <nui-resources-icon class="inline-block h-5 fill-current mb-1"/>
          <span class="block text-xs md:text-base md:pl-3 font-medium text-light-onSurfacePrimary dark:text-dark-onSurfacePrimary transition-colors duration-300 ease-linear">{{ $store.state.lang.links['resources'] || 'resources' }}</span>
        </nuxt-link>
      </div>
    </nav>
    <!-- Mobile Aside Navigation -->
    <div class="header_mobile_aside block lg:hidden fixed left-0 light:bg-light-surface dark:bg-dark-surface pt-6 z-20 w-full overflow-y-auto transition-colors duration-300 ease-linear" :class="{'header_mobile_aside--open': sublinks.length}">
      <nui-container>
        <transition-group v-for="(group, index) in sublinks" :key="index" tag="div" name="list" class="header_mobile_aside_group">
          <h3 :key="`title-${index}`" class="uppercase text-gray-500 pb-2">
            {{ group.title }}
          </h3>
          <ul :key="`list-${index}`" class="pb-6">
            <li v-for="l in group.links" :key="l.to" class="py-2">
              <nuxt-link class="block dark:text-dark-onSurfacePrimary hover:text-nuxt-lightgreen transition-colors duration-300 ease-linear" :class="{'text-nuxt-lightgreen': path === locale + l.to}" :to="locale + l.to" exact>
                {{ l.name }}
              </nuxt-link>
            </li>
          </ul>
        </transition-group>
      </nui-container>
    </div>
  </div>
</template>

<script>
import nuiLogo from '@/components/svg/Nuxtjs'
import nuiGlobe from '@/components/svg/Globe'
import nuiExamplesIcon from '@/components/svg/Code'
import nuiGuideIcon from '@/components/svg/Books'
import nuiApiIcon from '@/components/svg/List'
import nuiFaqIcon from '@/components/svg/Faq'
import nuiResourcesIcon from '@/components/svg/Resources'
import nuiTimesIcon from '@/components/svg/Times'
import nuiSearchIcon from '@/components/svg/Search'
import nuiSearch from '@/components/partials/Search'
import nuiArrowLeft from '@/components/svg/ArrowLeft'
import localeManager from '@/mixins/localeManager'

export default {
  components: {
    nuiTimesIcon,
    nuiExamplesIcon,
    nuiGuideIcon,
    nuiApiIcon,
    nuiFaqIcon,
    nuiResourcesIcon,
    nuiLogo,
    nuiSearchIcon,
    nuiSearch,
    nuiArrowLeft,
    nuiGlobe
  },
  mixins: [
    localeManager
  ],
  model: {
    prop: 'action',
    event: 'change'
  },
  props: {
    action: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      links: ['guide', 'api', 'examples', 'faq']
    }
  },
  computed: {
    path () { return this.$route.path.slice(-1) === '/' ? this.$route.path.slice(0, -1) : this.$route.path },
    locale () { return '/' + this.action },
    sublinks () { return this.$store.state.menu[this.action] || [] }
  },
  methods: {
    nav (section) {
      this.currentSection = (this.currentSection === section ? '' : section)
      this.mobileNav = !this.mobileNav
    }
  }
}
</script>

<style>
.header {
  @apply fixed top-0 left-0 right-0 z-30;
}
.header_nav_link a.nuxt-link-active {
    @apply text-nuxt-lightgreen;
}
/*.header_mobile_nav a {
  padding-bottom: max(0.5rem,env(safe-area-inset-bottom));
}*/
.header_mobile_aside {
  top: 72px;
  bottom: 60px;
  transform: translateX(-100%);
  transition-property: transform;
  transition-duration: 1s;
  transition-timing-function: cubic-bezier(0.23, 1, 0.32, 1);
  /* transition-delay: .5s; */
  & .header_mobile_aside_group {
    transform: translateX(-100%);
    transition-property: transform;
    transition-duration: 0.3s;
    transition-timing-function: ease-in-out;
    &:nth-child(1) { transition-delay: 0.2s; }
    &:nth-child(2) { transition-delay: 0.3s; }
    &:nth-child(3) { transition-delay: 0.4s; }
    &:nth-child(4) { transition-delay: 0.5s; }
  }
}
.header_mobile_aside--open {
  transform: translateX(0px);
  transition-delay: 0s;
  & .header_mobile_aside_group {
    transform: translateX(0px);
  }
}
.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
