<template>
  <b-container
    class="filters"
    fluid
  >
    <b-row>
      <b-col>
        <b-btn
          v-b-toggle.collapse1
          variant="outline-primary"
          size="sm"
        >
          Szűrők megjelenítése
        </b-btn>
      </b-col>
    </b-row>
    <b-collapse
      id="collapse1"
      visible
    >
      <b-form-group
        label-cols-sm="2"
        label="Játékosok száma"
      >
        <b-row>
          <b-col sm="auto">
            <b-form-input
              v-model="filters.bestnum"
              type="number"
              placeholder="Legjobb ennyien"
              min="1"
              size="sm"
            />
          </b-col>
          <b-col sm="auto">
            <b-form-input
              v-model="filters.recnum"
              type="number"
              placeholder="Ajánlott ennyien"
              min="1"
              size="sm"
            />
          </b-col>
          <b-col sm="auto">
            <b-form-input
              v-model="filters.supplayer"
              type="number"
              placeholder="Lehetséges ennyien"
              min="1"
              size="sm"
            />
          </b-col>
        </b-row>
      </b-form-group>
      <b-form-group
        label-cols-sm="2"
        label="Idő"
      >
        <b-row>
          <b-col sm="auto">
            <b-form-input
              v-model="filters.mintime"
              type="number"
              placeholder="Minimum"
              min="0"
              step="10"
              size="sm"
            />
          </b-col>
          <b-col sm="auto">
            <b-form-input
              v-model="filters.maxtime"
              type="number"
              placeholder="Maximum"
              min="0"
              step="10"
              size="sm"
            />
          </b-col>
        </b-row>
      </b-form-group>
      <b-row>
        <b-col sm="auto">
          <b-button
            :id="'mech-filter'"
            size="sm"
            variant="primary"
          >
            <i
              class="fa fa-gear"
              aria-hidden="true"
            />
            Szűrés mechanizmusok által
          </b-button>
          <b-popover
            :target="'mech-filter'"
            :placement="'bottom'"
            triggers="click"
            :show.sync="popoverShow"
            :content="`Placement`"
          >
            <b-tabs>
              <b-tab
                title="Show"
                active
              >
                <b-form-group>
                  <b-form-checkbox-group
                    v-model="filters.mechShow"
                    name="mechanisms"
                    :options="mechOptions"
                  />
                </b-form-group>
              </b-tab>
              <b-tab title="Hide">
                <b-form-group>
                  <b-form-checkbox-group
                    v-model="filters.mechHide"
                    name="mechanisms"
                    :options="mechOptions"
                  />
                </b-form-group>
              </b-tab>
            </b-tabs>
            <b-btn
              size="sm"
              variant="primary"
              @click="onClose"
            >
              Bezár
            </b-btn>
          </b-popover>
        </b-col>
        <b-col
          v-if="showOwned"
          sm="auto"
        >
          <b-button
            size="sm"
            variant="primary"
            @click="ownedgames = !ownedgames"
          >
            <span v-if="ownedgames">
              <i
                class="fa fa-users"
                aria-hidden="true"
              />
              Show All Games
            </span>
            <span v-if="!ownedgames">
              <i
                class="fa fa-user"
                aria-hidden="true"
              />
              Show Only Owned Games
            </span>
          </b-button>
        </b-col>
      </b-row>
    </b-collapse>
  </b-container>
</template>

<script>

import { mapState } from 'vuex'
import cookie from '~/components/cookie.js'
import params from '~/components/params.js'
const mechKeys = require('~/assets/mechKey.json')

export default {
  props: {
    bestnum: {
      default: 0,
      type: Number
    },
    maxtime: {
      default: 0,
      type: Number
    },
    maxweight: {
      default: 0,
      type: Number
    },
    minweight: {
      default: 0,
      type: Number
    },
    mintime: {
      default: 0,
      type: Number
    },
    mechShow: {
      default: () => [],
      type: Array
    },
    mechHide: {
      default: () => [],
      type: Array
    },
    ownedgames: {
      type: Boolean,
      required: true
    },
    playgreaterthan: {
      default: 0,
      type: Number
    },
    playlessthan: {
      default: 0,
      type: Number
    },
    recnum: {
      default: 0,
      type: Number
    },
    showexp: {
      default: cookie.get('showexp') == null ? true : cookie.get('showexp'),
      type: Boolean
    },
    showOwned: {
      type: Boolean
    },
    supplayer: {
      default: 0,
      type: Number
    }
  },
  data () {
    return {
      mechOptions: this.getMechOptions(),
      popoverShow: false
    }
  },
  computed: {
    ...mapState([
      'filters'
    ]),
    _filters: function () {
      return params.reduce((acc, val) => {
        acc[val] = this[val]
        return acc
      }, {})
    }
  },
  watch: {
    _filters: function (filters) {
      this.filters = filters
      this.$store.commit('filters/set', filters)
    }
  },
  methods: {
    getMechOptions: function () {
      return Object.keys(mechKeys).map(key => ({
        text: mechKeys[key],
        value: mechKeys[key]
      }))
    },
    onClose () {
      this.popoverShow = false
    }
  }
}
</script>

<style>
.filters.container-fluid {
  text-align: left;
  margin-bottom: .5rem;
}
</style>
