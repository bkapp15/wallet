<template>
  <MainLayout>
    <form @submit="createAsset">
      <h1>
        {{ $t("assetcreate.title") }}

        <button
          class="btn btn-light btn-xs"
          @click="advanced = true"
          v-if="!advanced"
        >
          {{ $t("assetcreate.show_advanced") }}
        </button>
        <button
          class="btn btn-light btn-xs"
          @click="advanced = false"
          v-if="advanced"
        >
          {{ $t("assetcreate.hide_advanced") }}
        </button>
      </h1>

      <label for="assetName" class="m-1">{{
        $t("assetcreate.assetName")
      }}</label>
      <input
        v-model="asset.assetName"
        id="assetName"
        class="form-control m-1"
      />
      <label for="addr " class="m-1">{{ $t("assetcreate.creator") }} </label>
      <div v-if="!hasPrivate" class="alert alert-danger">
        {{ $t("assetcreate.creator_not_found") }}
      </div>
      <select
        v-if="hasPrivate"
        class="select form-control m-1"
        v-model="asset.addr"
      >
        <option
          v-for="option in accountsWithPrivateKey"
          :key="option.addr"
          :value="option.addr"
        >
          {{ option.name }} - {{ option.addr }}
        </option>
      </select>
      <div class="form-check m-1" v-if="advanced">
        <input
          class="form-check-input"
          type="checkbox"
          v-model="asset.defaultFrozen"
          id="defaultFrozen"
        />
        <label class="form-check-label" for="defaultFrozen">
          {{ $t("assetcreate.default_fronzen") }}
        </label>
      </div>
      <label for="decimals" class="m-1" v-if="advanced"
        >{{ $t("assetcreate.decimals") }}
      </label>
      <input
        v-if="advanced"
        v-model="asset.decimals"
        id="decimals"
        class="form-control m-1"
        type="number"
        min="0"
        max="18"
        step="1"
      />
      <label for="totalIssuance" class="m-1"
        >{{ $t("assetcreate.totalIssuance") }} (<span title="min">{{
          min
        }}</span>
        - <span title="max">{{ max }}</span
        >)
      </label>
      <input
        v-model="asset.totalIssuance"
        id="totalIssuance"
        class="form-control m-1"
        type="number"
        min="0"
        max="1000000000000"
        step="1"
      />
      <label for="unitName" class="m-1" v-if="advanced"
        >{{ $t("assetcreate.unitName") }}
      </label>
      <input
        v-model="asset.unitName"
        id="unitName"
        class="form-control m-1"
        v-if="advanced"
      />
      <label for="assetURL" class="m-1" v-if="advanced"
        >{{ $t("assetcreate.assetURL") }}
      </label>
      <input
        v-model="asset.assetURL"
        id="assetURL"
        class="form-control m-1"
        v-if="advanced"
      />
      <label for="assetMetadataHash" class="m-1" v-if="advanced"
        >{{ $t("assetcreate.assetMetadataHash") }}
      </label>
      <input
        v-if="advanced"
        v-model="asset.assetMetadataHash"
        id="assetMetadataHash"
        class="form-control m-1"
      />

      <label for="manager " class="m-1" v-if="advanced"
        >{{ $t("assetcreate.manager") }}
      </label>
      <select
        class="select form-control m-1"
        v-model="asset.manager"
        v-if="advanced"
      >
        <option
          v-for="option in $store.state.wallet.privateAccounts"
          :key="option.addr"
          :value="option.addr"
        >
          {{ option.name }} - {{ option.addr }}
        </option>
      </select>
      <label for="reserve " class="m-1" v-if="advanced"
        >{{ $t("assetcreate.reserve") }}
      </label>
      <select
        class="select form-control m-1"
        v-model="asset.reserve"
        v-if="advanced"
      >
        <option
          v-for="option in $store.state.wallet.privateAccounts"
          :key="option.addr"
          :value="option.addr"
        >
          {{ option.name }} - {{ option.addr }}
        </option>
      </select>
      <label for="freeze " class="m-1" v-if="advanced"
        >{{ $t("assetcreate.freeze") }}
      </label>
      <select
        class="select form-control m-1"
        v-model="asset.freeze"
        v-if="advanced"
      >
        <option
          v-for="option in $store.state.wallet.privateAccounts"
          :key="option.addr"
          :value="option.addr"
        >
          {{ option.name }} - {{ option.addr }}
        </option>
      </select>
      <label for="clawback " class="m-1" v-if="advanced"
        >{{ $t("assetcreate.clawback") }}
      </label>
      <select
        class="select form-control m-1"
        v-model="asset.clawback"
        v-if="advanced"
      >
        <option
          v-for="option in $store.state.wallet.privateAccounts"
          :key="option.addr"
          :value="option.addr"
        >
          {{ option.name }} - {{ option.addr }}
        </option>
      </select>
      <label for="note" class="m-1" v-if="advanced"
        >{{ $t("assetcreate.note") }}
      </label>
      <input
        v-model="asset.note"
        id="note"
        class="form-control m-1"
        v-if="advanced"
      />
      <input
        type="submit"
        class="btn btn-primary"
        :value="$t('assetcreate.create_button')"
      />
    </form>
  </MainLayout>
</template>

<script>
import MainLayout from "../../layouts/Main.vue";
import { mapActions } from "vuex";
export default {
  components: {
    MainLayout,
  },
  data() {
    return {
      asset: {
        addr: "",
        note: "",
        totalIssuance: 1,
        decimals: 0,
        defaultFrozen: false,
        manager: "",
        reserve: "",
        freeze: "",
        clawback: "",
        unitName: "",
        assetName: "",
        assetURL: "",
        assetMetadataHash: "",
      },
      advanced: false,
    };
  },
  computed: {
    accountsWithPrivateKey() {
      return this.$store.state.wallet.privateAccounts.filter((a) => !!a.sk);
    },
    hasPrivate() {
      return (
        this.accountsWithPrivateKey && this.accountsWithPrivateKey.length > 0
      );
    },
    min() {
      if (this.asset.decimals == 0) {
        return 1;
      }
      return parseFloat(Math.pow(10, -1 * this.asset.decimals)).toFixed(
        this.asset.decimals
      );
    },
    max() {
      return new Intl.NumberFormat().format(this.asset.totalIssuance);
    },
  },
  mounted() {
    console.log("accountsWithPrivateKey", this.accountsWithPrivateKey);
  },
  methods: {
    ...mapActions({
      makeAssetCreateTxnWithSuggestedParams:
        "algod/makeAssetCreateTxnWithSuggestedParams",
      openSuccess: "toast/openSuccess",
    }),
    async createAsset(e) {
      e.preventDefault();
      console.log("asset", this.asset);
      const asset = await this.makeAssetCreateTxnWithSuggestedParams({
        asset: this.asset,
      });
      if (asset.txId) {
        this.openSuccess("Asset request sent to the network: " + asset.txId);
      }
    },
  },
};
</script>