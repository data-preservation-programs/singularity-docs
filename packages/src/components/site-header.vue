<template>
  <header id="site-header">

    <!-- ============================================================== Logo -->
    <NuxtLink to="/docs" class="logo-link">
      <component :is="useResolveDoczillaComponent('site-logo')" class="logo" />
    </NuxtLink>

    <!-- ========================================================= Nav links -->
    <nav id="site-nav">
      <ButtonClear
        v-for="(link, index) in links"
        v-bind="link"
        :key="index"
        ref="navItems"
        theme="nav"
        :class="['site-nav-link', { active: link.to === routeActive }]">
        <div class="text" v-html="link.label" />
      </ButtonClear>
    </nav>

    <!-- ===================================================== Theme toggler -->
    <div id="site-toolbar">

      <ButtonThemeToggle />

      <ZeroButton
        tag="a"
        :to="githubUrl"
        target="_blank"
        :disabled="!githubUrl"
        class="github-link">
        <IconGithub />
      </ZeroButton>

      <ButtonAlgoliaSearch />

      <DropdownSelector
        v-if="languageSelectorVisible"
        :options="languageOptions" />

    </div>

  </header>
</template>

<script setup>
// ===================================================================== Imports
import { storeToRefs } from 'pinia'
import Header from '@/data/header'

// ======================================================================== Data
const generalStore = useGeneralStore()
const { languageSelectorVisible } = storeToRefs(generalStore)
const links = Header.navigation
const githubUrl = Header.toolbar.github_url
const languageOptions = Header.toolbar.language_options

const route = useRoute()
const contentPath = `/docs/content${route.path}`
const { data: content } = await useAsyncData('content', () => {
  return queryContent({
    where: {
      _path: { $contains: contentPath }
    }
  }).find()
})

const routeActive = ref(undefined)

if (content.value.length > 0) {
  routeActive.value = content.value[0]._file.includes('docs') ? '/docs' : undefined
}
</script>

<style lang="scss" scoped>
// ///////////////////////////////////////////////////////////////////// General
#site-header {
  display: flex;
  flex-direction: row;
  justify-content: space-between;;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: $siteHeaderHeight;
  padding: 0 2.5rem;
  background-color: var(--background-color);
  border-bottom: 1px solid var(--divider);
  z-index: 1000;
  transition: background-color 500ms, border-color 500ms;
}

// /////////////////////////////////////////////////////////////////// Site Logo
.logo-link {
  position: relative;
  display: block;
  z-index: 10000;
}

.logo {
  display: block;
  height: 100%;
  transition: 150ms ease-out;
  &:hover {
    transition: 150ms ease-in;
    transform: scale(1.05);
  }
}

// ////////////////////////////////////////////////////////////////// Navigation
#site-nav {
  display: flex;
  flex-direction: row;
  position: relative;
  padding-left: 2rem;
  @include large {
    padding-left: 0.25rem;
  }
}

.site-nav-link {
  display: flex;
  flex-direction: row;
  align-items: center;
  position: relative;
  @include large {
    &:not(:last-child) {
      margin-right: 2rem;
    }
  }
  &:hover {
    &:before {
      transition: 150ms ease-in;
      height: toRem(5);
    }
  }
  &:not(:last-child) {
    margin-right: 3.125rem;
  }
  &:before {
    content: '';
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 0px;
    background-color: var(--theme-color);
    transition: 150ms ease-out;
  }
  &.active {
    &:before {
      transition: 150ms ease-in;
      height: toRem(5);
    }
  }
  :deep(.text) {
    font-weight: 500;
  }
}

// ///////////////////////////////////////////////////////////////////// Toolbar
#site-toolbar {
  display: flex;
  align-items: center;
  & > * {
    &:not(:last-child) {
      margin-right: toRem(38);
    }
  }
}

.github-link {
  display: flex;
  :deep(path) {
    transition: 150ms ease-out;
    fill: var(--theme-color);
  }
}
</style>
