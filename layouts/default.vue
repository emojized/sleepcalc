<template>
  <div>
    <nav
      class="navbar header has-shadow is-info"
      role="navigation"
      aria-label="main navigation"
    >
      <div class="navbar-brand">
        <a
          class="navbar-item"
          href="/"
        >
          <img
            src="~assets/logo.svg"
            alt="sleepcalc"
            height="28"
            width="28"
          >

          <h1 class="title is-3 has-text-white">Sleepcalc.one</h1>
        </a>
      </div>
    </nav>

    <section class="main-content">
      <Nuxt />
    </section>
  </div>
</template>

<script>
export default {
}
</script>
