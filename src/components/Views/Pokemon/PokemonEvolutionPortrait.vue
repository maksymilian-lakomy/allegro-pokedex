<template>
    <div
        class="pokemon-evolution-portrait"
        :class="{'pokemon-evolution-portrait--main': isMain}"
        @mouseenter="active = true"
        @mouseleave="active = false"
        @click="$emit('click', $event)"
    >
        <img
            class="pokemon-evolution-portrait__img"
            :class="{'pokemon-evolution-portrait__img--active': active && !isMain,
                'pokemon-evolution-portrait__img--placeholder': !hasSprite,
                'pokemon-evolution-portrait__img--main': isMain}"
            :src="hasSprite ? pokemonData.sprites.frontDefault : require('@/assets/pokemon-placeholder.png')"
        />
        <transition name="slide">
        <div class="pokemon-evolution-portrait__label" v-show="active && !isMain">
            {{pokemonData.name | name}}
        </div>
        </transition>
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import { Prop, Mixins } from "vue-property-decorator";
import PokemonData from "@/classes/PokemonData";
import { StringFilters } from '@/mixins/StringFilters';

@Component
export default class PokemonEvolutionPortrait extends Mixins(StringFilters) {
    @Prop(PokemonData)
    readonly pokemonData!: PokemonData;

    @Prop({
        type: Boolean,
        default: false
    })
    readonly isMain!: boolean;

    active = false;

    get hasSprite() {
        if (this.pokemonData.sprites.frontDefault) return true;
        return false;
    }
}
</script>

<style lang="sass" scoped>
@import "@/styles/variables"

.pokemon-evolution-portrait
    border-radius: 50%
    width: 100%
    cursor: pointer
    position: relative

    &__img
        opacity: $portrait-not-active-opacity
        width: 100%
        transition-duration: .25s

    &__img--placeholder
        opacity: $placeholder-opacity !important

    &__img--active
        opacity: 1
        transform: translateY(-.25em)

    &__img--main
        opacity: 1

    &__label
        font-size: .8em
        background-color: $light-gray
        color: $darkest-gray
        width: 100%
        border-radius: .5em
        padding: .25em 0
        font-weight: bolder
        text-align: center
        position: absolute
        bottom: 0

.pokemon-evolution-portrait--main
    background-color: $light-gray
    cursor: default !important

.slide-enter-active, .slide-leave-active 
    transition-duration: .25s
    transform: translateY(0em)
    opacity: 1

.slide-enter, .slide-leave-to
    transform: translateY(.25em)
    opacity: 0
</style>