<template>
  <section class="container">
    <div>
      <v-loader v-if="loading && !error" />
      <b-container
        v-if="!loading && !error"
        class="bv-example-row"
      >
        <b-row>
          <b-col>
            <v-filters ownedgames />
            <v-actions :games="items" />
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <v-table
              v-if="views.listView"
              :default-asc="true"
              :games="items"
              :headers="tableHeader"
            />
            <v-grid
              v-if="!views.listView"
              :games="items"
            />
          </b-col>
        </b-row>
      </b-container>
      <v-refresh
        v-if="error"
        :message="errorMessage"
      />
    </div>
  </section>
</template>

<script>
import cookie from '~/components/cookie.js'
import filterItems from '~/components/filterItems.js'
import VFilters from '~/components/v-filters.vue'
import VActions from '~/components/v-actions.vue'
import VGrid from '~/components/v-grid.vue'
import VLoader from '~/components/v-loader.vue'
import VRefresh from '~/components/v-refresh.vue'
import VTable from '~/components/v-table.vue'
import { mapActions, mapState } from 'vuex'
import _ from 'lodash'

export default {
  components: {
    VGrid,
    VLoader,
    VRefresh,
    VTable,
    VActions,
    VFilters
  },
  data () {
    return {
      tableHeader: [
        { key: '', value: '', hide: this.$route.query.noimage },
        { key: 'name', value: 'Név' },
        { key: 'playingtime', value: 'Idő' },
        { key: 'minplayer', value: 'Min. játékos' },
        { key: 'maxplayer', value: 'Max. játékos' },
        { key: 'bggbestplayers', value: 'Legjobb ennyien' },
        { key: 'average', value: 'Átl. Értékelés' },
        { key: 'rank', value: 'Rangsor' },
        { key: 'mech', value: 'Mechanizmusok' }
      ],
      userId: cookie.get('username')
    }
  },
  computed: mapState({
    items: state => _.pickBy(state.items['index'], ['own', true]),
    loading: state => state.pageState['index'] ? !state.pageState['index'].loaded : true,
    error: state => state.pageState['index'] ? state.pageState['index'].error : null,
    errorMessage: state => state.pageState['index'] ? state.pageState['index'].errorMessage : null,
    views: state => state.views
  }),
  beforeCreate: function () {
    if (this.$route.query.userId) {
      cookie.set('username', this.$route.query.userId)
    } else if (this.$route.query.userid) {
      cookie.set('username', this.$route.query.userid)
    } else if (!cookie.get('username')) {
      cookie.set('username', 'Joacoking')
    }

    if (this.$route.query.showexp) {
      cookie.set('showexp', true)
    } else if (cookie.get('showexp') === '') {
      cookie.set('showexp', true)
    }

    if (this.$route.query.disableLS) {
      cookie.set('disableLS', true)
    } else if (cookie.get('disableLS') === '') {
      cookie.set('disableLS', false)
    }
  },
  created: function () {
    this.$store.commit('filters/reset', this.$route.query)
    this.$store.commit('filters/setOwned', true)
    const userIds = this.$route.query.userId || this.userId
    this.fetch({ userIds, page: 'index', disableLS: cookie.get('disableLS') === 'true' })
  },
  methods: {
    filteredItem: filterItems,
    ...mapActions({
      fetch: 'items/query/fetch'
    })
  }
}
</script>

<style>
.container {
  justify-content: center;
  align-items: center;
  text-align: center;
}

.filters .col-sm-auto {
  padding-bottom: 0.25rem;
}

.popover {
  max-width: 32rem;
}
</style>
