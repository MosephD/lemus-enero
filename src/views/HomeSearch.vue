<template>
  <search-filters></search-filters>
  <div class="homesearch-main-container">
    <template v-if="isLoading">
      <loading :active="isLoading" :can-cancel="true" :on-cancel="onCancel" :is-full-page="fullPage"></loading>
    </template>
    <template v-else>
      <div class="map-container">
        <estates-map :markersData="propertys" @filtering="filteringPropertys"></estates-map>
      </div>
      <div class="home-cards-container">
        <PropertyCard v-for="property in propertys" :key="property.numPropiedad" :property="property" :class="{
          initialColor: initialCardState,
          filteredColor:
            property.numPropiedad != markerFilter && !initialCardState,
          unfilteredColor: property.numPropiedad == markerFilter,
        }" />
      </div>
    </template>
  </div>
</template>

<script>
import SearchFilters from "@/components/SearchFilters.vue";
import PropertyCard from "@/components/PropertyCard.vue";
import EstatesMap from "@/components/EstatesMap.vue";
import { usePropertiesData } from "@/composables/propertiesDataFetch.js";
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";
// import { usePropertysData } from "@/stores/PropertysStore.js";
// import { storeToRefs } from "pinia";

export default {
  name: "Homesearch",
  components: { SearchFilters, PropertyCard, EstatesMap, Loading },
  data() {
    return {
      markerFilter: "",
      initialCardState: true,
    };
  },
  setup() {
    // usePropertysData()
    const { propertys, isLoading, error } = usePropertiesData()
    // const { emit } = usePropertiesData()
    // emit('SinglePropertyData', propertys)
    // console.log(propertys);
    // const { propertys } = storeToRefs(usePropertysData());
    // console.log(propertys.propertys);
    return { propertys, isLoading, error }


  },
  methods: {
    filteringPropertys(markerUID) {
      this.markerFilter = markerUID;
      this.initialCardState = false;
      let propertysCollection = [];
      let target;
      this.propertys.forEach((propiedad, index, array) => {
        const targetEquals = propiedad.numPropiedad === markerUID;
        if (!targetEquals) propertysCollection.push(propiedad);
        else target = propiedad;
      });
      this.propertys = propertysCollection;
      this.propertys.unshift(target);
    },
    // mapSecondaryInfoWinOn() {
    //   console.log("cakin");
    //   // console.log(this.propertys[1]);
    // },
    // mapSecondaryInfoWinOff() {
    //   console.log("cacon");
    // },
  },
};
</script>

<style>
.homesearch-main-container {
  position: fixed;
  width: 100%;
  display: flex;
  height: calc(100% - 150px);
  top: 150px;
  background: white;
}

.map-container {
  width: 56%;
  background: #dddddd;
  display: flex;
  justify-content: center;
  align-items: center;
}

.home-cards-container {
  right: 0;
  width: 44%;
  height: 100%;
  overflow-y: auto;
  padding: 10px;
  overflow-y: scroll;
  z-index: 2;
}

.filteredColor {
  background-color: #93918e;
}

.unfilteredColor {
  background-color: none;
}

.initialColor {
  background-color: none;
}

.filteredColor h5 {
  color: #646464;
}

@media screen and (min-width: 1025px) {
  .map-container {
    width: 41.5%;
  }

  .home-cards-container {
    width: 58.5%;
  }
}

@media screen and (min-width: 1350px) {
  .map-container {
    width: 56%;
  }

  .home-cards-container {
    width: 44%;
  }
}

@media screen and (min-width: 1640px) {
  .map-container {
    width: 69.5%;
  }

  .home-cards-container {
    width: 31.5%;
  }
}
</style>