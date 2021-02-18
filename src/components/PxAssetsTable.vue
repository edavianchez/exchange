<template>
  <div class="w-full overflow-auto bg-gray-100 rounded-md shadow">
    <table class="w-full">
      <thead>
        <tr class="bg-gray-100 border-b-2 border-gray-400">
          <th></th>
          <th :class="{ up: sortOrder === 1, down: sortOrder === -1 }">
            <span class="underline cursor-pointer" @click="changeSortOrder"
              >Ranking</span
            >
          </th>
          <th>Nombre</th>
          <th>Precio</th>
          <th>Cap. de Mercado</th>
          <th>Variación 24hs</th>
          <td class="hidden sm:block">
            <input
              type="text"
              class="bg-gray-100 focus:outline-none border-b border-gray-400 py-2 px-4 block w-full appearance-none leading-normal"
              id="filter"
              placeholder="Buscar..."
              v-model="filter"
            />
          </td>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="asset in filteredAssets"
          :key="asset.id"
          class="border-b border-gray-100 hover:bg-gray-100 hover:bg-orange-100"
        >
          <td>
            <img
              class="w-6 h-auto"
              :src="
                `https://static.coincap.io/assets/icons/${asset.symbol.toLowerCase()}@2x.png`
              "
              :alt="asset.name"
            />
          </td>
          <td>
            <b>#{{ asset.rank }}</b>
          </td>
          <td>
            <router-link
              class="hover:underline text-green-600"
              :to="{ name: 'coin-detail', params: { id: asset.id } }"
              >{{ asset.name }}
            </router-link>
            <small class="ml-1 text-gray-500">{{ asset.symbol }}</small>
          </td>
          <td>{{ asset.priceUsd | dollar }}</td>
          <td>{{ asset.marketCapUsd | dollar }}</td>
          <td
            :class="
              asset.changePercent24Hr < 0
                ? 'text-red-600 down'
                : 'text-green-600 up'
            "
          >
            {{ asset.changePercent24Hr | percent }}
          </td>
          <td class="hidden sm:block">
            <px-button @go="goToCoin(asset.id)">
              <span>Detalle</span>
            </px-button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import PxButton from "@/components/PxButton";

export default {
  name: "PxAssetsTable",
  components: { PxButton },
  props: {
    assets: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      filter: "",
      sortOrder: 1
    };
  },
  computed: {
    filteredAssets() {
      return this.assets
        .filter(
          asset =>
            asset.symbol.toLowerCase().includes(this.filter.toLowerCase()) ||
            asset.name.toLowerCase().includes(this.filter.toLowerCase())
        )
        .sort((a, b) => {
          if (parseInt(a.rank) > parseInt(b.rank)) {
            return this.sortOrder;
          }
          return this.sortOrder * -1;
        });
    }
  },
  methods: {
    goToCoin(id) {
      this.$router.push({ name: "coin-detail", params: { id } });
    },
    changeSortOrder() {
      this.sortOrder = this.sortOrder === 1 ? -1 : 1;
    }
  }
};
</script>

<style scoped>
.up::before {
  content: "👆";
}

.down::before {
  content: "👇";
}

td {
  padding: 20px 0px;
  font-size: 0.6rem;
  text-align: center;
}

th {
  padding: 5px;
  font-size: 0.6rem;
}

@media (min-width: 640px) {
  td,
  th {
    padding: 20px;
    font-size: 1rem;
  }

  th {
    padding: 12px;
  }
}
</style>
