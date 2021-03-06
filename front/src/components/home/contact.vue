<template>
    <transition name="modal">
        <div class="modal__mask" @click="$emit('close')">
            <div class="modal__wrapper">
                <table class="modal__container" @click="preventClose">
                    <tr><td>
                        <input v-model="email"
                               v-bind:disabled="isLocked"
                               v-bind:class="{ error: isEmailError }"
                               v-on:input="isEmailError = false"
                               v-on:change="isEmailError = false"
                               class="field email" type="email" placeholder="Your email" ref="email"
                               spellcheck="false"
                        />
                    </td></tr>
                    <tr><td>
                        <textarea v-model="message"
                                  v-bind:disabled="isLocked"
                                  class="field message" placeholder="Message"></textarea>
                    </td></tr>
                    <tr><td>
                        <div v-bind:class="{ touch: isTouch }" @click="send" class="modal__button" >
                            <div v-bind:class="{ active: isSent }" class="modal__button__blinder">
                                <div class="modal__button__message">sent</div>
                            </div>
                            send
                        </div>
                    </td></tr>
                </table>
            </div>
        </div>
    </transition>
</template>

<script>
import * as axios from 'axios'

export default {
    props: ['isTouch'],
    data () {
        return {
            isEmailError: false,
            isLocked: false,
            isSent: false,
            email: '',
            message: ''
        }
    },

    mounted () {
        this.$refs.email.focus()
    },

    methods: {
        send: function (e) {
            if (this.isSent) {
                return
            }
            e.isSendEvent = true
            if (!/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@(([[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(this.email)) {
                this.isEmailError = true
                this.$refs.email.focus()
                return
            }
            this.isEmailError = false
            this.isLocked = true
            axios.post('/api/contact', {
                email: this.email,
                message: this.message
            })
            this.isSent = true
        },

        preventClose: function (e) {
            if (!this.isSent || e.isSendEvent) {
                e.stopPropagation()
            }
        }
    }
}

</script>

<style scoped lang="sass">
    @import "colors"
    @import "mixins"

    .modal__mask
        position: fixed
        z-index: 9998
        top: 0
        left: 0
        width: 100vw
        height: 100vh
        background-color: rgba(0, 0, 0, .5)
        display: table
        transition: opacity .1s ease

    .modal__wrapper
        display: table-cell
        vertical-align: middle

    .modal__container
        width: 100%
        max-width: 700px
        margin: 0 auto
        padding: 10px 20px 10px 20px
        background-color: white
        color: $c-background
        border-radius: 1px
        box-shadow: 0 2px 8px rgba(0, 0, 0, .33)
        transition: all .1s ease
        @include breakpoint(xs)
            padding: 15px 50px 15px 50px
        @include breakpoint(md)
            padding: 30px 100px 30px 100px
        td
            padding: 2px

    .modal-enter
        opacity: 0

    .modal-leave-active
        opacity: 0

    .modal-enter .modal__container,
    .modal-leave-active .modal__container
        -webkit-transform: scale(.95)
        transform: scale(.95)

    .field
        width: 100%
        box-sizing: border-box
        resize: none
        outline: 0 transparent
        background: $c-background
        border: none
        color: white
        padding: 5px
        border-radius: 1px
        font-size: 10px
        &::-webkit-input-placeholder
            color: $c-text
        &:-ms-input-placeholder
            color: $c-text
        &::-moz-placeholder
            color: $c-text
        &:-moz-placeholder
            color: $c-text
        @include breakpoint(xs)
            font-size: 15px
            padding: 10px
        @include breakpoint(md)
            font-size: 14px
            padding: 10px

    .email
        border: 3px solid transparent
        transition: border .2s
        &.error
            border: 3px solid #c70000

    .message
        height: 300px

    .modal__button
        display: block
        position: relative
        text-align: center
        cursor: pointer
        padding: 5px
        border: $c-background 1px solid
        -webkit-tap-highlight-color: transparent
        font-size: 15px
        @include breakpoint(xs)
            font-size: 20px
        @include breakpoint(md)
            font-size: 25px
        &:not(:hover)
            transition: all 0.2s
        &:hover, &.touch
            background: $c-background
            color: $c-text
        &:active
            background: darken($c-background, 10%)
            color: $c-text

    .modal__button__blinder
        position: absolute
        width: 100%
        top: 0
        left: 0
        height: 0
        background: white
        color: $c-background
        overflow: hidden
        transition: height .2s
        cursor: default
        &.active
            height: 100%

    .modal__button__message
        padding: 5px

</style>

