<style lang="stylus" scoped>
hr
  border: none
  border-top: 1px solid #dd
  padding-bottom: 15px

footer
  text-align: center
  a
    display: block
    margin: 0 auto
    padding: 20px 0 0 0
    color: #666
    font-weight: bold
    font-decoration: none
    &:hover
      font-decoration: underline
</style>

<template lang="jade">
article.form(transition='slide')
  header
    button.plain.back(@click='back') < BACK
    h1 Watcher configuration
  form
    p.
      {{appName}} uses a special daemon called #[strong Watcher] to monitor your servers, send alerts and enforce smart policies.
    hr
    p
      strong Have you already installed and set up {{appName}} Watcher in your server?
  footer
    button(v-link="{ path: '/wizard/watcherGuide' }") Not yet, please show me how
    a(v-link="{ path: '/wizard/import' }") Yes, I have
</template>

<script lang="coffee">
app = document.app
module.exports =
  data: app.data
  methods:
    back: ->
      app.router.go '/wizard/export'
</script>
