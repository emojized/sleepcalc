<template>
  <div>
    <b-navbar class="is-info">
      <template #brand>
        <NuxtLink
          class="navbar-item"
          :to="localePath('/')"
        >
          <img
            src="~assets/logo.svg"
            alt="sleepcalc"
            height="28"
            width="28"
          >

          <h1 class="title is-3 has-text-white">Sleepcalc.one</h1>
        </NuxtLink>
      </template>
      <template #start>
        <NuxtLink  :to="localePath('/about')"  class="navbar-item">
          {{ $t('about') }}
        </NuxtLink>
      </template>

      <template #end>
        <b-navbar-dropdown :label="$t('choose_language')">
          <NuxtLink :to="switchLocalePath('en')" class="navbar-item">{{$t('language.english')}}</NuxtLink>
          <NuxtLink :to="switchLocalePath('de')" class="navbar-item">{{$t('language.german')}}</NuxtLink>
        </b-navbar-dropdown>
      </template> 
    </b-navbar>

    <section class="main-content">
      <Nuxt />
    </section>
  </div>
</template>

<script>
export default {
}
</script>
