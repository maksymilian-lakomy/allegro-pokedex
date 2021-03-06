<template>
    <ol class="list">
        <li class="list__element pagination__element__page-number" v-if="(pages[0] > 1)">
            <button
                @click="setPage(1)"
                class="list__element__button list__element__page-number"
            >{{'1' | numeric}}</button>
        </li>
        <li class="list__element list__element__page-number" v-if="(pages[0] > 1)">. . .</li>
        <li class="list__element" v-for="i in pages" :key="i">
            <button
                @click="setPage(i)"
                class="list__element__button list__element__page-number"
                :class="{'list__element__button--active': i === currentPage}"
            >{{i.toString() | numeric}}</button>
        </li>
        <li
            class="list__element list__element__page-number list__element__page-number--count"
        >/ {{pageAmount.toString() | numeric}}</li>
    </ol>
</template>

<script lang="ts">
import Vue from 'vue';
import { Component, Prop } from 'vue-property-decorator';

import { Queries } from '@/classes/Queries';

@Component({
    filters: {
        numeric(value: string) {
            if (typeof value !== 'string') return;
            while (value.length < 2) value = `0${value}`;
            return value;
        }
    }
})
export default class BrowserPaginationn extends Vue {
    @Prop({ required: true, type: Number })
    readonly currentPage!: number;

    @Prop({ required: true, type: Number })
    readonly pageAmount!: number;

    get pages() {
        const pages: Array<number> = [];
        let upperLimit = this.currentPage + 2;
        if (upperLimit - 5 < 0) upperLimit -= upperLimit - 5;
        if (upperLimit > this.pageAmount) upperLimit = this.pageAmount;
        while (pages.length < 5 && upperLimit > 0) {
            pages.unshift(upperLimit);
            upperLimit--;
        }
        return pages;
    }

    async setPage(page: number) {
        this.queries.setQuery('p', page.toString());

        try {
            await this.$router.push({
                path: this.$route.path,
                params: this.$route.params,
                query: this.queries.queries
            });
        } catch (e) {
            return;
        }
    }

    get queries(): Queries {
        return new Queries(this.$route.query);
    }
}
</script>

<style lang="sass" scoped>
.list
    border-bottom: 1px solid #E9E9E9
    padding: 1em 0
    font-size: .8em
    margin: 0
    margin-bottom: 1em
    position: sticky
    background-color: white
    top: 0
    display: flex
    z-index: 100

    &__element
        list-style: none
        padding: 0 .5em

        &__button
            cursor: pointer
            outline: none
            border: unset
            background-color: unset
            opacity: .5
        &__button--active
            opacity: 1
        &__page-number
            font-family: inherit
            font-weight: bolder
            font-size: 1em
            color: #707070

    &__element:nth-child(1)
        padding-left: 0

</style>