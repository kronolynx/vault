<style lang="stylus" scoped>
article
  &.generating
    width: 300px
    height: 300px
    top: 50vh
    left: 50vw
    margin: -150px
    p
      margin: 0
      text-align: center

  input[name=pass]
    width: calc(100% - 170px)

  button.unhide
    float: right
    position: absolute
    top: 35px
    right: 10px
    opacity: .5
    &.show
      opacity: 1
</style>

<template lang="jade">
article.generating(v-show='generating', transition='pop')
  div.loader Loading...
  p
    strong Generating keypair...
  p
    small (It may take up to 1 minute)

article.form(v-else, transition='slide')
  header
    h1 PGP keypar generation
  form(@keyup.enter='submit')
    p Please choose a safe password or passphrase for protecting your keys:
    p.error(v-if='error') {{error}}
    fieldset.pass
      label(for='pass') Passphrase
      input(v-if='show', type='text', v-model='pass')
      input(v-else, name='pass', type='password', v-model='pass')
      button.plain.unhide(@click='unhide', v-bind:class="{'show': show}")
        img(src='/img/eye.svg')
  footer
    button.next(v-show='pass', @click='submit') Next
</template>

<script lang="coffee">
app = document.app
module.exports =
  data: ->
    $.extend app.data(),
      generating: false
      pass: null
      show: false
      error: false
  methods:
    run: ->
      if app.settings.keys
        @next()
    submit: (e) ->
      e.preventDefault()
      if @pass
        @generating = true
        @genKeys()
      else
        @error = 'Please make sure you set a passphrase'
    genKeys: ->
      app.pgp.generateKey(
        userIds: [@identikit()]
        numBits: 4096
        passphrase: @pass
      ).then (keys) =>
        app.settings.keys =
          priv: keys.privateKeyArmored
          pub: keys.publicKeyArmored
          fingerprint: keys.key.primaryKey.fingerprint
        document.vault.updateFingerprint app.settings.keys.fingerprint
        app.privateKey = keys.key
        app.privateKey.decrypt @pass
        app.save()
        @next()
    identikit: ->
      try
        user = os.homedir().split('/').pop()
        host = os.hostname()
      catch e
        user = 'webuser'
        host = "#{navigator.appCodeName}.#{navigator.appName}".toLowerCase()
      {name: user, email: "#{user}@#{host}.local"}
    unhide: (e) ->
      e.preventDefault()
      @show = !@show
    next: ->
      @generating = false
      console.log app.settings.keys
      app.router.replace '/wizard/export'
</script>
