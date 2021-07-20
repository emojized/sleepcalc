<template>
  <div>
    <b-navbar class="is-info">
      <template #brand>
        <NuxtLink
          class="navbar-item"
          to="/"
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
        <NuxtLink  to="/about"  class="navbar-item">
          About
        </NuxtLink>
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
