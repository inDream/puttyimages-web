<template>
  <no-ssr>
    <div class="the-overlay-component">

      <div v-if="shouldShowWeb3Dialog">
        <div v-if="checkIsMobileClient()">
          <!-- Trust Dialog start (show when mobile) -->
          <v-dialog
            v-model="isDialogOpen"
            max-width="290"
            @keydown.esc="isDialogOpen = false"
          >
            <v-card>
              <v-card-title class="headline">
                Wallet App required
              </v-card-title>
            </v-card>
            <v-btn
              dark
              flat
              @click.native="isDialogOpen = false"
            >Close</v-btn>
          </v-dialog>
          <!-- Trust Dialog end -->
        </div>
        <div v-else>
          <!-- Chrome Dialog start (ask when user use browser like Safari) -->
          <v-dialog
            v-if="shouldShowChromeDialog"
            v-model="isDialogOpen"
            max-width="290"
            @keydown.esc="isDialogOpen = false"
          >
            <v-card>
              <v-card-title class="headline">
                Chrome required
              </v-card-title>
            </v-card>
            <v-btn
              dark
              flat
              @click.native="isDialogOpen = false"
            >Close</v-btn>
          </v-dialog>
          <!-- Chrome Dialog end -->

          <!-- Metamask related dialog start -->
          <the-metamask-dialog
            v-else-if="!!getWeb3Message"
            :getWeb3Message="getWeb3Message"
            :isDialogOpen.sync="isDialogOpen"
          />
          <!-- Metamask related dialog end -->
        </div>
      </div>

      <the-image-details-dialog />
    </div>
  </no-ssr>
</template>
<script>
import { mapGetters } from 'vuex';

import TheImageDetailsDialog from '@/components/TheImageDetailsDialog';
import TheMetamaskDialog from '@/components/TheMetamaskDialog';

import { checkIsMobileClient, checkIsDesktopChrome } from '@/util/client';

export default {
  name: 'the-overlay-component',
  components: {
    TheImageDetailsDialog,
    TheMetamaskDialog,
  },
  data() {
    return {
      isDialogOpen: false,
    };
  },
  computed: {
    ...mapGetters(['getWeb3Message', 'getWeb3Type']),
    shouldShowWeb3Dialog() {
      return !!this.getWeb3Message;
    },
    shouldShowChromeDialog() {
      return this.getWeb3Message === 'web3' && !checkIsDesktopChrome();
    },
  },
  watch: {
    getWeb3Message(message) {
      this.isDialogOpen = !!message;
    },
  },
  mounted() {
    this.isDialogOpen = !!this.getWeb3Message;
  },
  methods: {
    checkIsMobileClient,
  },
};
</script>
<style lang="scss" scoped>
@import '~assets/css/variables';

.overlay {
  position: relative;
  z-index: 999;
  top: 0px;

  width: 100%;
}
</style>
