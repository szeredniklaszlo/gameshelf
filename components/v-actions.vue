<template>
  <b-container
    class="actions"
    fluid
  >
    <b-row>
      <b-col sm="auto">
        <b-button
          v-clipboard="getShareLink()"
          size="sm"
          variant="primary"
          @click="$toast.success('Link kimásolva', { icon : 'fa-clipboard'})"
        >
          <i
            class="fa fa-share-alt"
            aria-hidden="true"
          />
          Lista másolása vágólapra
        </b-button>
      </b-col>
      <b-col sm="auto">
        <b-button
          size="sm"
          variant="primary"
          @click="getARandomGame()"
        >
          <i
            class="fa fa-random"
            aria-hidden="true"
          />
          Random játék választása
        </b-button>
      </b-col>
      <b-col sm="auto">
        <b-button
          v-clipboard="getList()"
          size="sm"
          variant="primary"
          @click="$toast.success('Lista kimásolva', { icon : 'fa-clipboard'})"
        >
          <i
            class="fa fa-copy"
            aria-hidden="true"
          />
          Lista másolása vágólapra
        </b-button>
      </b-col>
      <b-col sm="auto">
        <b-button
          size="sm"
          variant="primary"
          @click="toggleListView()"
        >
          <span v-if="views.listView">
            <i
              class="fa fa-th"
              aria-hidden="true"
            />
            Rács nézet
          </span>
          <span v-if="!views.listView">
            <i
              class="fa fa-list"
              aria-hidden="true"
            />
            Lista nézet
          </span>
        </b-button>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>

import cookie from '~/components/cookie'
import filterItems from '~/components/filterItems.js'
import params from '~/components/params.js'
import _ from 'lodash'

export default {
  props: {
    games: { type: Object, required: true }
  },
  data () {
    return {
      getShareLink: function () {
        let link = `${window.location.origin}${window.location.pathname}?userId=${cookie.get('username')}&`
        const { filters } = this.$store.state
        const queryParams = params.map(param => (filters[param] ? `${param}=${filters[param]}` : null)).filter(i => !!i).join('&')
        return encodeURI(`${link}${queryParams}`)
      },
      getList: function () {
        let result = ''
        _.forEach(this.games, game => {
          result += game.name + (game.comment != undefined ? ' - ' + game.comment : '') + '\n'
        })
        return result
      },
      getARandomGame: function () {
        let game = _.sample(filterItems(this.games, this.$store.state.filters))
        this.$toast.success('Játssz ezzel: ' + game.name, {
          icon: 'fa-play',
          action: {
            text: 'Link',
            href: 'https://boardgamegeek.com/boardgame/' + game.id
          },
          duration: 5000
        })
      },
      views: this.$store.state.views
    }
  },
  methods: {
    toggleListView () {
      this.$store.commit('views/toggleListView')
    }
  }
}
</script>

<style>
.actions {
  margin-bottom: 1rem;
}
</style>
