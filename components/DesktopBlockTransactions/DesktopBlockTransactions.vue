<template>
  <div class="desktop-block-transactions">
    <h2 class="desktop-heading">{{ $t('transactions') }}</h2>
    <TableLoader v-if="loading" :count="10" />
    <table v-else class="table">
      <thead class="table__head">
        <tr class="table__head-row">
          <th class="table__title text_align_left">{{ $t('type') }}</th>
          <th class="table__title text_align_left">{{ $t('created') }}</th>
          <th class="table__title text_align_left">{{ $t('hash') }}</th>
        </tr>
      </thead>
      <tbody class="table__body">
        <DesktopTx v-for="tx in transactions" :key="tx.id" :tx="tx" />
      </tbody>
    </table>
    <div v-if="transactions.length > 0" class="page-navigation">
      <div class="page-navigation__info">
        {{ $t('showing') }} {{ from }} {{ $t('to') }} {{ to }} {{ $t('of') }}
        {{ txCount | commaNumber }}
      </div>
      <div class="page-navigation__pagination">
        <Pagination :page="prevPage" type="prev" @click.native="getBlockTransactions(prevPage)" />
        <Pagination :page="nextPage" type="next" @click.native="getBlockTransactions(nextPage)" />
      </div>
    </div>
  </div>
</template>

<style lang="scss">
@import './DesktopBlockTransactions.scss';
</style>

<script>
import DesktopTx from '~/components/DesktopTx/DesktopTx'
import TableLoader from '~/components/Loaders/TableLoader'
import Pagination from '~/components/Pagination/Pagination'

export default {
  components: { TableLoader, DesktopTx, Pagination },
  props: {
    blockId: {
      type: Number,
      default: 0
    },
    txCount: {
      type: Number,
      default: 0
    }
  },
  data: () => {
    return {
      transactions: [],
      loading: true,
      nextPage: null,
      prevPage: null,
      currentPage: 1,
      from: 0,
      to: 0
    }
  },
  mounted () {
    this.getBlockTransactions(this.currentPage)
  },
  methods: {
    getBlockTransactions (page) {
      const self = this

      // Checking if page exists
      if (page === null) {
        return false
      }

      self.loading = true

      // Disabling pagination untill data fetched
      self.nextPage = null
      self.prevPage = null

      // Fetcing data
      this.$axios
        .$get(`blocks/${this.blockId}/transactions?page=${page}`)
        .then(function (response) {
          const {
            data,
            currentPage,
            prevPageUrl,
            nextPageUrl,
            from,
            to
          } = response

          self.transactions = data

          self.from = from
          self.to = to
          self.currentPage = currentPage
          self.prevPage = prevPageUrl != null ? self.currentPage - 1 : null
          self.nextPage = nextPageUrl != null ? self.currentPage + 1 : null

          self.loading = false
        })
    }
  }
}
</script>
