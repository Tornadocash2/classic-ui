<template>
  <div class="field">
    <div class="label">
      {{ $t('total') }}
    </div>
    <div class="withdraw-data">
      <div v-if="isTokenSelected" class="withdraw-data-item">
        {{ $t('noteBalance') }}
        <span>
          {{ selectedStatistic.amount }}
          {{ currency }}
        </span>
      </div>
      <div v-if="withdrawType === 'relayer'" class="withdraw-data-item">
        {{ $t('gasPrice') }}

        <span>{{ gasPriceInGwei }} Gwei</span>
      </div>
      <div v-if="withdrawType === 'relayer'" class="withdraw-data-item">
        {{ $t('networkFee') }}
        <span data-test="label_network_fee">{{ toDecimals(networkFee, 18, 6) }} {{ networkCurrency }}</span>
      </div>
<<<<<<< HEAD
      <div v-if="withdrawType === 'relayer' && currency !== 'STORM'" class="withdraw-data-item">
        {{ $t('relayerFee') }}
        <span data-test="label_relayer_fee">1%</span>
        <!-- {{ toDecimals(relayerFee, null, 6) }} {{ currency }} -->
      </div>
      <div v-if="withdrawType === 'relayer' && currency !== 'STORM'" class="withdraw-data-item">
=======
      <div v-if="withdrawType === 'relayer'" class="withdraw-data-item">
        {{ $t('relayerFee') }}
        <span data-test="label_relayer_fee">2%</span>
        <!-- {{ toDecimals(relayerFee, null, 6) }} {{ currency }} -->
      </div>
      <div v-if="withdrawType === 'relayer'" class="withdraw-data-item">
>>>>>>> 95c13a08109fe71f23578455200c086c1e2cbcca
        {{ $t('totalFee') }}
        <span data-test="label_total_fee">2%</span>
        <!-- {{ toDecimals(totalRelayerFee, null, 6) }} {{ currency }} -->
      </div>
      <!-- <div v-if="isTokenSelected" class="withdraw-data-item">
        {{ $t('ethPurchase', { currency: networkCurrency }) }}
        <span>{{ toDecimals(ethToReceiveInToken, null, 6) }} {{ currency }}</span>
      </div> -->
      <hr v-if="withdrawType === 'relayer'" />
      <div class="withdraw-data-item">
        {{ $t('tokensToReceive') }}
        <span data-test="label_tokens_to_receive">{{ total }} {{ currency }} </span>
      </div>
      <!-- <div v-if="isTokenSelected" class="withdraw-data-item">
        <span class="is-alone">{{ ethToReceiveFromWei }} {{ networkCurrency }}</span>
      </div> -->
    </div>
  </div>
</template>
<script>
import { mapState, mapGetters } from 'vuex'
import { decimalPlaces } from '@/utils'
const { fromWei, toBN } = require('web3-utils')

export default {
  props: {
    currency: {
      type: String,
      default: 'ETH'
    },
    withdrawType: {
      type: String,
      default: 'wallet'
    },
    ethToReceive: {
      type: String,
      default: '20000000000000000'
    },
    serviceFee: {
      type: Number,
      default: null
    }
  },
  computed: {
    ...mapState('application', ['selectedStatistic']),
    ...mapGetters('metamask', ['networkConfig', 'nativeCurrency']),
    ...mapGetters('metamask', {
      networkCurrency: 'currency'
    }),
    ...mapGetters('gasPrices', ['gasPriceInGwei']),
    ...mapGetters('token', ['toDecimals', 'fromDecimals']),
    ...mapGetters('application', ['networkFee']),
    ...mapGetters('price', ['tokenRate']),
    relayerFee() {
      const { amount } = this.selectedStatistic
      const total = toBN(this.fromDecimals(amount.toString()))
      // const fee = this.serviceFee || this.$store.state.relayer.selectedRelayer.tornadoServiceFee
      const fee = 2
      const decimalsPoint = decimalPlaces(fee)
      const roundDecimal = 10 ** decimalsPoint
      const aroundFee = toBN(parseInt(fee * roundDecimal, 10))
      const tornadoServiceFee = total.mul(toBN(aroundFee)).div(toBN(roundDecimal * 100))
<<<<<<< HEAD
      console.log('debug-1', tornadoServiceFee, decimalsPoint, toBN(aroundFee), Number(total))
      console.log(tornadoServiceFee, this.gasPriceInGwei, aroundFee, roundDecimal, 'debug->tornadoServiceFee')
=======
>>>>>>> 95c13a08109fe71f23578455200c086c1e2cbcca
      return tornadoServiceFee
    },
    totalRelayerFee() {
      const tornadoServiceFee = this.relayerFee
      const { currency } = this.selectedStatistic
      // const { decimals } = this.networkConfig.tokens[currency]
<<<<<<< HEAD
      // const ethFee = this.networkFee
      if (currency === this.nativeCurrency) {
        return tornadoServiceFee
        // return ethFee.add(tornadoServiceFee)
=======
      const ethFee = this.networkFee
      if (currency === this.nativeCurrency) {
        return ethFee.add(tornadoServiceFee)
>>>>>>> 95c13a08109fe71f23578455200c086c1e2cbcca
      }
      // const tokenFee = ethFee.mul(toBN(10 ** decimals)).div(toBN(this.tokenRate))
      return tornadoServiceFee
      // return tokenFee.add(tornadoServiceFee)
    },
    isTokenSelected() {
      return (
        this.withdrawType === 'relayer' &&
        this.selectedStatistic.currency !== this.nativeCurrency &&
        this.currency !== 'TORN'
      )
    },
    ethToReceiveInToken() {
      const { currency } = this.selectedStatistic
      const { decimals } = this.networkConfig.tokens[currency]
      const price = this.tokenRate
      return toBN(this.ethToReceive)
        .mul(toBN(10 ** decimals))
        .div(toBN(price))
    },
    ethToReceiveFromWei() {
      return fromWei(this.ethToReceive)
    },
    total() {
      const { amount, currency } = this.selectedStatistic
      let total = toBN(this.fromDecimals(amount.toString()))

      if (this.withdrawType === 'relayer') {
        const relayerFee = this.totalRelayerFee
<<<<<<< HEAD
        if (currency === this.nativeCurrency) {
          total = total.sub(relayerFee)
        } else if (currency === 'storm') {
          total = total.sub(toBN(0))
=======

        if (currency === this.nativeCurrency) {
          total = total.sub(relayerFee)
>>>>>>> 95c13a08109fe71f23578455200c086c1e2cbcca
        } else {
          total = total.sub(relayerFee)
          // .sub(this.ethToReceiveInToken)
        }
      }

      return this.toDecimals(total, null, 6)
    }
  }
}
</script>
